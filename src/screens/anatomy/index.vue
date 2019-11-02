<template>
  <!--<view class="container">
    <camera class="container" :type="myType"/>
  </view>-->

  <nb-container >
    <nb-header >
      <nb-left>
        <nb-button
                transparent
                :onPress="() => this.props.navigation.openDrawer()"
        >
          <nb-icon name="menu" />
        </nb-button>
      </nb-left>
      <nb-body>
        <nb-title>Header</nb-title>
      </nb-body>
      <nb-right />
    </nb-header>
<!--    <nb-text >От: {{searchP1}}, До:{{searchP2}}</nb-text>-->
    <nb-header searchBar rounded :style="{height: 20,paddingTop:-25}">
      <nb-text>
        A:
      </nb-text>
      <nb-item>
        <nb-icon active name="search" />
        <nb-input placeholder="Search" v-model="searchP1"/>
        <nb-icon active name="people" />
      </nb-item>

    </nb-header >
    <nb-header searchBar rounded :style="{height: 20,paddingTop:-25}">
      <nb-text>
        B:
      </nb-text>
      <nb-item>
        <nb-icon active name="search" />
        <nb-input placeholder="Search" v-model="searchP2"/>
        <nb-icon active name="people" />
      </nb-item>

    </nb-header>

      <view  class="container">
        <map-view class="container"
                  :initial-region="coordinates"
                  :onPress="(event)=>{
                  clickOnMap(event);}"
        >
          <polyline v-for="(polyline,i) in polylines" :key="i" :coordinates="polyline"
                    strokeColor="#000"
                    :strokeWidth="6"
                    :strokeColors="arr"
          >

          </polyline>

          <map-marker v-for="(marker,i) in markers" :key="i" :coordinate="marker" pointerEvents="auto"
                      :onPress="(event)=>{event.stopPropagation()}">

            <!--<callout >
              <Text>
                12333</Text>
            </callout>-->
            <callout :style="{ marginBottom: 50}" :onPress="(event)=>{clickOnMarker(event,i);}">
              <view>

                  <nb-text>Удалить?</nb-text>

              </view>
            </callout>
            <!--<callout >

                <view>
                  <Text>
                    12313
                  </Text>
                  &lt;!&ndash;<nb-text :onPress="(event)=>clickOnMarker(event,i)"> 1231231</nb-text>&ndash;&gt;
                </view>

            </callout>-->
          </map-marker>
<!--          <callout :style="{ marginBottom: 50}">-->
<!--            <view>-->
<!--              <nb-button success  >-->
<!--                <nb-text>Success</nb-text>-->
<!--              </nb-button>-->

<!--            </view>-->
<!--          </callout>-->
        </map-view>



      </view>

    <nb-footer :style="{height:20}" v-if="superFlag">
      <nb-footer-tab>
        <nb-button rounded large info :style="superStyle3"  :onPress="(event)=>{superFlag=!superFlag}">
          <nb-text :style="superStyle">Я пассажир</nb-text>
        </nb-button>
        <nb-button light rounded large  :style="superStyle31"  :onPress="(event)=>{superFlag=!superFlag}">
          <nb-text :style="superStyle">Я водитель</nb-text>
        </nb-button>
      </nb-footer-tab>
    </nb-footer>
    <nb-footer :style="{height:20}" v-else>
      <nb-footer-tab>
        <nb-button light rounded large  :style="superStyle31"  :onPress="(event)=>{superFlag=!superFlag}">
          <nb-text :style="superStyle">Я пассажир</nb-text>
        </nb-button>
        <nb-button  rounded large info  :style="superStyle3"  :onPress="(event)=>{superFlag=!superFlag}">
          <nb-text :style="superStyle">Я водитель</nb-text>
        </nb-button>
      </nb-footer-tab>
    </nb-footer>
    <nb-footer :style="{height:20}">
      <nb-footer-tab>
        <nb-button v-if="disable" rounded large success :style="superStyle2"  :onPress="(event)=>{createReq(event);}">
          <nb-text :style="superStyle">{{footerText}}</nb-text>
        </nb-button>
        <nb-button v-else disabled rounded large  :style="superStyle2"  :onPress="(event)=>{createReq(event);}">
          <nb-text :style="superStyle">{{footerText}}</nb-text>
        </nb-button>



      </nb-footer-tab>


    </nb-footer>
  </nb-container>
