<template>
  <div class="login-container">
    <mu-card class="login-card" raised>
      <mu-form ref="form" :model="validateForm" class="mu-demo-form">
        <mu-card-title id="login-title" title="奥利阳酒店"></mu-card-title>
        <mu-card-text>
          <mu-form-item label="用户名" prop="username" :rules="usernameRules">
            <mu-text-field v-model="validateForm.username" prop="username"></mu-text-field>
          </mu-form-item>
          <mu-form-item label="密码" prop="password" :rules="passwordRules">
            <mu-text-field type="password" v-model="validateForm.password" prop="password" @keyup.enter="submit"></mu-text-field>
          </mu-form-item>
          <mu-form-item>
            <mu-button id="login-button" flat>忘记密码？</mu-button>
            <mu-button id="login-button" flat @click="navigateTo('/register')">立即注册</mu-button>
          </mu-form-item>
        </mu-card-text>
        <mu-card-actions>
          <mu-form-item>
            <mu-button id="login-button" color="primary" @click="submit">登录</mu-button>
            <!--            <mu-button @click="navigateTo('/')">先随便逛逛</mu-button>-->
          </mu-form-item>
        </mu-card-actions>
      </mu-form>
    </mu-card>
  </div>
</template>

<script>
import {userLogin, getUserInfoByUsername} from "@/api/user";
import Cookies from 'js-cookie'

export default {
  name: "Login",
  data() {
    return {
      usernameRules: [
        {validate: (val) => !!val, message: '必须填写用户名'},
      ],
      passwordRules: [
        {validate: (val) => !!val, message: '必须填写密码'},
      ],
      validateForm: {
        username: '',
        password: ''
      },
      userId: null,
      btnLoading: false
    }
  },
  methods: {
    navigateTo(val) {
      this.$router.push(val);
    },
    submit() {
      this.$refs.form.validate().then((result) => {
        if (result == false) {
          this.$toast.warning("请填入账户密码！")
          return
        } else {
          this.login()
        }
      });
    },
    clear() {
      this.$refs.form.clear();
      this.validateForm = {
        username: '',
        password: ''
      };
    },
    login() {
      userLogin(this.validateForm.username, this.validateForm.password).then(res => {
        var response = res;
        if (response.data !== null) {
          Cookies.set('username', this.validateForm.username)
          Cookies.set('session', response.data.sessionId)
          Cookies.set('user_id', response.data.userId)
          this.navigateTo('/')
        } else if (response.code !== 1000) {
          this.$toast.error(response.message)
        }
      }).catch(err => {
        console.log(err)
      })
    },
  }
}
</script>

<style scoped>
.login-container {
  background: url("../assets/imgs/login.jpg") no-repeat;
  height: 100vh;
  width: 100%;
  overflow: hidden;
  background-size: cover; //或者background-size:100%;
}

.mu-demo-form {
  background: aliceblue;
}

#login-title {
  text-align: center;
  font-weight: bold;
  font-family: Serif;
  font-size: large;
}

#login-button {
  margin: auto;
  display: block;
}

.login-card {
  width: 100%;
  max-width: 30%;
  max-height: 100%;
  margin: 120px auto;
}
</style>
