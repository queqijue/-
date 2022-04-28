<template>
 <div class="wrapper">
  <div class="header-wrapper"> 
   <div class="header-title">
     <span>空气质量-{{aqi}}</span>
     <span>{{area}}-{{city}}</span>
   </div>
   <div class="header-text">
     <span>{{airValue}}</span>
     <span>{{weather}}</span>
   </div>
   <div class="weather-advice">
     <span>{{weatherAdvice}}</span>
   </div>
  </div> 
  <div class="bode-wrapper">
    <div class="body">
      <div class="data-wrapper">
        <div class="data">
          <img class="data-logo" src="/static/images/temp.png"/>
           <div class="data-text">
              <div class="data-title">温度</div>
              <div class="data-value">{{Temp}}℃</div>
           </div>
        </div>
        <div class="data">
          <img class="data-logo" src="/static/images/hum.png"/>
           <div class="data-text">
              <div class="data-title">湿度</div>
              <div class="data-value">{{Hum}}%</div>
           </div>
        </div>
      </div>
      <div class="data-wrapper">
        <div class="data">
          <img class="data-logo" src="/static/images/CO2.png"/>
           <div class="data-text">
              <div class="data-title">CO2浓度</div>
              <div class="data-value">{{CO2}}ppm</div>
           </div>
        </div>
        <div class="data">
          <img class="data-logo" src="/static/images/light.png"/>
           <div class="data-text">
              <div class="data-title">光照强度</div>
              <div class="data-value">{{Light}}lx</div>
           </div>
        </div>
      </div>
      <div class="data-wrapper">
        <div class="data">
          <img class="data-logo" src="/static/images/tovc.png"/>
           <div class="data-text">
              <div class="data-title">tovc浓度</div>
              <div class="data-value">{{TOVC}}ppm</div>
           </div>
        </div>
        <div class="data">
          <img class="data-logo" src="/static/images/led.png"/>
           <div class="data-text">
              <div class="data-title">led开关</div>
              <div class="data-value">
                <switch @change="onLedChange" :checked="Led" color="#3d7ef9"/>
              </div>
           </div>
        </div>
      </div>
    </div>
  </div>
 </div>
</template>

<script>
import {connect} from "mqtt/dist/mqtt.js"
const mqttUrl ='wxs://broker-cn.emqx.io:8084/mqtt'
export default {
  data () {
    return {
      client:null,
      Temp:0,
      Hum:0,
      Light:0,
      CO2:0,
      TOVC:0,
      Led:false,
      area:'请求中',//城区
      city:'请求中',//城市
      airValue:0,//空气质量
      weather:'请求中',//天气
      weatherAdvice:'请求中',//天气建议
      aqi:0//空气指数
    }
  },

  components: {
  },

  methods: {
    onLedChange(event){
      var that =this
      console.log(event.mp.detail)
      let sw=event.mp.detail.value
      that.Led=sw
      if (sw) {
        that.client.publish('/mqtttest/sub','{"target":"LED_SW","value":1}',function(err) {
          if (!err) {
            console.log("成功下发命令——开灯")
          }
        })
      }
      else{
        that.client.publish('/mqtttest/sub','{"target":"LED_SW","value":0}',function(err) {
          if (!err) {
            console.log("成功下发命令——关灯")
          }
        })
      }
    }
  },

  //created () {
    // let app = getApp()
  //}
onShow(){
   var that=this
   that.client=connect(mqttUrl);//输入mqtt服务器地址
   that.client.on('connect',function () {
     console.log("成功连接MQTT服务器！")
     that.client.subscribe("/mqtttest/pub",function(err) {
       if (err) {
         console.log("成功订阅设备上行数据Topic！")
       }
     })
   })
   that.client.on('message',function(topic,message) {
     console.log(topic)
    //console.log(message)//message是16进制字节流
     let dataFromDev={}
     dataFromDev =JSON.parse(message)
     console.log(dataFromDev)  
     that.Temp=dataFromDev.Temp
     that.Hum=dataFromDev.Hum
     that.CO2=dataFromDev.CO2
     that.TOVC=dataFromDev.TOVC 
     that.Light=dataFromDev.Light
     that.Led=dataFromDev.Led
     });
     wx.getLocation({
       type:"wgs84",
       success(res){
         const latitude=res.latitude;
         const longitude=res.longitude;
         const key='348bb2ca1af14e759b4d12bbd6f9a89a';
         wx.request({
           url: `https://free-api.heweather.net/s6/weather/now?location=${longitude},${latitude}&key=${key}`,//获取天气数据的API
           success(res){
           const {basic,now}=res.data.HeWeather6[0]
           that.area=basic.location
           that.city=basic.parent_city
           that.weather=now.cond_txt

           }
         });
          wx.request({
           url: `https://devapi.qweather.com/v7/air/now?location=${longitude},${latitude}&key=${key}`,//获取空气数据的API
           success(res){
           that.airValue=res.data.now.category
           that.aqi=res.data.now.aqi
           }
         });
         wx.request({
           url: `https://free-api.heweather.net/s6/weather/lifestyle?location=${longitude},${latitude}&key=${key}`,//获取advice的API
           success(res){
                const{basic,lifestyle}=res.data.HeWeather6[0]
                that.weatherAdvice=lifestyle[0].txt
           }
         });
       }
     })
}

}
</script>

<style lang="scss" scoped>
.wrapper{
  padding:15px;
  .header-wrapper{
    background-color: rgb(0, 132, 255);
    border-radius: 20px;
    color: white;
    box-shadow: #d6d6d6 0px 0px 5px;
    padding: 15px 30px;
    .header-title{
      display: flex;
      justify-content: space-between;
    }
    .header-text{
      font-size: 32px;
      font-weight: 400;
      display: flex;
      justify-content: space-between;
    }
    .weather-advice{
      margin-top: 20px;
      font-size: 12px;
    }
  }
  .data-wrapper{
    margin-top: 20px;
    display: flex;
    justify-content: space-between;
    .data{
      background-color: white;
      width: 150px;
      height: 80px;
      border-radius: 20px;
      display: flex;
      justify-content: space-around;
      padding: 0 8px;
      box-shadow: #d6d6d6 0px 0px 5px;
      .data-logo{
        height: 56px;
        width: 56px;
        margin-top: 15px;
      }
      .data-text{
        margin-top: 15px;
        color: #7f7f7f;
        .data-title{
          text-align: right;
        }
        .data-value{
          font-size: 26px;
        }
      }
    }
  }
}
</style>
