<template>
  <nb-container :style="{ backgroundColor: '#fff' }">
    <nb-header>
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
    <nb-header searchBar rounded>
      <nb-item>
        <nb-icon active name="search" />
        <nb-input placeholder="Search" v-model="searchP1"/>
        <nb-icon active name="people" />
      </nb-item>
      <nb-button transparent>
        <nb-text>Search</nb-text>
      </nb-button>
    </nb-header>
    <nb-header searchBar rounded>
      <nb-item>
        <nb-icon active name="search" />
        <nb-input placeholder="Search" v-model="searchP2"/>
        <nb-icon active name="people" />
      </nb-item>
      <nb-button transparent :onPress="()=>{search();}">
        <nb-text>Search</nb-text>
      </nb-button>
    </nb-header>
      <view  class="container">
        <map-view class="container"
                  :initial-region="coordinates"
                  :onPress="(event)=>{
                  clickOnMap(event);}"
        >

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
          <callout :style="{ marginBottom: 50}">
            <view>
              <nb-button success  >
                <nb-text>Success</nb-text>
              </nb-button>

            </view>
          </callout>
        </map-view>



      </view>
    <nb-footer>
      <nb-footer-tab>
        <nb-button active full :onPress="(event)=>{createReq(event);}">
          <nb-text>Footer</nb-text>
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
  export default {
    data: function() {
      return {
        coordinates: {
          longitude: 65.52667,
          latitude: 57.149963,
          latitudeDelta: 0.0922,
          longitudeDelta: 0.0421,
        },
        markers: [

          ],
        searchP1:'',
        searchP2:'',
        uuidReq: ''
      };
    },
    created(){
      this.uuidReq = this.uuidv4()
    },
    methods:{
      uuidv4() {
        return 'xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx'.replace(/[xy]/g, function(c) {
          var r = Math.random() * 16 | 0, v = c == 'x' ? r : (r & 0x3 | 0x8);
          return v.toString(16);
        });
      },
      createReq(event){

        event.stopPropagation()

        axios.post('http://192.168.43.7:8080/api/addRequest',{
          id: this.uuidReq,
          userName: 'user2',
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
                  if(req.id===this.uuidReq){
                    result = clusters
                  }
                })
              })
            })

            this.markers[1] = {latitude:parseFloat(result[0].fromLatitude),
            longitude:parseFloat(result[0].fromLongitude)}
            this.markers.push({latitude:parseFloat(result[0].toLatitude),
              longitude:parseFloat(result[0].toLongitude)})
          })
        })
      },
      clickOnMap(event){

        this.markers.push(event.nativeEvent.coordinate)
        axios.post('http://192.168.43.7:8080/api/reverse',{
          longitude: event.nativeEvent.coordinate.longitude,
          latitude: event.nativeEvent.coordinate.latitude
        }).then(res=>{
          if(this.markers.length===1){
            this.searchP1 = res.data.display_name
          }
          if(this.markers.length===2){
            this.searchP2 = res.data.display_name
          }

          Toast.show({
            text: res.data.display_name,
            buttonText: "Okay"
          });
        })

      },
      clickOnMarker(event,i){
        event.stopPropagation()
        this.markers.splice(i,1);

        Toast.show({
          text: "+++++++++" + '      '+ i,
          buttonText: "Okay"
        });
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
      Callout: MapView.Callout
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