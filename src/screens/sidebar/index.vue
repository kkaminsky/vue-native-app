<template>
  <nb-container>
    <nb-content class="sidebar-content-wrapper" :bounces="false">
      <image
        :source="drawerCover"
        class="drawer-cover"
        :style="stylesObj.drawerCoverObj"
      />
      <image
        :source="drawerImage"
        class="drawer-image"
        :style="stylesObj.drawerImageObj"
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
          <nb-right v-if="data.types" :style="{ flex: 1 }">
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
          name: "Anatomy",
          route: "Anatomy",
          icon: "phone-portrait",
          bg: "#C5F442"
        },
        {
          name: "Actionsheet",
          route: "Actionsheet",
          icon: "easel",
          bg: "#C5F442"
        },
        {
          name: "Header",
          route: "Header",
          icon: "phone-portrait",
          bg: "#477EEA",
          types: "10"
        },
        {
          name: "Footer",
          route: "Footer",
          icon: "phone-portrait",
          bg: "#DA4437",
          types: "4"
        },
        {
          name: "Badge",
          route: "NHBadge",
          icon: "notifications",
          bg: "#4DCAE0"
        },
        {
          name: "Button",
          route: "NHButton",
          icon: "radio-button-off",
          bg: "#1EBC7C",
          types: "9"
        },
        {
          name: "Card",
          route: "NHCard",
          icon: "keypad",
          bg: "#B89EF5",
          types: "8"
        },
        {
          name: "Check Box",
          route: "NHCheckbox",
          icon: "checkmark-circle",
          bg: "#EB6B23"
        },
        {
          name: "Deck Swiper",
          route: "NHDeckSwiper",
          icon: "swap",
          bg: "#3591FA",
          types: "2"
        },
        {
          name: "Fab",
          route: "NHFab",
          icon: "help-buoy",
          bg: "#EF6092",
          types: "2"
        },
        {
          name: "Form & Inputs",
          route: "NHForm",
          icon: "call",
          bg: "#EFB406",
          types: "12"
        },
        {
          name: "Icon",
          route: "NHIcon",
          icon: "information-circle",
          bg: "#bfe9ea",
          types: "4"
        },
        {
          name: "Layout",
          route: "NHLayout",
          icon: "grid",
          bg: "#9F897C",
          types: "5"
        },
        {
          name: "List",
          route: "NHList",
          icon: "lock",
          bg: "#5DCEE2",
          types: "8"
        },
        // {
        //   name: "ListSwipe",
        //   route: "ListSwipe",
        //   icon: "swap",
        //   bg: "#C5F442",
        //   types: "3"
        // },
        {
          name: "Picker",
          route: "NHPicker",
          icon: "arrow-dropdown",
          bg: "#F50C75",
          types: "9"
        },
        {
          name: "Radio",
          route: "NHRadio",
          icon: "radio-button-on",
          bg: "#6FEA90"
        },
        {
          name: "SearchBar",
          route: "NHSearchbar",
          icon: "search",
          bg: "#29783B"
        },
        {
          name: "Segment",
          route: "Segment",
          icon: "menu",
          bg: "#0A2C6B",
          types: "2"
        },
        {
          name: "Spinner",
          route: "NHSpinner",
          icon: "navigate",
          bg: "#BE6F50"
        },
        {
          name: "Tabs",
          route: "NHTab",
          icon: "home",
          bg: "#AB6AED",
          types: "3"
        },
        {
          name: "Thumbnail",
          route: "NHThumbnail",
          icon: "image"
        },
        {
          name: "Toast",
          route: "NHToast",
          icon: "albums",
          bg: "#C5F442",
          types: "6"
        },
        {
          name: "Typography",
          route: "NHTypography",
          icon: "paper",
          bg: "#48525D"
        }
      ]
    };
  },
  methods: {
    handleListItemClick(dataObj) {
      axios.get('http://192.168.43.247:8080/coin/user/' + global.username).then(res1=> {
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
