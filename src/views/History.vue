<template>
    <div class="main-container">
        <el-breadcrumb separator-class="el-icon-arrow-right">
            <el-breadcrumb-item :to="{ path: '/index' }">首页</el-breadcrumb-item>
            <el-breadcrumb-item>业务</el-breadcrumb-item>
            <el-breadcrumb-item>待办业务</el-breadcrumb-item>
            <el-breadcrumb-item>待办列表</el-breadcrumb-item>
        </el-breadcrumb>
        <div class="loginForm">
            <el-tabs type="card" v-model="activeName2" @tab-click="handleClick">
                <el-tab-pane label="已分配" name="first"></el-tab-pane>
                <el-tab-pane label="未分配" name="second"></el-tab-pane>
            </el-tabs>
            <el-tabs v-model="activeName">
                <el-tab-pane label="贷款信息" name="first">贷款信息</el-tab-pane>
                <el-tab-pane label="客户信息" name="second" @click="dialogFormVisible = true">
                    <el-dialog title="查询意图" :visible.sync="dialogFormVisible">
                        <el-form :model="form">
                            <el-form-item label="名称：" :label-width="formLabelWidth">
                                <el-input v-model="form.name" autocomplete="off"></el-input>
                            </el-form-item>

                            <el-form-item label="业务内容：" :label-width="formLabelWidth">
                                <el-select v-model="form.region" placeholder="贷前">
                                    <el-option label="贷前" value="shanghai"></el-option>
                                    <el-option label="贷后" value="beijing"></el-option>
                                </el-select>
                            </el-form-item>
                            <el-form-item label="" :label-width="formLabelWidth">
                                <el-select v-model="form.infoTax" placeholder="查询征税信息">
                                    <el-option label="查询征税信息" value="shanghai"></el-option>
                                    <el-option label="其它" value="beijing"></el-option>
                                </el-select>
                            </el-form-item>
                            <el-form-item label="备注" :label-width="formLabelWidth">
                                <el-input
                                        type="textarea"
                                        :rows="3"
                                        placeholder="请输入内容"
                                        v-model="text">
                                </el-input>
                            </el-form-item>
                        </el-form>
                        <div slot="footer" class="dialog-footer">
                            <el-button @click="dialogFormVisible = false">取 消</el-button>
                            <el-button type="primary" @click="handleClickDialog">确 定</el-button>
                        </div>
                    </el-dialog>


                    <div style="margin: 20px 0;"></div>
                    <div>
                        <el-button type="text" v-if="!seen" @click="dialogFormVisible = true"> 客户编号：{{id}}</el-button>
                        <br><br>

                        <div class="block">
                            <el-timeline>
                                <el-timeline-item
                                        v-for="(activity, index) in activities"
                                        :key="index"
                                        :icon="activity.icon"
                                        :type="activity.type"
                                        :color="activity.color"
                                        :size="activity.size"
                                        :timestamp="activity.timestamp">
                                    {{activity.content}}
                                </el-timeline-item>
                            </el-timeline>
                        </div>


                        <div style="width: 30%">...</div>
                    </div>
                </el-tab-pane>
                <el-tab-pane label="收费信息" name="third">收费信息</el-tab-pane>
                <el-tab-pane label="担保信息" name="fourth">担保信息</el-tab-pane>
                <el-tab-pane label="文件信息" name="fifth">文件信息</el-tab-pane>
                <el-tab-pane label="审批意见" name="sixth">审批意见</el-tab-pane>
            </el-tabs>
        </div>

        <!--        <div class="loginForm">-->
        <!--           -->
        <!--        </div>-->
    </div>
</template>

<script>
    export default {
        name: "history",
        data() {
            return {
                id: "A01P18111902211",
                activeName: 'second',
                activeName2: 'first',
                dialogFormVisible: false,
                seen: false,
                form: {
                    name: '12',
                    region: '贷前',
                    date1: '12',
                    date2: '12',
                    delivery: false,
                    type: [],
                    text: "",
                    resource: '12',
                    desc: '12',
                    infoTax: "查询征税信息"
                },
                formLabelWidth: '120px',
                activities: [{
                    content: '浏览历史',
                    // timestamp: '2018-04-12 20:46',
                    size: 'large',
                    type: 'primary',
                    icon: 'el-icon-more'
                }, {
                    content: '贷前-评估的风险',
                    timestamp: 'xxx（姓名） 2019-04-23 10：08',
                    size: "normal"
                    // type: "warning",
                    // color: '#0bbd87'
                }, {
                    content: '信息生成',
                    // timestamp: 'xxx（姓名） 2019-04-23 9：30',
                    // type: "warning",
                    size: "large",
                    type: "primary",
                    icon: 'el-icon-more'
                }, {
                    content: '信息审核',
                    // type: "dang",
                    timestamp: '2019-04-22 16:30',
                    size: 'normal'
                }, {
                    content: '信息提交',
                    // type: "dang",
                    timestamp: '2019-04-22 13:25',
                    size: 'normal'
                },]
            };
        },
        methods: {
            handleClickDialog() {
                this.seen = true;
                this.dialogFormVisible = false;
            },
            handleClick(tab, event) {
                if (activeName === "second") {
                    this.dialogFormVisible = true;
                    console.log(dialogFormVisible);
                } else {
                    this.dialogFormVisible = false;
                }
                // console.log(tab, event);
            },
            OKOK: function () {
                dialogFormVisible = false;
                seen = true;
            }
        }
    }
</script>

<style scoped>
    .block {
        margin-left: 1%;
    }

    .main-container {
        padding: 30px;
    }

    .loginForm {
        /*margin-top: 20px;*/
        /*margin-left: 20px;*/
        /*margin-right: 20px;*/
        height: 80%;
        background-color: #fff;
        padding: 20px 40px 20px 20px;
        border-radius: 5px;
        box-shadow: 0px 5px 10px #cccc;
    }
</style>
