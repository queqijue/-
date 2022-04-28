<template>
  <div>
   <div class="header">
     <div class="header-title">请登录</div>
     <div class="header-info">Please Login Your Account</div>
   </div>
   <div class="body">
     <div class="login-from">
       <van-field placeholder="请输入用户名" :value="inputUserName" @change="onUserNameChange"/>
       <van-field placeholder="请输入密码" :value="inputPassWord" @change="onPassWordChange"/>
     </div>
     <van-button slot="button" round block color="#3d7ef9" @click="onClick" >登录</van-button>
     <div class="other-choice">
       <span>注册账户</span>
       <span style="margin:0 30px">|</span>
       <span>忘记密码</span>
     </div>
     <div class="copyright-wrapper">
       <span class="copyright">Prower by 家居卫士</span>
     </div>
   </div>
  </div>
</template>

<script>

export default {
  data () {
    return {
    inputUserName:"",
    inputPassWord:""
    }
  },

  methods:{
    onUserNameChange(event){
      var that=this
      that.$set(that,"inputUserName",event.mp.detail)
      console.log("当前输入用户名为"+event.mp.detail)
    },

    onPassWordChange(event){
      var that=this
      that.$set(that,"inputPassWord",event.mp.detail)
      console.log("当前输入密码为"+event.mp.detail)
    },

     onClick(event){
      var that=this
      console.log("被点击了")
    }
  }
}
</script>

<style lang="scss" scoped>
.header{
  height: 100px;
  padding: 25px 0;
  background-color: #3d7ef9;
  .header-title{
    font-size: 28px;
    font-weight: 500;
    color: #fff;
    margin-left: 20px;
  }
  .header-info{
    font-size: 14px;
    color: #fff;
    margin-left: 20px;
  }
}
.body{
  padding: 20px;
  .login-from{
    margin-bottom: 30px;
  }
  .other-choice{
    display: flex;
    justify-content: center;
    margin-top: 20px;
    color: #9f9f9f;
  }
  .copyright-wrapper{
    display: flex;
    justify-content: center;
    .copyright{
    color: #d6d6d6;
    position: fixed;
    bottom: 50px;
  }
  }
}
</style>
