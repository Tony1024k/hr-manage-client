<template>
  <el-form :model="loginForm" :rules="rules" class="login-container" ref="loginForm" label-position="left"
           label-width="0px" v-loading="loading" @keyup.enter.native="submitClick('loginForm')">
    <div style="float: right">
      <router-link :to="{ path: '/reg' }">注册</router-link>
    </div>
    <h3 class="login_title">系统登录</h3>
    <el-form-item prop="username">
      <el-input type="text" v-model="loginForm.username" auto-complete="off" placeholder="账号" autofocus></el-input>
    </el-form-item>
    <el-form-item prop="password">
      <el-input type="password" v-model="loginForm.password" auto-complete="off" placeholder="密码"></el-input>
    </el-form-item>
    <el-form-item prop="verifyCode">
      <el-col :span="8">
        <el-input type="text" v-model="loginForm.verifyCode" placeholder="验证码" auto-complete="off"></el-input>
      </el-col>
      <el-col :span="10" style="height: 43px">
        <el-link href="javascript:void(0);" :underline="false">
          <el-image style="width: 120px;height: 41px" :src="loginForm.verifyUrl" @click="getNewVerify()" ></el-image>
        </el-link>
      </el-col>
      <el-col :span="6">
        <el-checkbox class="login_remember" v-model="checked" label-position="left">记住密码</el-checkbox>
      </el-col>
    </el-form-item>
    <el-form-item style="width: 100%;margin-top: 50px">
      <el-button type="primary" style="width: 100%" @click="submitClick('loginForm')" >登录</el-button>
    </el-form-item>
  </el-form>
</template>
<script>
    export default {
        data() {
            return {
                rules: {
                    username: [{required: true, message: '请输入用户名', trigger: 'blur'}],
                    password: [{required: true, message: '请输入密码', trigger: 'blur'}],
                    verifyCode: [{required: true, message: '请输入验证码', trigger: 'blur'}]
                },
                checked: true,
                loginForm: {
                    username: 'admin',
                    password: '123',
                    verifyUrl: "/verify?",
                    verifyCode: ""
                },
                loading: false
            }
        },
        methods: {
            getNewVerify() {
                this.loginForm.verifyUrl += new Date().getTime(); // 给src赋一个新值，就会向新值的地址发送一次请求
            },
            submitClick(formName) {
                var _this = this;
                this.$refs[formName].validate((valid) => {
                    if (valid) {
                        this.loading = true;
                        this.postRequest('/login', {
                            username: this.loginForm.username,
                            password: this.loginForm.password,
                            verifyCode: this.loginForm.verifyCode
                        }).then(resp => {
                            _this.loading = false;
                            if (resp && resp.status === 200) {
                                var data = resp.data;
                                _this.$store.commit('login', data.obj);
                                var path = _this.$route.query.redirect;
                                _this.$router.replace({path: (path === '/' || path === undefined ? '/home' : path)});
                            }
                        });
                    } else {
                        console.log('error submit!!');
                        return false;
                    }
                })

            }
        }
    }
</script>
<style>
  .login-container {
    border-radius: 15px;
    background-clip: padding-box;
    margin: 150px auto;
    width: 350px;
    padding: 30px 40px 20px 40px;
    background: #fff;
    border: 1px solid #eaeaea;
    box-shadow: 0 0 25px #cac6c6;
  }

  .login_title {
    margin: 10px auto 40px auto;
    text-align: center;
    color: #505458;
  }

  .login_remember {
    text-align: left;
  }
</style>
