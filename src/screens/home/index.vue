<template>
  <nb-container>
    <status-bar :barStyle="'light-content'"></status-bar>
    <image-background :source="launchScreenBg" class="imageContainer">

      <view class="text-container">
        <nb-h3 :style="{ marginTop: 100 }" class="text-color-white"
          >Поиск попутчиков в городе</nb-h3>
      </view>

      <view class="text-container">
        <nb-h3 :style="{ marginTop: 20 }" class="text-color-white"
        >Введи юзернейм:</nb-h3>
      </view>


        <nb-input  placeholder="Юзернейм" v-model="username"/>

      <view :style="{ marginBottom: 20, alignSelf: 'center' }">
        <nb-button rounded info
          :style="stylesObj.btnContainer"
          :onPress="handleLetGoBtnPress"
        >
          <nb-text> Войти </nb-text>
        </nb-button>
      </view>
    </image-background>
  </nb-container>
</template>

<script>
import { Dimensions, Platform } from "react-native";
import launchScreenBg from "../../../assets/launchscreen-bg.png";
import launchscreenLogo from "../../../assets/logo-kitchen-sink.png";
import { Toast } from "native-base";
import axios from 'axios'

export default {
  props: {
    navigation: {
      type: Object
    }
  },
  data() {
    return {
      launchScreenBg,
      launchscreenLogo,
      username: "den",
      stylesObj: {
        logoContainerStyle: {
          marginTop: Dimensions.get("window").height / 10
        },
        logoStyle: {
          height:300,
          left: Platform.OS === "android" ? 40 : 50,
          top: Platform.OS === "android" ? 35 : 60
        },
        btnContainer: {
          backgroundColor: "#6faf98",
          alignSelf: "center"
        }
      }
    };
  },
  methods: {
    handleLetGoBtnPress() {
      if(this.username == ""){
        Toast.show({
          text: "Заполни юзернейм!"
        })
        return
      }
      axios.get('http://192.168.43.7:8080/coin/user/' + this.username).then(res1=> {
        global.coins = res1.data.balance
        global.username = res1.data.username
        global.driver = res1.data.driver
        global.pinAccepted = false
        this.navigation.openDrawer();
      })
    }
  }
};
</script>

<style>
.imageContainer {
  flex: 1;
}
.text-color-primary {
  color: blue;
  font-family: Roboto;
}
.logoContainer {
  flex: 1;
  margin-bottom: 30;
}
.logo {
  position: absolute;
  width: 300;
  height: 100;
}
.text-container {
  align-items: center;
  margin-bottom: 50;
  background-color: transparent;
}
.text-color-white {
  color: white;
}
.button-container {
  background-color: #6faf98;
  align-self: center;
}
</style>
