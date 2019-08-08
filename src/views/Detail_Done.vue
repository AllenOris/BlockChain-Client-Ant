<template>
    <div class="fill-contain">
        <div class="card-container">
            <el-breadcrumb separator-class="el-icon-arrow-right">
                <el-breadcrumb-item :to="{ path: '/index' }">首页</el-breadcrumb-item>
                <el-breadcrumb-item :to="{path:'/done'}">业务</el-breadcrumb-item>
                <el-breadcrumb-item>合同申请流程</el-breadcrumb-item>
                <el-breadcrumb-item>起草合同</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div style="margin: 20px 0;"></div>
        <div class="card-container">
            <el-tabs v-model="activeName" @tab-click="handleClick">
                <el-tab-pane label="贷款信息" name="1">
                    <!--                    <div style="margin: 1px 0;"></div>-->
                    <el-table
                            :data="tableData"
                            stripe
                            style="width: 100%">
                        <el-table-column
                                prop="name"
                                label="Name"
                                sortable>
                        </el-table-column>
                        <el-table-column
                                prop="value"
                                label="Value"
                                sortable>
                        </el-table-column>
                    </el-table>
                    <div style="margin: 20px 0;"></div>
                </el-tab-pane>
                <el-tab-pane label="电核信息" name="2">
                    <el-table
                            :data="tableData2"
                            stripe
                            style="width: 100%">
                        <el-table-column
                                prop="name"
                                label="Name">
                        </el-table-column>
                        <el-table-column
                                prop="value"
                                label="Value"
                                sortable>
                        </el-table-column>
                    </el-table>

                    <div style="margin: 20px 0;">添加备注：</div>
                    <div style="margin: 20px 0;"></div>
                    <el-input
                            type="textarea"
                            :rows="4"
                            placeholder="请输入内容"
                            v-model="textarea"
                            style="width: 50%;">
                    </el-input>
                    <el-button style="margin-left: 20px" type="danger" @click="handleSubmit" round>保存</el-button>
                </el-tab-pane>
            </el-tabs>
        </div>
    </div>
</template>

<script>
    export default {
        name: "detailDone",
        data() {
            return {
                activeName: "1",
                id: "",
                vis: false,
                data: {},
                tableData: [
                    {name: "姓名", prop: "cusName"},
                    {name: "性别", prop: "cusSex"},
                    {name: "民族", prop: "cusRace"},
                    {name: "出生日期", prop: "cusBirth"},
                    {name: "身份证号", prop: "cusID"},
                    {name: "户籍地址", prop: "cusHold"},
                    {name: "收获地址", prop: "cusLocation"},
                    {name: "开户行", prop: "cusBank"},
                    {name: "银行卡号", prop: "cusBankID"},
                    {name: "银行预留手机号", prop: "cusBankPhone"},
                    {name: "手机号", prop: "cusPhone"},
                ],
                tableData2: {},
                options: [{
                    value: '1',
                    label: '良好'
                }, {
                    value: '2',
                    label: '差'
                }],
                textarea: "",
            }
        },
        created() {
            this.id = this.$route.query.id;
            this.getProfile();
            console.log("data:", this.data);
        },
        methods: {
            handleSubmit() {
                const fd = {note: this.textarea};
                if (this.textarea.length === 0) {
                    this.$message.error('empty note');
                    return;
                }
                this.$axios.post('/api/orders/evaluate', fd).then(res => {
                    if (res.data.code * 1 === 0) {
                        this.$message({type: "success", message: "操作成功"});
                        this.sleep(1000).then(() => {
                            this.handleCancel(); //code
                        });
                    } else {
                        this.$message.error(res.data.message);
                    }
                }, err => {
                    this.$message.error(err.message);
                });
            },
            handleCancel() {
                this.activeName = "1";
                this.vis = false;
            },
            handleClick(tab, event) {
                this.getProfile();
            },
            sleep(ms) {
                return new Promise(resolve =>
                    setTimeout(resolve, ms)
                )
            },
            getProfile() {
                let fd = new FormData();
                fd.append('order_id', this.id);
                this.$axios.post("/api/orders", fd).then(res => {
                    console.log("res:", res);
                    if (res.data.code * 1 === 0) {
                        this.data = res.data.data;
                        this.$message({message: "获取成功", type: "success"});
                        this.tableData = [
                            {name: "客户编号", value: this.data.clientID},
                            {name: "商户名称", value: this.data.comName},
                            {name: "还款方式", value: this.data.payType},
                            {name: "利率", value: this.data.interestRate},
                            {name: "收货地区", value: this.data.receiveAddress},
                            {name: "合同金额", value: this.data.amount},
                            {name: "合同期数", value: this.data.period},
                            {name: "购买物品", value: this.data.purchaseName},
                            {name: "购买种类", value: this.data.cat},
                            {name: "购买时间", value: this.data.purchaseTime},
                        ]
                    } else {
                        this.$message.error(res.data.message);
                    }
                }, err => {
                    this.$message.error(err.message);
                });

                this.$axios.post("/api/orders/evaluate/information", fd).then(res => {
                    if (res.data.code * 1 === 0) {
                        this.data = res.data.data;
                        this.$message({message: "获取成功", type: "success"});
                        this.tableData2 = [
                            {name: "姓名", value: this.data.order_id},
                            {name: "性别", value: this.data.cusName},
                            {name: "民族", value: this.data.cusRace},
                            {name: "出生日期", value: this.data.cusBirth},
                            {name: "身份证号", value: this.data.cusID},
                            {name: "户籍地址", value: this.data.cusHold},
                            {name: "收获地址", value: this.data.cusLocation},
                            {name: "银行卡号", value: this.data.cusBankID},
                            {name: "银行预留手机号", value: this.data.cusBankPhone},
                            {name: "手机号", value: this.data.cusPhone},
                            {name: "标签", value: this.data.tag},
                            {name: "备注", value: this.data.note},
                        ]
                    } else {
                        this.$message.error(res.data.message);
                    }
                }, err => {
                    this.$message.error(err.message);
                });
            }
        }
    }
</script>

<style scoped>
    .row {
        margin-top: 10px;
    }

    .col {
        width: 40%;
    }

    .text {
        font-size: 14px;
    }

    .item {
        padding: 18px 0;
    }

    .box-card {
        width: 480px;
    }

    .fill-contain {
        width: 100%;
        height: 100%;
        padding: 16px;
        box-sizing: border-box;
    }

    .card-container {
        margin: 20px;
    }
</style>
