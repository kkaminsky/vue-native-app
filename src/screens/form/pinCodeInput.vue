<template>
  <nb-container :style="{ backgroundColor: '#fff' }">
    <nb-header>
      <nb-left>
        <nb-button transparent :onPress="() => this.props.navigation.goBack()">
          <nb-icon name="arrow-back" />
        </nb-button>
      </nb-left>
      <nb-body>
        <nb-title>Floating Label</nb-title>
      </nb-body>
      <nb-right />
    </nb-header>

    <nb-content padder>
      <nb-form>
        <nb-item floatingLabel>
          <nb-label>Пин код</nb-label>
          <nb-input v-model="pincode"/>
        </nb-item>
      </nb-form>
      <nb-button block :style="{ margin: 15, marginTop: 50 }" :onPress="(event)=>{regPin(event);}">
        <nb-text>Принять</nb-text>
      </nb-button>
    </nb-content>
  </nb-container>
</template>
<script>
  import axios from 'axios'

  export default {
    data() {
      return {
        pincode:""
      }
    },
    methods: {
      regPin(){
        axios.post('http://192.168.43.247:8080/api/pincode',{
          pincode: this.pincode
        }).then(res=>{
          if(res.data) {
            Toast.show({
              text:"Успешной поездки!"
            })
            global.pinAccepted = true
            this.props.navigation.goBack()
          }
        })
      }
    }
  }
</script>