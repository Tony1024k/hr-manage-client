<template>
  <div>
    <el-form :model="regForm" status-icon :rules="rules" ref="regForm" label-width="100px" class="reg-container"
             v-loading="loading" @keyup.enter.native="submitForm('regForm')" >
      <div style="float: right">
        <router-link :to="{ path: '/' }">返回登录</router-link>
      </div>
      <h3 class="reg_title">用户注册</h3>
      <el-form-item label="用户名" prop="username" label-width="80px">
        <el-input v-model="regForm.username" autofocus></el-input>
      </el-form-item>
      <el-form-item label="密码" prop="password" label-width="80px">
        <el-input type="password" v-model="regForm.password" autocomplete="off"></el-input>
      </el-form-item>
      <el-form-item label="确认密码" prop="password2" label-width="80px">
        <el-input type="password" v-model="regForm.password2" autocomplete="off"></el-input>
      </el-form-item>
      <el-form-item style="margin-top: 50px;">
        <el-button type="primary" @click="submitForm('regForm')">提交</el-button>
        <el-button @click="resetForm('regForm')">重置</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
    export default {
        data() {

            var validatePass = (rule, value, callback) => {

                if (this.regForm.password2 !== '') {
                    this.$refs.regForm.validateField('password2');
                }
                callback();
            };
            var validatePass2 = (rule, value, callback) => {
                if (value !== this.regForm.password) {
                    callback(new Error('两次输入密码不一致!'));
                } else {
                    callback();
                }
            };
            return {
                regForm: {
                    username: "",
                    password: "",
                    password2: ""
                },

                loading: false,

                rules: {
                    username: [
                        {required: true, message: '请输入用户名', trigger: ['blur']},
                        {
                            pattern: /^[a-zA-Z0-9_-]{4,16}$/,
                            message: '用户名由4-16个字母数字下划线或减号组成',
                            trigger: ['blur', 'change']
                        }
                    ],
                    password: [
                        {required: true, message: '请输入密码', trigger: ['blur']},
                        {validator: validatePass, trigger: ['blur']},
                        {
                            pattern: /^(?=.*[a-z])(?=.*[A-Z])[a-zA-Z0-9~!@&%#_]{6,16}$/,
                            message: '密码长度为 6 到 16 个字符且必须包含大小写',
                            trigger: ['blur']
                        }

                    ],
                    password2: [
                        {required: true, message: '请再次输入密码', trigger: ['blur']},
                        {validator: validatePass2, trigger: ['blur']}
                    ]

                }
            }
        },
        methods: {
            submitForm(formName) {
                let _this = this;
                this.$refs[formName].validate((valid) => {
                    if (valid) {
                        this.loading = true;
                        this.postRequest("/reg", {username: this.regForm.username, password: this.regForm.password}).then(resp => {
                            _this.loading = false;
                            if (resp && resp.status === 200) {
                                _this.$router.replace({path: '/'});
                            }
                        })
                    } else {
                        console.log('error submit!!');
                        return false;
                    }
                });
            },
            resetForm(formName) {
                this.$refs[formName].resetFields();
            }
        }

    }
</script>

<style scoped>
  .reg-container {
    border-radius: 15px;
    background-clip: padding-box;
    margin: 150px auto;
    width: 350px;
    padding: 35px 35px 15px 35px;
    background: #fff;
    border: 1px solid #eaeaea;
    box-shadow: 0 0 25px #cac6c6;
  }

  .reg_title {
    margin: 0px auto 40px auto;
    text-align: center;
    color: #505458;
  }
</style>
