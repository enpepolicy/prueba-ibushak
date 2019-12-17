<template>
  <v-app>
    <v-container class="fill-height">
      <v-row justify="center"  align="center">
        <v-btn
        @click="getPosts">
          Pedir
        </v-btn>
      </v-row>
    </v-container>
  </v-app>
</template>

<script>
import axios from 'axios'

export default {
  name: 'App',

  components: {
    //
  },

  data: () => ({
    celularesCategoryID: 'MLM1051',
    celularesCategoryNestedID: 'MLM1055',
    attributes: 'id,price,category_id,title',
    appName: 'prueba-ibushak',
    appID: '4631758456484172',
    userID: '92127685',
    paisSiglas: 'MLM',
    appSecretKey: 'EbaUvc3sJfmcC2Q6OE9d1SWGLwnzir44',
    accessTokenGeneratorCode: 'TG-5df879cebeb417000678978b-92127685',
    accessToken: 'APP_USR-4631758456484172-121707-d33c31f29eb2ebf7d3e441b7f4b4655a-92127685',
    paging: null,
    newToken: ''
  }),

  methods: {
    // No jala por preflight
    async refreshToken(){
      await axios
      .post(`https://auth.mercadolibre.com.co/authorization?response_type=token&client_id=${this.appID}`, {
        'grant_type': 'refresh_token',
        'refresh_token' : this. accessToken,
        'client_id': this.appID,
        'client_secret': this.appSecretKey
      })
      .then(
        (res) => {
          this.newToken = res.data;
          console.log(this.newToken)
        }
      )
      .catch(e => console.log(e))
    },
    async getPaging(){
      await axios
      .get(`https://api.mercadolibre.com/sites/${this.paisSiglas}/search?category=${this.celularesCategoryNestedID}}&sort=price_asc`)
      .then(
        (res) => {
          this.paging = res.data.paging;
          console.log(this.paging)
        }
      )
      .catch(e => console.log(e))
    },
    async getPosts(){
      console.log('Inicio petición')
      await axios
      .get(`https://api.mercadolibre.com/sites/${this.paisSiglas}/listing_types`) // check category
      // .get(`https://api.mercadolibre.com/sites/${this.paisSiglas}/search?category=${this.celularesCategoryNestedID}&sort=price_asc`)
      // .get(`https://api.mercadolibre.com/users/${this.userID}/items/search?search_type=scan&access_token=${this.accessToken}`) // + de 1l
      .then(
        (res) => {
          this.firstQuery = res.data;
          console.log(this.firstQuery)
        }
      )
      .catch(e => console.log(e))
    }
  }
};
</script>
