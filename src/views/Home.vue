<template>
  <div class="home">
    <v-row class="pa-6">
      <v-col cols="6">
        <v-form @submit.prevent="login">]
          <v-card class="pa-6">
            <v-select
                :items="items"
                v-model="url"
                label="URL"
                outlined
                clearable
            ></v-select>
            <v-text-field outlined v-model="username" label="Username" placeholder="Username"/>
            <v-text-field type="password" outlined v-model="password" label="Password" placeholder="Password"/>
            <v-btn type="submit" shaped>Login</v-btn>
          </v-card>
        </v-form>
      </v-col>
      <v-col cols="6">
        <v-form @submit.prevent="encryptParams">
          <v-card class="pa-6">
            <v-card-title>
              <div style="width: 100% !important; max-width: 100% !important;" class="d-flex align-center">
                <p style="width: 100% !important; max-width: 100% !important;" class="ma-0">Parameters</p>
                <v-menu
                    v-model="menuSetting"
                    :close-on-content-click="false"
                    :nudge-width="360"
                    offset-x
                >
                  <template v-slot:activator="{ on, attrs }">
                    <v-btn color="primary" class="px-3" height="36" icon
                           v-bind="attrs" v-on="on">
                      <v-icon>mdi-cog</v-icon>
                    </v-btn>
                  </template>
                  <v-card class="pa-3">
                    <v-form @submit.prevent="menuSetting = false">
                      <v-card-title>Secret Key</v-card-title>
                      <v-card-text>
                        <v-text-field
                            autofocus
                            v-model="secretKey"
                            label="Secret Key"
                            clearable
                            placeholder="Secret Key"
                        />
                      </v-card-text>
                      <v-card-actions>
                        <v-spacer/>
                        <v-btn type="submit" color="primary">Submit</v-btn>
                      </v-card-actions>
                    </v-form>
                  </v-card>
                </v-menu>
                <v-menu
                    v-model="menu"
                    :close-on-content-click="false"
                    :nudge-width="360"
                    offset-x
                >
                  <template v-slot:activator="{ on, attrs }">
                    <v-btn color="primary" class="px-3" height="36" icon
                           v-bind="attrs" v-on="on">
                      <v-icon>mdi-plus</v-icon>
                    </v-btn>
                  </template>
                  <v-card class="pa-3">
                    <v-form @submit.prevent="addParameters">
                      <v-card-title>ADD PARAMETER</v-card-title>
                      <v-card-text>
                        <v-text-field
                            v-model="keyName"
                            label="Key"
                            placeholder="Key"
                        />
                      </v-card-text>
                      <v-card-actions>
                        <v-spacer/>
                        <v-btn type="submit" color="primary">Submit</v-btn>
                      </v-card-actions>
                    </v-form>
                  </v-card>
                </v-menu>
              </div>
            </v-card-title>
            <v-card-text>
              <v-text-field v-for="(key, index) in Object.keys(params)"
                            :key="index"
                            :label="key"
                            :placeholder="key"
                            v-model="params[key]"
              >
                <template v-slot:append-outer>
                  <v-btn class="ml-2"
                         color="error"
                         fab
                         dark
                         small
                         @click="deleteParams(key)">
                    <v-icon>mdi-close</v-icon>
                  </v-btn>
                </template>
              </v-text-field>
              <v-btn type="submit" shaped>Submit</v-btn>
            </v-card-text>
            <v-card class="my-2">
              <v-card-title>params</v-card-title>
              <v-card-text>
                {{params}}
              </v-card-text>
            </v-card>
            <v-card>
              <v-card-title>resultEncrypt</v-card-title>
              <v-card-text>
                <div class="d-inline-flex" style="max-width: 100% !important; width: 100% !important;">
                  <div class="d-inline-flex text-break mr-2" style="width: 100%; vertical-align: middle">
                    <p class="my-auto mx-0">{{ resultEncrypt }}</p>
                  </div>
                  <v-btn @click="copy(resultEncrypt)" icon small>
                    <v-icon color="primary" size="18">mdi-content-copy</v-icon>
                  </v-btn>
                </div>
              </v-card-text>
            </v-card>
          </v-card>
        </v-form>
      </v-col>
    </v-row>
  </div>
</template>

<script>
import 'vue-json-viewer/style.css'
// @ is an alias to /src
const CryptoJS = require("crypto-js")

export default {
  name: 'Home',
  components: {},
  data: () => ({
    username: '',
    password: '',
    url: 'http://localhost:3000',
    items: [
      'http://localhost:3000',
      'https://dev-biller-agregator.air.id/web/bot/autologin/',
      'https://stage-biller-agregator.air.id/web/bot/autologin/',
    ],
    keyName: '',
    params: {},
    menu: false,
    menuSetting: false,
    resultEncrypt: '',
    secretKey: '',
  }),
  methods: {
    login() {
      if (this.url) {
        const url = this.url + encodeURIComponent(this.encryptText({username: this.username, password: this.password}))
        window.open(url, "_blank")
      } else alert("Silakan pilih URL terlebih dahulu")
    },
    encryptText(plainText) {
      return CryptoJS.AES.encrypt(JSON.stringify(plainText), "Icon Plus 2022")
    },
    encryptParams() {
      if (this.secretKey) {
        const parsedBase64Key = CryptoJS.enc.Base64.parse(this.secretKey);
        const jsonString = JSON.stringify(this.params)
        this.resultEncrypt = CryptoJS.AES.encrypt(jsonString, parsedBase64Key, {
          mode: CryptoJS.mode.ECB,
          padding: CryptoJS.pad.Pkcs7
        }).toString()
      } else alert("Secret Key harus diisi")
    },
    addParameters() {
      this.params[this.keyName] = ''
      this.keyName = ''
      this.menu = false
      this.$forceUpdate()
    },
    deleteParams(key) {
      delete this.params[key]
      this.$forceUpdate()
    },
    copy(text) {
      navigator.clipboard.writeText(text).then(
          () => {
            alert("Berhasil Tersalin ke Papan Klip: " + text); // success
          })
          .catch(
              function (e) {
                alert("ERROR: " + e); // error
              });
    },

  }
}
</script>
