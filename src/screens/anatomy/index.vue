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
                <nb-button success  >
                  <nb-text>Success</nb-text>
                </nb-button>
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
        <nb-button active full>
          <nb-text>Footer</nb-text>
        </nb-button>
      </nb-footer-tab>
    </nb-footer>



  </nb-container>
</template>
<script>
  import MapView from "react-native-maps";
  import { Toast } from "native-base";
  export default {
    data: function() {
      return {
        coordinates: {
          latitude: 12.91074,
          longitude: 77.5996363,
          latitudeDelta: 0.0922,
          longitudeDelta: 0.0421,
        },
        markers: [{latitude: 12.91074,
          longitude: 77.5996363}]
      };
    },
    methods:{
      clickOnMap(event){

        this.markers.push(event.nativeEvent.coordinate)
        Toast.show({
          text: event.nativeEvent.coordinate.latitude + '        ' + event.nativeEvent.coordinate.longitude,
          buttonText: "Okay"
        });

      },
      clickOnMarker(event,i){
        event.stopPropagation()
        Toast.show({
          text: "+++++++++" + '      '+ i,
          buttonText: "Okay"
        });
      },
      deleteAllMarkers(){
        event.stopPropagation();
        this.markers = []
      },

    },
    components: {
      MapView,
      MapMarker: MapView.Marker,
      Callout: MapView.Callout,

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