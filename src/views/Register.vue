<template>
    <div class="register">
        <section class="form_container">
            <div class="manage_tip">
                <span class="title">区块链借贷系统</span>
            </div>
            <el-form :model="registerUser" :rules="rules" class="registerForm" ref="registerForm" label-width="80px">
                <el-form-item label="用户名" prop="name">
                    <el-input v-model="registerUser.name" placeholder="请输入用户名"></el-input>
                </el-form-item>
                <el-form-item label="密码" prop="password">
                    <el-input v-model="registerUser.password" placeholder="请输入密码" type="password"></el-input>
                </el-form-item>
                <el-form-item label="确认密码" prop="password2">
                    <el-input v-model="registerUser.password2" placeholder="请确认密码" type="password"></el-input>
                </el-form-item>
                <el-form-item label="身份类型" prop="identity">
                    <el-select v-model="registerUser.identity" placeholder="请选择身份">
                        <el-option
                                v-for="item in options"
                                :key="item.value"
                                :label="item.label"
                                :value="item.value">
                        </el-option>
                    </el-select>
                </el-form-item>
                <el-form-item>
                    <el-button type="primary" class="submit_btn" @click="submitForm('registerForm')">注 册</el-button>
                </el-form-item>
                <div class="tiparea">
                    <p>有账号？立即
                        <router-link to='/login'>登陆</router-link>
                    </p>
                </div>
            </el-form>
        </section>
    </div>
</template>

<script>
    export default {
        name: "register",
        data() {
            var validatePass2 = (rule, value, callback) => {
                if (value !== this.registerUser.password) {
                    callback(new Error("两次输入密码不一致!"));
                } else {
                    callback();
                }
            };
            return {
                registerUser: {
                    name: "",
                    password: "",
                    identity: "",
                },
                options: [
                    {key: "1", value: "资金方"},
                    {key: "2", value: "第三方"},
                    {key: "3", value: "贷款用户"},
                ],
                rules: {
                    name: [
                        {required: true, message: "用户名不能为空", trigger: "change"},
                        {min: 1, max: 30, message: "长度在 1 到 30 个字符", trigger: "blur"}
                    ],
                    password: [
                        {required: true, message: "密码不能为空", trigger: "blur"},
                        {min: 4, max: 30, message: "长度在 4 到 30 个字符", trigger: "blur"}
                    ],
                    password2: [
                        {required: true, message: "确认密码不能为空", trigger: "blur"},
                        {
                            min: 4, max: 30, message: "长度在 4 到 30 个字符", trigger: "blur"
                        },
                        {validator: validatePass2, trigger: "blur"}
                    ],
                    identity: [
                        {required: true, message: '请选择活动区域', trigger: 'change'}
                    ]
                }
            };
        },
        methods: {
            submitForm(formName) {
                this.$refs[formName].validate(valid => {
                    if (valid) {
                        this.$axios
                            .post("/api/register", this.registerUser)
                            .then(res => {
                                // 注册成功
                                //console.log(res);
                                if (res.data.code * 1 === 0) {
                                    this.$message({
                                        message: "注册成功！",
                                        type: "success"
                                    });
                                    this.$router.push("/login");
                                } else {
                                    this.$message.error(res.data.message);
                                }
                            });
                    } else {
                        console.log("error submit!!");
                        return false;
                    }
                });
            }
        }
    };
</script>


<style scoped>
    .register {
        position: relative;
        width: 100%;
        height: 100%;
        background: url(../assets/bg2.jpg) no-repeat center center;
        background-size: 100% 100%;
    }

    .form_container {
        width: 370px;
        height: 210px;
        position: absolute;
        top: 10%;
        left: 34%;
        padding: 25px;
        border-radius: 5px;
        text-align: center;
    }

    .form_container .manage_tip .title {
        font-family: "Microsoft YaHei";
        font-weight: bold;
        font-size: 26px;
        color: #fff;
    }

    .registerForm {
        margin-top: 20px;
        background-color: #fff;
        padding: 20px 40px 20px 20px;
        border-radius: 5px;
        box-shadow: 0px 5px 10px #cccc;
    }

    .submit_btn {
        width: 100%;
    }

    .tiparea {
        text-align: right;
        font-size: 12px;
        color: #333;
    }

    .tiparea p a {
        color: #409eff;
    }
</style>



