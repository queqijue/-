<template>
  <div>
   <div class="header">
     <div v-if="isLogin">
        <div class="header-title">请登录</div>
        <div class="header-info">Please Login Your Account</div>
     </div>
     <div v-else>
        <div class="header-title">请注册</div>
        <div class="header-info">Please Register Your Account</div>
     </div>
   </div>
   <div class="body">
     <div class="login-from">
       <van-field placeholder="请输入用户名" :value="inputUserName" @change="onUserNameChange"/>
       <van-field type="password" placeholder="请输入密码" :value="inputPassWord" @change="onPassWordChange"/>
       <div v-if="!isLogin">
         <van-field placeholder="请输入绑定手机号" :value="inputContect" @change="onContectChange"/>
       </div>
     </div>
     <van-button slot="button" round block color="#3d7ef9" @click="onClick" >{{isLogin?'登录':'注册'}}</van-button>
     <div class="other-choice">
       <span @click="onRes">{{isLogin?'注册账户':'登录账户'}}</span>
       <span style="margin:0 30px">|</span>
       <span>忘记密码</span>
     </div>
     <div class="copyright-wrapper">
       <span class="copyright">Prower by 家居卫士</span>
     </div>
     <van-toast id="van-toast" />
   </div>
  </div>
</template>

<script>
import Toast from "vant-weapp/dist/toast/toast"
export default {
  data () {
    return {
    isLogin:true,  
    inputUserName:"",
    inputPassWord:"",
    inPutContect:""
    }
  },

  methods:{
    onUserNameChange(event){
      var that=this
      that.$set(that,"inputUserName",event.mp.detail)
    },

    onPassWordChange(event){
      var that=this
      that.$set(that,"inputPassWord",event.mp.detail)
    },

     onClick(event){
      var that=this
      Toast.loading({
        duration:0,
        forbidClick:true,
        message:that.isLogin?'登录中':'注册中'
      })
      setTimeout(()=>{
        if (!that.isLogin) {
          wx.setStorage({
            key: "user",
            data:{
              username:that.inputUserName,
              password:that.inputPassWord,
              contect:that.inPutContect
            },
            success(res){
              console.log(res)
              Toast.success('注册成功')
            },
            fail(res){
              console.log(res)
              Toast.success('注册失败')
            }
          })
        }else{
          console.log('登录')
          wx.getStorage({
            key: "user",
            data:{
              username:that.inputUserName,
              password:that.inputPassWord,
              contect:that.inPutContect
            },
            success(res){
              console.log(res.data)
              if(that.inputUserName===res.data.username&&that.inputPassWord===res.data.password){
                Toast.success('登录成功') 
                setTimeout(()=>{
                  wx.switchTab({
                   url:"/pages/index/main"
                  })
                },500);
              }else{
                Toast.fail('用户名和密码错误')
              }
            },
            fail(res){
              console.log(res)
              Toast.fail("数据库暂无注册用户")
            }
          })
        }
      },1000)
    },

    onRes(event){
     var that=this
     console.log("helloworld")
     that.isLogin=!that.isLogin
     console.log(that.isLogin)
    },
     onContectChange(event){
      var that=this
      that.$set(that,"inputContect",event.mp.detail)
    },
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
