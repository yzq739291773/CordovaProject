<template>
  <div id="app">
    <img :src="imgsrc" width="64" height="64"><br/>
    
    <button @click="getposition(1)">获取位置</button>
    <span>{{msg}}</span><br/>

    <button @click="getimage()">显示本地图片</button>
    <button @click="getcode()">获取二维码</button><br/>
    <span>{{codeinfo}}</span>

    <!-- <router-view/> -->
  </div>
</template>

<script>
export default {
  name: 'App',
  data:function(){
    return {
      imgsrc:'./static/logo.png',
      msg:'cordova not init',
      codeinfo:''
    }
  },
  methods:{
    getcode()
    {
      var me = this;
      cordova.plugins.barcodeScanner.scan(
      function (result) {
          me.codeinfo="We got a barcode\n" +
                "Result: " + result.text + "\n" +
                "Format: " + result.format + "\n" +
                "Cancelled: " + result.cancelled;
      }, 
      function (error) {
          alert("Scanning failed: " + error);
      }
   );
    },
    getimage()
    {
      var me= this;
      navigator.camera.getPicture(onSuccess, onFail, { quality: 50,
          destinationType: Camera.DestinationType.FILE_URI,sourceType:0 });

      function onSuccess(imageURI) {          
          me.imgsrc = imageURI;
      }

      function onFail(message) {
          alert('Failed because: ' + message);
      }
    },
    getposition:function(val)
    {
      navigator.vibrate(500);
      var me = this;
       var onSuccess = function(position) {
        me.msg='Latitude:\t'          + position.coords.latitude          + '\n' +
              'Longitude:\t'         + position.coords.longitude         + '\n' +
              'Altitude:\t'          + position.coords.altitude          + '\n' +
              'Accuracy:\t'          + position.coords.accuracy          + '\n' +
              'Altitude Accuracy:\t' + position.coords.altitudeAccuracy  + '\n' +
              'Heading:\t'           + position.coords.heading           + '\n' +
              'Speed:\t'             + position.coords.speed             + '\n' +
              'Timestamp:\t'         + position.timestamp                + '\n';
    };

    // onError Callback receives a PositionError object
    //
    var error= function(error) {
        me.msg='code: '    + error.code    + '\n' +
              'message: ' + error.message + '\n';
    }

    navigator.geolocation.getCurrentPosition(onSuccess, error,{maximumAge: 30000, timeout: 30000, enableHighAccuracy: val});
    }
  },
  mounted(){
    document.addEventListener("deviceready", onDeviceReady, false);
    var me = this;
    function onDeviceReady() {
        me.msg="cordova is ready";
    }
  }
}
</script>

<style lang="less">
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
#nav {
  padding: 30px;
  a {
    font-weight: bold;
    color: #2c3e50;
    &.router-link-exact-active {
      color: #42b983;
    }
  }
}
</style>
