<template>
  <nb-container>
    <nb-content class="sidebar-content-wrapper" :bounces="false">
      <image
        :source="drawerCover"
        class="drawer-cover"
        :style="stylesObj.drawerCoverObj"
      />
      <view class="text-container">
        <nb-h3 :style="{ marginBottom: 8 }" class="text-color-white"
        >Привет, {{username}}</nb-h3>
      </view>
      <view class="text-container">
        <nb-h3 :style="{ marginBottom: 8 }" class="text-color-white"
        >Литров: {{coins}}</nb-h3>
      </view>
      <nb-list>
        <nb-list-item
          v-for="data in datas"
          :key="data.route"
          button
          noBorder
          :onPress="() => handleListItemClick(data)"
        >
          <nb-left>
            <nb-icon
              active
              :name="data.icon"
              :style="{ color: '#777', fontSize: 26, width: 30 }"
            />
            <nb-text>
              {{ data.name }}
            </nb-text>
          </nb-left>
          <nb-right v-if="data.types"   :style="{ flex: 1 }">
            <nb-badge
              class="list-item-badge-container"
              :style="{ backgroundColor: data.bg }"
            >
              <nb-text
                class="list-item-badge-text"
                :style="stylesObj.badgeText"
              >
                {{ `${data.types} Types` }}
              </nb-text>
            </nb-badge>
          </nb-right>
        </nb-list-item>
      </nb-list>
    </nb-content>
  </nb-container>
</template>

<script>
import { Dimensions, Platform } from "react-native";
import drawerCover from "../../../assets/drawer-cover.png";
import drawerImage from "../../../assets/logo-kitchen-sink.png";
import axios from 'axios'

const deviceHeight = Dimensions.get("window").height;
const deviceWidth = Dimensions.get("window").width;
export default {
  props: {
    navigation: {
      type: Object
    }
  },
  data() {
    return {
      drawerCover,
      drawerImage,
      stylesObj: {
        drawerCoverObj: {
          height: deviceHeight / 3.5
        },
        drawerImageObj: {
          left: Platform.OS === "android" ? deviceWidth / 10 : deviceWidth / 9,
          top:
            Platform.OS === "android" ? deviceHeight / 13 : deviceHeight / 12,
          resizeMode: "cover"
        },
        badgeText: {
          fontSize: Platform.OS === "android" ? 11 : 13,
          marginTop: Platform.OS === "android" ? -3 : 0,
          fontWeight: "400"
        }
      },
      username: global.username,
      coins: global.coins,
      datas: [
        {
          name: "Поиск попутчиков",
          route: "Anatomy",
          icon: "phone-portrait",
          bg: "#C5F442"
        },
        {
          name: "История",
          route: "NHToast",
          icon: "albums",
          bg: "#5cb85c",
          types: "6"
        },
        {
          name: "Предложения партнеров",
          route: "NHTypography",
          icon: "paper",
          bg: "#62B1F6",
          types: "36"
        }
      ]
    };
  },
  methods: {
    handleListItemClick(dataObj) {
      axios.get('http://192.168.43.7:8080/coin/user/' + global.username).then(res1=> {
        global.coins = res1.data.balance
        global.username = res1.data.username
        global.driver = res1.data.driver
        this.username = global.username
        this.coins = global.coins
      })
      this.navigation.navigate(dataObj.route);
    }
  }
};
</script>

<style>
.sidebar-content-wrapper {
  flex: 1;
  background-color: #fff;
}
.drawer-cover {
  flex: 1;
  align-self: stretch;
  position: relative;
  margin-bottom: 10;
}
.drawer-image {
  align-self: center;
  position: absolute;
  height: 75;
  width: 210;
}
.list-item-badge-container {
  border-radius: 3;
  height: 25;
  width: 72;
}
.list-item-badge-text {
  /* font-weight: 400; // not-working  */
  /* font-weight: bold; // working */
  text-align: center;
}
</style>
