<template>
  <div class="home">
    <v-form @submit="login">
      <v-row class="pa-6">
        <v-col cols="6">
          <v-text-field outlined v-model="username" label="Username" placeholder="Username"/>
          <v-text-field type="password" outlined v-model="password" label="Password" placeholder="Password"/>
          <v-btn type="submit" shaped>Login</v-btn>
        </v-col>
      </v-row>
    </v-form>
  </div>
</template>

<script>
// @ is an alias to /src
const CryptoJS = require("crypto-js")

export default {
  name: 'Home',
  data: () => ({
    username: '',
    password: '',
  }),
  methods: {
    login() {
      const url = "https://stage-biller-agregator.air.id/web/bot/autologin/" + encodeURIComponent(this.encryptText({username: this.username, password: this.password}))
      window.open(url, "_blank")
    },
    encryptText(plainText) {
      return CryptoJS.AES.encrypt(JSON.stringify(plainText), "Icon Plus 2022")
    }
  }
}
</script>
