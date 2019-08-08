<template>
    <div class="fillcontain">
        <div style="margin: 20px;">
            <el-breadcrumb separator-class="el-icon-arrow-right">
                <el-breadcrumb-item :to="{ path: '/index' }">首页</el-breadcrumb-item>
                <el-breadcrumb-item>业务</el-breadcrumb-item>
                <el-breadcrumb-item>待办业务</el-breadcrumb-item>
                <el-breadcrumb-item>待办列表</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div style="margin: 20px 0;"></div>
        <el-card style="margin: 20px;width: 100%;">
            <el-button @click="resetDateFilter">清除日期过滤器</el-button>
            <el-button @click="clearFilter">清除所有过滤器</el-button>
            <el-button @click="refresh">刷新</el-button>
            <div style="margin: 20px 0;"></div>
            <el-table v-if="row.length>0"
                      :data="tableData"
                      stripe
                      border
                      height="800"
                      style="width: 100%">
                <el-table-column v-for="item in row" :prop="item.prop" :label="item.label" sortable>
                </el-table-column>
                <el-table-column
                        fixed="right"
                        label="操作">
                    <template slot-scope="scope">
                        <el-button @click="handleCheck(scope.row)" type="text" size="small">查看详情</el-button>
                    </template>
                </el-table-column>
            </el-table>
        </el-card>
    </div>
</template>

<script>
    export default {
        name: "todo",
        data() {
            return {
                tableData: [],
                tags: [],
                isRouterAlive: true,
                row: [
                    {label: "业务编号", prop: "id"},
                    {label: "项目名称", prop: "proName"},
                    {label: "合作商名称", prop: "comName"},
                    {label: "合作金额", prop: "amount"},
                    {label: "合同期限", prop: "period"},
                    {label: "创建时间", prop: "createTime"},
                    {label: "物品种类", prop: "cat"},
                    {label: "利率", prop: "interestRate"},
                ]
            }
        },
        provide() { //提供
            return {
                reload: this.reload
            }
        },
        created() {
            this.getProfile();
        },
        methods: {
            handle(row) {
                this.download(row);
            },
            handleCheck(row) {
                this.$router.push({
                    path: '/detail',
                    query: {
                        id: row.id
                    }
                });
            },
            refresh() {
                this.getProfile();
            },
            getProfile() {
                let fd = new FormData();
                this.$axios.get("/api/orders/myself").then(res => {
                    console.log(res);
                    if (res.data.code * 1 === 0) {
                        this.tableData = [];
                        for (let i = 0; i < res.data.data.length; ++i) {
                            let ele = res.data.data[i];
                            this.tableData.push({
                                createTime: ele.createTime.replace('T', ' ').replace('.000Z', ''),
                                id: ele.id,
                                proName: ele.proName,
                                comName: ele.comName,
                                amount: (ele.amount * 1).toFixed(1),
                                period: ele.period,
                                cat: ele.cat,
                                interestRate: ele.interestRate,
                            });
                        }
                        this.$message({message: "更新成功", type: "success"});
                    } else {
                        this.$message.error(res.data.message);
                    }
                });
            },
            resetDateFilter() {
                this.$refs.filterTable.clearFilter('date');
            }
            ,
            clearFilter() {
                this.$refs.filterTable.clearFilter();
            }
            ,
            formatter(row, column) {
                return row.address;
            }
            ,
            filterTag(value, row) {
                return row.tag === value;
            }
            ,
            filterHandler(value, row, column) {
                const property = column['property'];
                return row[property] === value;
            }
        }
    }
</script>

<style scoped>
    .fillcontain {
        width: 100%;
        height: 100%;
        padding: 16px;
        box-sizing: border-box;
    }
</style>
