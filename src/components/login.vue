<template>
  <div class="login-modal" v-if="loginDialogFormVisible" v-loading.fullscreen="loading" element-loading-background="rgba(255, 255, 255, 0.8)">
    <div class="login-box">
      <i class="el-icon-close" @click="hideDialog"></i>
      <p>{{type === 'login' ? '登录' : '注册'}}</p>
      <div class="login-form">
        <span>用户名</span>
        <el-input
                placeholder="请输入用户名"
                v-model="username"
                clearable>
        </el-input>
      </div>
      <div class="login-form">
        <span>密码</span>
        <el-input
                placeholder="请输入密码"
                v-model="password"
                clearable
                show-password>
        </el-input>
      </div>
      <div class="login-form" v-if="type === 'register'">
      </div>
      <div class="buttons-wrap">
        <el-button type="primary" v-if="type === 'login'" @click="login">登录</el-button>
        <el-button type="primary" v-if="type === 'register'" @click="register">注册</el-button>
        <a @click.prevent="type = 'register'" v-if="type === 'login'">注册</a>
        <a @click.prevent="type = 'login'" v-if="type === 'register'">重新登录</a>
      </div>

    </div>

  </div>
</template>
<script>
  export default {
    name: 'Login',
    props: ['loginDialogFormVisible'],
    data () {
      return {
        baseUrl: 'http://39.100.99.94:3000',
        username: '',
        password: '',
        rePass: '',
        type: 'login',
        loading: false
      }
    },
    methods: {
      hideDialog() {
        this.$emit('hideDialog', 'login');
      },
      login() {
        if (!this.username || !this.password) {
          this.$message({
            showClose: true,
            message: '用户名或者密码不能为空',
            type: 'error'
          });
          return;
        }
        this.loading = true;
        let data = {username: this.username, password: this.password};
        let url = this.baseUrl + '/user/login';
        fetch(url, {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json',
          },
          body: JSON.stringify(data),
        })
                .then(res => res.json())
                .then(res => {
                  this.loading = false;
                  if (res.code == 0) {

                    this.$message({
                      showClose: true,
                      message: '登录成功',
                      type: 'success'
                    });
                    sessionStorage.setItem('username', this.username);
                    sessionStorage.setItem('userId', res.userId);
                    this.hideDialog();
                    this.$emit('successCallback', this.username);
                  } else {
                    this.$message({
                      showClose: true,
                      message: res.msg,
                      type: 'error'
                    });
                  }
                }).catch(err => {
          this.loading = false;
          this.$message({
            showClose: true,
            message: '登录失败',
            type: 'error'
          });
        })
      },
      register() {
        if (!this.username || !this.password) {
          this.$message({
            showClose: true,
            message: '用户名或者密码不能为空',
            type: 'error'
          });
          return;
        }

        this.loading = true;
        let data = {username: this.username, password: this.password};
        let url = this.baseUrl + '/user/register';
        fetch(url, {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json',
          },
          body: JSON.stringify(data), // "{"name":"Billy","age":18}"
        })
                .then(res => res.json())
                .then(res => {
                  this.loading = false;
                  if (res.code == 0) {
                    // 登录成功,隐藏登录框，返回首页中
                    this.$message({
                      showClose: true,
                      message: '注册成功',
                      type: 'success'
                    });
                    sessionStorage.setItem('username', this.username);
                    sessionStorage.setItem('userId', res.userId);
                    this.hideDialog();
                    this.$emit('successCallback', this.username);
                  }
                })

      }
    }
  }
</script>
<style lang="scss" scoped>
  .login-modal {
    background-color: rgba(0,0,0,.5);
    position: fixed;
    left: 0;
    top: 0;
    right: 0;
    bottom: 0;
    z-index: 9999;

    .login-box {
      width: 400px;
      background-color: #fff;
      position: absolute;
      left: 50%;
      top: 40%;
      transform: translate(-50%, -50%);
      padding: 20px 20px 40px;

      .el-icon-close {
        position: absolute;
        right: 20px;
        top: 20px;
        cursor: pointer;
      }

      & > p {
        text-align: center;
        font-size: 20px;
      }

      .login-form {
        display: flex;
        align-items: center;
        margin-bottom: 25px;

        & > span {
          width: 60px;
          font-size: 13px;
        }

        .el-input {
          flex: 1;
        }

        .el-icon-circle-check {
          color: #fff595;
        }

        .el-icon-circle-close {
          color: #ff0000;
        }
      }

      .buttons-wrap {
        display: flex;
        align-items: center;

        .el-button {
          flex: 1;
          margin-right: 20px;
          margin-left: 20px;
        }

        a {
          color: #000003;
          cursor: pointer;
          font-size: 13px;
        }
      }

    }
  }
</style>