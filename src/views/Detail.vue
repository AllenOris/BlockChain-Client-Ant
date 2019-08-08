<template>
    <div class="fill-contain">
        <div class="card-container">
            <el-breadcrumb separator-class="el-icon-arrow-right">
                <el-breadcrumb-item :to="{ path: '/index' }">首页</el-breadcrumb-item>
                <el-breadcrumb-item :to="{path:'/todo'}">业务</el-breadcrumb-item>
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
                    <el-button type="danger" @click="vis=true" style="float:right;" round>进行电核</el-button>
                </el-tab-pane>
                <el-tab-pane v-if="vis" label="电核信息" name="2">

                    <el-form ref="form" :model="form">
                        <el-form-item label="信息核对">
                            <br>
                            <el-checkbox-group v-model="form.checkList">
                                <div v-for="i in Math.ceil(check.length/2)" class="row">
                                    <el-checkbox class="col" :label="check[(i-1)*2].prop" border>
                                        {{check[(i-1)*2].name}}:
                                        {{data[check[(i-1)*2].prop]}}
                                    </el-checkbox>
                                    <el-checkbox class="col" v-if="(i-1)*2+1<check.length"
                                                 :label="check[(i-1)*2+1].prop" border>
                                        {{check[(i-1)*2+1].name}}: {{data[check[(i-1)*2+1].prop]}}
                                    </el-checkbox>
                                </div>
                            </el-checkbox-group>
                        </el-form-item>
                        <el-form-item label="选择标签">
                            <el-select v-model="form.value" placeholder="请选择信用评分">
                                <el-option
                                        v-for="item in options"
                                        :key="item.value"
                                        :label="item.label"
                                        :value="item.value">
                                </el-option>
                            </el-select>
                        </el-form-item>
                        <el-form-item label="备注">
                            <el-input
                                    type="textarea"
                                    :rows="4"
                                    placeholder="请输入内容"
                                    v-model="form.textarea">
                            </el-input>
                        </el-form-item>
                        <el-form-item>
                            <el-button type="danger" @click="handleSubmit" round>提交</el-button>
                            <el-button type="info" @click="handleCancel" round>退出</el-button>
                        </el-form-item>
                    </el-form>
                </el-tab-pane>
            </el-tabs>
        </div>


    </div>
</template>

<script>
    export default {
        name: "Detail",
        data() {
            return {
                activeName: "1",
                id: "",
                vis: false,
                data: {},
                tableData: [],
                check: [
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
                options: [{
                    value: '1',
                    label: '良好'
                }, {
                    value: '2',
                    label: '差'
                }],
                form: {
                    textarea: "",
                    value: '1',
                    checkList: [],
                }
            }
        },
        created() {
            this.id = this.$route.query.id;
            this.getProfile();
            console.log("data:", this.data);
        },
        methods: {
            handleSubmit() {
                // console.log(this.form.checkList);
                let tf = {};
                for (let i = 0; i < this.check.length; ++i) {
                    tf[this.check[i].prop] = this.form.checkList.indexOf(this.check[i].prop) !== -1 ? 'True' : 'False';
                }
                // console.log(tf);
                let fd = {};
                fd['note'] = this.form.textarea;
                fd['type'] = this.form.value;
                fd['order_id'] = this.id;
                fd['data'] = tf;
                // console.log("fd:", fd);
                this.$axios.post('/api/orders/evaluate', fd).then(res => {
                    // console.log("res:", res);
                    if (res.data.code * 1 === 0) {
                        this.$message({type: "success", message: "操作成功"});
                        this.sleep(1000).then(()=>{
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
                            {name: "业务编号", value: this.data.order_id},
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
