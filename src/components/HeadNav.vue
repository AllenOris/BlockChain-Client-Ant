<template>
    <div>
        <div class="header-nav">
            <el-menu :default-active="activeIndex" class="el-menu-demo" mode="horizontal" @select="handleSelect">
                <el-menu-item index='0'>
                    <!--                    <img src="../assets/logo.png" class='logo' alt="">-->
                    <span class='title'>数微云</span></el-menu-item>
                <el-menu-item style="float:left" index='1'>业务</el-menu-item>
                <el-menu-item style="float:left" index='2'>客户</el-menu-item>
                <el-menu-item style="float:left" index='3'>合作方</el-menu-item>
                <el-menu-item style="float:left" index='4'>产品</el-menu-item>
                <el-menu-item style="float:left" index='5'>风控</el-menu-item>
                <el-menu-item style="float:left" index='6'>报表</el-menu-item>
                <el-menu-item style="float:right" index=3>
                    <el-button type="primary" icon="el-icon-user-solid" @click="logout">退出登陆</el-button>
                </el-menu-item>
            </el-menu>
        </div>

        <div>
            <el-row class="menu_page">
                <el-col>
                    <el-menu
                            mode="vertical"
                            background-color="#324057"
                            text-color="#fff"
                            active-text-color="#409eff"
                            class="el-menu-vertical-demo"
                    >
                        <div v-if="permission===1">
                            <router-link v-for="menu in leftBarMenu" :to=menu.link>
                                <el-menu-item index=index>
                                    <i :class="menu.icon"></i>
                                    <span slot="title">{{menu.name}}</span>
                                </el-menu-item>
                            </router-link>
                        </div>
                        <div v-else>
                            <router-link v-for="menu in leftBarMenu2" :to=menu.link>
                                <el-menu-item index=index>
                                    <i :class="menu.icon"></i>
                                    <span slot="title">{{menu.name}}</span>
                                </el-menu-item>
                            </router-link>
                        </div>
                    </el-menu>
                </el-col>
            </el-row>
        </div>
    </div>
</template>
<script>
    export default {
        name: "head-nav",
        data() {
            return {
                activeIndex: '1',
                permission: 2, //用户类型
                userType: 1,
                leftBarMenu: [
                    {
                        name: "待办业务",
                        link: "history",
                        icon: "el-icon-time"
                    }, {
                        name: "贷款业务",
                        link: "404",
                        icon: "el-icon-suitcase"
                    }, {
                        name: '还款申请',
                        link: "404",
                        icon: "el-icon-document"
                    }, {
                        name: '放款支付',
                        link: "404",
                        icon:"el-icon-money"
                    }
                    , {
                        name: '被拒业务',
                        link: "404",
                        icon:"el-icon-remove-outline"
                    }
                ],
                leftBarMenu2: [
                    {
                        name: "待办业务",
                        link: "todo",
                        icon: "el-icon-time"
                    }, {
                        name: "已处理业务",
                        link: "done",
                        icon: "el-icon-suitcase"
                    }, {
                        name: "全部业务",
                        link: "404",
                        icon: "el-icon-document"
                    },
                ],
            };
        },
        created() {
            this.$axios.get("/api/users/type").then(res => {
                // console.log(res);
                if (res.data.code*1 === 0) {
                    this.permission = res.data.data.type * 1;
                    if(this.permission===1)this.$router.push('/history');
                    else this.$router.push('/todo');
                } else {
                    this.$message.error(res.data.message);
                }
            }, err => {
                this.$message.error(err.message);
            });
        },
        methods: {
            handleSelect(key, keyPath) {

            },
            logout() {
                this.$axios.get("/api/users/logout").then(res => {
                    if (res.data.code *1=== 0) {
                        this.$message({message: "退出成功", type: "success"});
                    } else {
                        this.$message.error(res.data.message);
                    }
                });
                this.$router.push('/login');
            }
        }
    };
</script>

<style scoped>
    .head-nav {
        width: 100%;
        height: 60px;
        min-width: 600px;
        padding: 5px;
        background: #324057;
        color: #fff;
        border-bottom: 1px solid #1f2d3d;
    }

    .logo-container {
        line-height: 60px;
        min-width: 20px;
    }

    .logo {
        height: 50px;
        width: 50px;
        margin-right: 5px;
        vertical-align: middle;
        display: inline-bl;
    }

    .title {
        vertical-align: middle;
        font-size: 22px;
        font-family: "Microsoft YaHei";
        letter-spacing: 3px;
    }

    .user {
        line-height: 60px;
        text-align: right;
        float: right;
        padding-right: 10px;
    }

    .avatar {
        width: 40px;
        height: 40px;
        border-radius: 50%;
        vertical-align: middle;
        display: inline-block;
    }

    .welcome {
        display: inline-block;
        width: auto;
        vertical-align: middle;
        padding: 0 5px;
    }

    .name {
        line-height: 20px;
        text-align: center;
        font-size: 14px;
    }

    .comename {
        font-size: 12px;
    }

    .avatarname {
        color: #409eff;
        font-weight: bolder;
    }

    .username {
        cursor: pointer;
        margin-right: 5px;
    }

    .el-dropdown {
        color: #fff;
    }

    .topbar-account {
        float: right;
        padding-right: 9pt;
        font-size: 9pt
    }

    .topbar-btn {
        color: #fff
    }

    .topbar-btn:hover {
        background-color: #58b7ff
    }

    .menu_page {
        position: fixed;
        top: 71px;
        left: 0;
        min-height: 100%;
        /*width: 10%;*/
        background-color: #324057;
        z-index: 99;
    }

    .el-menu {
        border: none;
    }

    .fa-margin {
        margin-right: 5px;
    }

    .el-menu-vertical-demo:not(.el-menu--collapse) {
        width: 180px;
        min-height: 400px;
    }

    .el-menu-vertical-demo {
        width: 35px;
    }

    .el-submenu .el-menu-item {
        min-width: 180px;
    }

    .hiddenDropdown,
    .hiddenDropname {
        display: none;
    }

    a {
        text-decoration: none;
    }
</style>
