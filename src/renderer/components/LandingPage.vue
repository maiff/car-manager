<template>
  <div id="wrapper">
    <!-- <img id="logo" src="~@/assets/logo.png" alt="electron-vue"> -->
    <h1 class="htitle" @click="setRange()">变电站接地线智能管理装置</h1>
    <!-- <div>
      35kV:<input type="text" v-model="min35"/> <input type="text" v-model="max35"/> <br>
      220kV:<input type="text" /> <input type="text" /> <br>
      500kV:<input type="text" /> <input type="text" /> <br>
    </div> -->
    <main>
      <div class="left-side">
        <span class="title">
          {{time}}
        </span>
        <system-information :status="status" :distance="distance"
          :lat="lat" :lng="lng"
        ></system-information>
        <!-- <button>设置范围</button> -->
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

  const { remote } = require('electron');
  import axios from 'axios'
  import fs from 'fs'
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
        i: 0,
        file: '',
        minlat35: 30.83841,
        maxlat35: 30.83905,
        minlng35: 108.286491,
        maxlng35: 108.28908,

        minlat220: 30.837,
        maxlat220: 30.83839,
        minlng220: 108.28651,
        maxlng220: 108.28931,

        minlat500: 30.83906 ,
        maxlat500: 30.84117,
        minlng500: 108.28612,
        maxlng500: 108.28965,

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
      // let file = process.cwd() + '/config.json' // 文件路径
      // // console.log(file)
      // this.file = file
      // fs.readFile(file, 'utf-8', function (err, data) {
      //   if (err) {
      //     console.log(err)// eslint-disable-line
      //   } else {
      //     console.log(data)// eslint-disable-line
      //   }
      // })
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
          // console.log(body);
        });

      },
    async setRange(){
      const result = await remote.dialog.showOpenDialog({
        properties: ['openFile'],
      });
      console.log(result)
      let file = result[0]
      fs.readFile(file, 'utf-8',  (err, data) => {
        if (err) {
          console.log(err)// eslint-disable-line
        } else {
          console.log(data)// eslint-disable-line
          let range = JSON.parse(data)
          this.minlat35= range.minlat35
          this.maxlat35= range.maxlat35
          this.minlng35= range.minlng35
          this.maxlng35= range.maxlng35

          this.minlat220= range.minlat220
          this.maxlat220= range.maxlat220
          this.minlng220= range.minlng220
          this.maxlng220= range.maxlng220

          this.minlat500= range.minlat500
          this.maxlat500= range.maxlat500
          this.minlng500= range.minlng500
          this.maxlng500= range.maxlng500
        }
      })

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
        // console.log(info, typeof(info));
        info = info[0]
        this.status = info['status']
        this.distance = info['distance']
        this.lat = parseFloat(info['baiduLat'])
        this.lng = parseFloat(info['baiduLng'])
        // console.log(this.lat, this.lng, 30.83941 <this.lat )
        if (this.minlat500 <= this.lat && this.lat <=this.maxlat500 && this.minlng500 <= this.lng && this.lng <= this.maxlng500){
          this.i = 3
        } else if(this.minlat35 <= this.lat && this.lat <= this.maxlat35 && this.minlng35 <= this.lng && this.lng <= this.maxlng35){
          this.i = 2
        }else if(this.minlat220 <= this.lat && this.lat <= this.maxlat220 && this.minlng220 <= this.lng && this.lng <= this.maxlng220){
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
  .htitle{
    cursor: pointer;
  }
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

  button {
    font-size: .8em;
    cursor: pointer;
    outline: none;
    padding: 0.75em 1.5em;
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