</template>
<script>
  import MapView from "react-native-maps";
  import { Toast } from "native-base";

  import axios from 'axios'


  //import DeviceInfo from 'react-native-device-info';
  //import QRCodeScanner from 'react-native-qrcode-scanner';
  export default {
    data: function() {
      return {
        hasCameraPermission : false,
        coordinates: {
          longitude: 65.52667,
          latitude: 57.149963,
          latitudeDelta: 0.0922,
          longitudeDelta: 0.0421,
        },
        superStyle:
                {
                  color: '#fff',
                  marginBottom: 0,
                  fontSize: 15,

                },
        superStyle2:
                {
                  color: '#fff',
                  marginBottom: 50,
                  fontSize: 15,
                  margin:3
                },
        superStyle3:
                {
                  //backgroundColor:'#b5b5b5',
                  color: '#fff',
                  marginBottom: 75,
                  margin:3,
                  fontSize: 15
                },
        superStyle31:
                {
                  backgroundColor:'#b5b5b5',
                  color: '#fff',
                  marginBottom: 75,
                  margin:3,
                  fontSize: 15
                },
        superFlag: false,
        arr:[
          '#7F0000',
          '#00000000', // no color, creates a "long" gradient between the previous and next coordinate
          '#B24112',
          '#E5845C',
          '#238C23',
          '#7F0000'
        ],
        markers: [],
        polylines:[],
        searchP1:'',
        searchP2:'',
        uuidReq: '',
        pathDrawed: false,
        footerText: "Укажите маршрут"
      };
    },
    created(){
      this.uuidReq = this.uuidv4()
    },
    computed:{
      disable(){
        return this.markers.length>1
      }
    },
    methods:{
      uuidv4() {
        return 'xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx'.replace(/[xy]/g, function(c) {
          var r = Math.random() * 16 | 0, v = c == 'x' ? r : (r & 0x3 | 0x8);
          return v.toString(16);
        });
      },
      removeMoney(cost){
        axios.post('http://192.168.43.7:8080/coin/buy/money',{
          moneyAmount: cost * -1,
          user: global.username
        }).then(res=>{
          if(res.data && res.data.balance) {
            global.coins = res.data.balance
            Toast.show({
              text:"Литры списаны"
            })
          }
        })
      },
      createReq(event){
        if(this.markers.length < 2){
          Toast.show({
            text:"Выберите откуда и куда ехать"
          })
          return
        }
        event.stopPropagation()
        // если маршрут уже построен то начинаем движение к точке сбора
        // исписываем деньги
        if(this.pathDrawed && !global.driver) {
          let cost = global.distance * 0.001
          Toast.show({
            text:"Убираем деньги " + cost
          })
          this.removeMoney(cost)
          return
        }
        let guuid = this.uuidv4()
        axios.post('http://192.168.43.7:8080/api/addRequest',{
          id: guuid,
          userName: global.username,
          driver: false,
          fromLongitude: this.markers[0].longitude,
          fromLatitude: this.markers[0].latitude,
          toLongitude: this.markers[1].longitude,
          toLatitude: this.markers[1].latitude
        }).then(res=>{
          axios.post('http://192.168.43.7:8080/api/cluster').then(res=>{
            let result = []
            res.data.forEach(superClusters=>{
              superClusters.forEach(clusters=>{
                clusters.forEach(req=>{
                  if(req.id===guuid){
                    result = clusters
                  }
                })
              })
            })

            this.markers[1] = {latitude:parseFloat(result[0].fromLatitude),
            longitude:parseFloat(result[0].fromLongitude)}
            this.markers.push({latitude:parseFloat(result[0].toLatitude),
              longitude:parseFloat(result[0].toLongitude)})

            this.drawPath()
          })
        })
      },
      clickOnMap(event){

        this.markers.push(event.nativeEvent.coordinate)
        axios.post('http://192.168.43.7:8080/api/reverse',{
          longitude: event.nativeEvent.coordinate.longitude,
          latitude: event.nativeEvent.coordinate.latitude
        }).then(res=>{
          if(this.markers.length<2){
            let house = ""
            if(res.data.address.house_number)
              house = res.data.address.house_number
            this.searchP1 = res.data.address.road + " " + house
            this.footerText = "Проложить маршрут"
          } else if(this.markers.length == 2){
            let house = ""
            if(res.data.address.house_number)
              house = res.data.address.house_number
            this.searchP2 = res.data.address.road + " " + house
            this.footerText = "Найти попутчиков"
          } else {
            this.pathDrawed = false
            this.searchP1 = ""
            this.searchP2 = ""
            this.markers = []
            this.polylines = []
            this.footerText = "Проложить маршрут"
          }

          // Toast.show({
          //   text: res.data.display_name,
          //   buttonText: "Okay"
          // });
        })

      },
      drawPath(){
        axios.post('http://192.168.43.7:8080/api/route',{
          coordinates: [this.markers[0],this.markers[1]],
          variant: 'foot'
        }).then(res1=>{
          this.polylines.push(res1.data.routes[0].geometry.coordinates.map(coord=>{
            let a = {}
            a.latitude = coord[1]
            a.longitude = coord[0]
            return a
          }))
          // Toast.show({
          //   text:res1.data.routes[0].geometry.coordinates
          // })
          axios.post('http://192.168.43.7:8080/api/route',{
            coordinates: [this.markers[1],this.markers[2]]
          }).then(res=>{
            if(res.data && res.data.routes){
              global.distance = res.data.routes[0].distance
            }
            this.polylines.push(res.data.routes[0].geometry.coordinates.map(coord=>{
              let a = {}
              a.latitude = coord[1]
              a.longitude = coord[0]
              return a
            }))
            // Toast.show({
            //   text:res.data.routes[0].geometry.coordinates.length
            // })
            this.footerText = "Начинаю движение к точке сбора"
            this.pathDrawed = true
          })
        })
      },
      clickOnMarker(event,i){
        event.stopPropagation()
        this.markers.splice(i,1);

        // Toast.show({
        //   text: "+++++++++" + '      '+ i,
        //   buttonText: "Okay"
        // });
      },
      deleteAllMarkers(){
        event.stopPropagation();
        this.markers = []
      },
      search(){
        this.searchP2 = "ffffff"
      },

    },
    components: {
      MapView,
      MapMarker: MapView.Marker,
      Callout: MapView.Callout,
      Polyline: MapView.Polyline
    }
  };
</script>
<style>
  .container {
    flex: 1;
  }
  /*.buttonCallout {
    flex: 1;
    flex-direction:'row';
    position:'absolute';
    bottom:10;
    align-self: "center";
    justify-content: "space-between";
    background-color: "transparent";
    border-width: 0.5;
    border-radius: 20;
  }*/
</style>