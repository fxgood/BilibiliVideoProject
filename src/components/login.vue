<template>
  <div>
    手机号码<input placeholder="输入手机号码" v-model="phone"><br>
    密码<input placeholder="密码" v-model="password"><br>
    <a href="javascript:void(0)" @click="userRegister">注册</a>
  </div>
  <br>
  <div>
    手机号码<input placeholder="输入手机号码" v-model="phone"><br>
    邮箱<input placeholder="输入邮箱" v-model="email"><br>
    密码<input placeholder="密码" v-model="password"><br>
    <a href="javascript:void(0)" @click="userLogin">登录</a>
  </div>

</template>

<script>
import Axios from 'axios'
import {JSEncrypt} from "jsencrypt";

const jse = new JSEncrypt();
Axios.defaults.baseURL = '/api'
export default {  //vue component
  name: 'login',
  data() {
    return {
      phone: '',
      email: '',
      password: '',
      userToken:''
    }
  },
  methods: {
    getPublicKey() {
      Axios.get('/rsa-pks').then(function (response) {
        jse.setPublicKey(response.data.data)
        console.log(jse.getPublicKey())
      })
    },
    userRegister() {
      let that = this
      this.getPublicKey()
      Axios.post('/users', {
        phone: that.phone,
        password: jse.encrypt(that.password) //将加密后的密码发送给服务器
      }).then(function (response) {
        console.log(response)
      }, function (err) {
        console.log(err)
      })
    },
    userLogin() {
      let that = this
      axios.post('/user-tokens', {
        phone: that.phone,
        email: that.email,
        password:jse.encrypt(that.password)
      }).then(function (response){
        console.log(response)
      },function (err){
        console.log(err)
      })
    }
  }
}
</script>