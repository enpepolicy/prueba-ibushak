<template>
  <v-app>
    <v-container class="fill-height">
      <v-row justify="center"  align="center">
        <v-col cols="12">
          <h2 class="text-center">
            Hola, me llamo Enrique y esta es mi pueba para trabajar con Uds.
          </h2>
          <h3 class="text-center">
            Abre la consola y mira como se ejecuta el programa.
          </h3>
          <h3 class="text-center">
            Existen muchos elementos mejorables, como el estilo, seller_id y la paginacion de respuesta.
          </h3 >
          <h3 class="text-center">Mira el repo <a href="https://github.com/enpepolicy/prueba-ibushak" target="_blank">acá</a></h3>

        </v-col>
        <v-btn
        :loading="btnALoading"
        :disabled="itemsOk"
        class="ma-2"
        @click="getPostsLoop(); btnALoading = !btnALoading">
          Get Posts Loop
        </v-btn>
        <v-btn
        :loading="btnBLoading"
        :disabled="!filterOk "
        class="ma-2"
        @click="filterItems()">
          Filter Array
        </v-btn>


        <v-col cols="12" v-if="itemsAcumulationFilterSorted">

        
          <v-data-iterator
            :items="itemsAcumulationFilterSorted"
            hide-default-footer
          >
            <template v-slot:header>
              <v-toolbar
                class="mb-2"
                color="grey"
                dark
                flat
              >
                <v-toolbar-title class="text-center">Lista de 1k Celus con precio ascedente de ML México</v-toolbar-title>
              </v-toolbar>
            </template>

            <template v-slot:default="props" v-if="itemsAcumulationFilterSorted">
              <v-row>
                <v-col
                  v-for="item in itemsAcumulationFilterSorted"
                  :key="item.itemId"
                  cols="12"
                  sm="6"
                  md="4"
                  lg="3"
                >
                  <v-card
                  hover
                  color="#d3f0ff"
                  flat>
                    <v-card-title
                    class="subheading font-weight-bold">{{ item.itemId }}</v-card-title>

                    <v-divider></v-divider>

                    <v-list dense>
                      <v-list-item>
                        <v-list-item-content>Seller Id:</v-list-item-content>
                        <v-list-item-content class="align-end">{{ item.sellerId }}</v-list-item-content>
                      </v-list-item>

                      <v-list-item>
                        <v-list-item-content>Seller Name:</v-list-item-content>
                        <v-list-item-content class="align-end">{{ item.sellerName }}</v-list-item-content>
                      </v-list-item>

                      <v-list-item>
                        <v-list-item-content>Marca:</v-list-item-content>
                        <v-list-item-content class="align-end">{{ item.marca }}</v-list-item-content>
                      </v-list-item>

                      <v-list-item>
                        <v-list-item-content>Free Shipping:</v-list-item-content>
                        <v-list-item-content class="align-end">{{ item.freeShipping }}</v-list-item-content>
                      </v-list-item>

                      <v-list-item>
                        <v-list-item-content>Logistic Type:</v-list-item-content>
                        <v-list-item-content class="align-end">{{ item.logisticType }}</v-list-item-content>
                      </v-list-item>

                      <v-list-item>
                        <v-list-item-content>Operation Place:</v-list-item-content>
                        <v-list-item-content class="align-end">{{ item.sellerOperationPlace }}</v-list-item-content>
                      </v-list-item>

                      <v-list-item>
                        <v-list-item-content>Item Condition:</v-list-item-content>
                        <v-list-item-content class="align-end">{{ item.itemCondition }}</v-list-item-content>
                      </v-list-item>

                      <v-list-item>
                        <v-list-item-content>Price:</v-list-item-content>
                        <v-list-item-content class="align-end blue--text">{{ item.price }}</v-list-item-content>
                      </v-list-item>
                    </v-list>
                  </v-card>
                </v-col>
              </v-row>
            </template>
          </v-data-iterator>  

        </v-col>

        <v-btn
        v-if="itemsAcumulationFilterSorted"
        class="ma-2"
        @click="printItems">
          Log array
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
    newToken: '',
    offsetAcumulator: 0,
    offsetTop: 1000, //1050,
    filterOk: false,
    itemsOk: false,
    sortedOk: false,
    btnALoading: false,
    btnBLoading: false,
    itemsAcumulation: [],
    itemsAcumulationFilter: [],
    itemsAcumulationFilterMapped: [],
    itemsAcumulationFilterSorted: null,
  }),

  methods: {
    // No jala por preflight
    // async refreshToken(){
    //   await axios
    //   .post(`https://auth.mercadolibre.com.co/authorization?response_type=token&client_id=${this.appID}`, {
    //     'grant_type': 'refresh_token',
    //     'refresh_token' : this. accessToken,
    //     'client_id': this.appID,
    //     'client_secret': this.appSecretKey
    //   })
    //   .then(
    //     (res) => {
    //       this.newToken = res.data;
    //       console.log(this.newToken)
    //     }
    //   )
    //   .catch(e => console.log(e))
    // },
    // async getPaging(){
    //   await axios
    //   .get(`https://api.mercadolibre.com/sites/${this.paisSiglas}/search?category=${this.celularesCategoryNestedID}}&sort=price_asc`)
    //   .then(
    //     (res) => {
    //       this.paging = res.data.paging;
    //       console.log(this.paging)
    //     }
    //   )
    //   .catch(e => console.log(e))
    // },
    async getPosts(){
      await axios
      .get(`https://api.mercadolibre.com/sites/${this.paisSiglas}/search?category=${this.celularesCategoryNestedID}&sort=price_asc&offset=${this.offsetAcumulator}`)
      .then(
        (res) => {
          this.itemsAcumulation = this.itemsAcumulation.concat(res.data.results)
        }
      )
      .catch(e => console.log(e))
    },
    async getPostsLoop(){

      if(this.offsetAcumulator < this.offsetTop){
        for (this.offsetAcumulator = 0; this.offsetAcumulator < this.offsetTop; this.offsetAcumulator += 50) {
          await this.getPosts()
          console.log("Offset Acumulator", this.offsetAcumulator)
          console.log("Item Acumulator length", this.itemsAcumulation.length)
        }
      }
      console.log("Fin bucle de recogida de items")
      this.btnALoading = !this.btnALoading
      this.itemsOk = true
      this.filterOk = true
    },
    async filterItems(){
      this.btnBLoading = !this.btnBLoading;
      this.itemsAcumulationFilter = this.itemsAcumulation;
      this.itemsAcumulationFilterMapped = this.itemsAcumulationFilter.map(
        obj => {
          let rObj = {};
          rObj["itemId"] = obj.id;
          rObj["sellerId"] = obj.seller.id;
          rObj["sellerName"] = 'No alcance a desarrollar segunda petición';

          if(obj.attributes[0]){
            rObj["marca"] = obj.attributes[0].value_name ;
          }
          else{
            rObj["marca"] = 'no hay' ;
          }

          rObj["freeShipping"] = obj.shipping.free_shipping;
          rObj["logisticType"] = obj.shipping.logistic_type;
          rObj["sellerOperationPlace"] = obj.address.city_name;
          rObj["itemCondition"] = obj.condition;
          rObj["price"] = obj.price;
          return rObj;
        }
      );  

      this.itemsAcumulationFilterSorted = this.itemsAcumulationFilterMapped.sort(function(a, b) {
        return a.price - b.price
      });

      this.filterOk = false
      this.sortedOk = true
      this.btnBLoading = !this.btnBLoading
    },
    async printItems(){
      console.log(this.itemsAcumulationFilterSorted)
    },
  }
};
</script>
