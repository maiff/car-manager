<template>
  <div id="wrapper">
    <!-- <img id="logo" src="~@/assets/logo.png" alt="electron-vue"> -->
    <h1>变电站接地线智能管理装置</h1>
    <main>
      <div class="left-side">
        <span class="title">
          {{time}}
        </span>
        <system-information :status="status" :distance="distance"
          :lat="lat" :lng="lng"
        ></system-information>
      </div>

      <div class="right-side">
          <div class="item-c">220kV

            <div v-if="i==1">★</div>
          </div>
          <div class="item-c">35kV
            <div v-if="i==2">★</div>
          </div>
          <div class="item-c">500kV
            <div v-if="i==3">★</div>
          </div>

      </div>
    </main>
  </div>
</template>

<script>
  import SystemInformation from './LandingPage/SystemInformation'
  import axios from 'axios'
  var request = require('request');
  var request = request.defaults({jar: true})
  export default {
    name: 'landing-page',
    components: { SystemInformation },
    data(){
      return {
        time: '00-00-00 00:00',
        status: '',
        distance: '',
        lat: '',
        lng: '',
        i: 0
      }
    },
    mounted(){
      this.login(()=>{
        this.get_location()
      })
      setInterval(()=> {
        var myDate = new Date()
        this.time = myDate.toLocaleString()
      },1000)
      setInterval(()=> {
        this.get_location()

      },10000)
    },
    methods: {
      open (link) {
        this.$electron.shell.openExternal(link)
      },
      login(fn){
        var options = { method: 'POST',
          url: 'http://www.gps902.net/UrlLogin.aspx',
          headers: 
          { 'content-type': 'application/x-www-form-urlencoded',
            'postman-token': '45685430-81bf-6532-53da-fd47154f2823',
            'cache-control': 'no-cache' },
          form: 
          { txtImeiCarNo: '8030162313',
            txtImeiPassword: '123456',
            chkUserIMEI: '0',
            loginType: '1' } };

        request(options, function (error, response, body) {
          if (error) throw new Error(error);
          fn && fn()
          console.log(body);
        });

      },
    get_location(){
        var options = { method: 'POST',
        url: 'http://www.gps902.net/Ajax/DevicesAjax.asmx/GetDevicesByUserID',
        headers: 
        { 'postman-token': '95d0e2df-7a71-444c-9d87-a6c1e1fa3c05',
          'cache-control': 'no-cache',
          'content-type': 'application/json' },
        body: '{UserID:12531,isFirst:false,TimeZones:\'China Standard Time\',DeviceID:2075512}' };

      request(options,  (error, response, body) => {
        if (error) throw new Error(error);
        var info = JSON.parse(body)
        info = eval(info['d'])
        console.log(info, typeof(info));
        info = info[0]
        this.status = info['status']
        this.distance = info['distance']
        this.lat = parseFloat(info['baiduLat'])
        this.lng = parseFloat(info['baiduLng'])
        console.log(this.lat, this.lng, 30.83941 <this.lat )
        if (30.83941 < this.lat && this.lat <30.84117 && 108.28612 < this.lng && this.lng < 108.28965){
          this.i = 3
        } else if(30.83910 < this.lat && this.lat < 30.83937 && 108.286491 < this.lng && this.lng < 108.28908){
          this.i = 2
        }else if(30.83842 < this.lat && this.lat < 30.83906 && 108.28651 < this.lng && this.lng < 108.28931){
          this.i = 1
        } else {
          this.i = 0 
        }
      });
    }
    }
  }
</script>

<style>
  @import url('https://fonts.googleapis.com/css?family=Source+Sans+Pro');

  * {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
  }

  body { font-family: 'Source Sans Pro', sans-serif; }

  #wrapper {
    background:
      radial-gradient(
        ellipse at top left,
        rgba(255, 255, 255, 1) 40%,
        rgba(229, 229, 229, .9) 100%
      );
    height: 100vh;
    padding: 60px 80px;
    width: 100vw;
  }

  #logo {
    height: auto;
    margin-bottom: 20px;
    width: 420px;
  }

  main {
    display: flex;
    justify-content: space-between;
  }

  main > div { flex-basis: 50%; }

  .left-side {
    display: flex;
    flex-direction: column;
  }

  .welcome {
    color: #555;
    font-size: 23px;
    margin-bottom: 10px;
  }

  .title {
    color: #2c3e50;
    font-size: 20px;
    font-weight: bold;
    margin-bottom: 6px;
  }

  .title.alt {
    font-size: 18px;
    margin-bottom: 10px;
  }

  .doc p {
    color: black;
    margin-bottom: 10px;
  }

  .doc button {
    font-size: .8em;
    cursor: pointer;
    outline: none;
    padding: 0.75em 2em;
    border-radius: 2em;
    display: inline-block;
    color: #fff;
    background-color: #4fc08d;
    transition: all 0.15s ease;
    box-sizing: border-box;
    border: 1px solid #4fc08d;
  }

  .doc button.alt {
    color: #42b983;
    background-color: transparent;
  }
  .right-side{
    margin-top: 40px;
    display: flex;
    justify-content: space-around;
  }
  .item-c {
    border: #42b983 3px solid;
    border-radius: 5px;
    height: 150px;
    width: 150px;
  }
</style>
