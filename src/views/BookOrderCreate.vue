<template>
  <v-container>
    <v-row>
      <v-col class="col-lg-6 mx-auto">
        <v-card>
          <v-toolbar dark flat color="primary">
            <v-toolbar-title>Your order</v-toolbar-title>
          </v-toolbar>
          <v-card-text v-if="storageNumGetter!==0">
            <v-container>
              <v-row>
                <v-col class="col-12" v-for="book in storageGetter" :key="book.isbn">
                  <BookInfoCard :book="book"/>
                </v-col>
              </v-row>
            </v-container>
            <v-container>
              <span class="d-block"><b>Order date:</b> {{ takenDateFirst }} – {{ takenDateSecond }}</span>
              <span class="d-block"><b>Expected return date:</b> {{ estimatedReturnDate }}</span>
            </v-container>
            <div class="d-flex">
              <v-btn color="primary" depressed class="ml-auto" @click="doOrder()">
                Order
              </v-btn>
            </div>
          </v-card-text>
          <v-card-text v-else>
            <span>Your basket is empty :(</span>
            <div class="d-flex">
              <v-btn color="primary" depressed class="ml-auto" @click="$router.push('/')">
                Back to catalog
              </v-btn>
            </div>
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import axios from "axios";
import BookInfoCard from "../components/BookInfoCard";
import {mapActions, mapGetters, mapMutations} from "vuex";
import router from "@/router";

const endpoint = process.env.VUE_APP_ENDPOINT;

export default {
  name: "BookOrder",
  components: {
    BookInfoCard,
  },
  methods: {
    ...mapActions([
      'orderAction',
    ]),
    ...mapMutations([
      'storageRemoveAllMutation',
    ]),
    doOrder() {
      axios.post(`${endpoint}/orders`, {
        bookIsbns: this.storageGetter.map(book => book.isbn),
      }).then(response => {
        console.log(response)
        this.storageRemoveAllMutation()
        router.push('/')
      }).catch(error => {
        console.log(error)
      });
    }
  },
  data() {
    return {
      takenDateFirst: new Date().toDateString(),
      takenDateSecond: new Date(new Date().getTime() + (24 * 60 * 60 * 1000)).toDateString(),
      estimatedReturnDate: new Date(new Date().getTime() + (31 * 24 * 60 * 60 * 1000)).toDateString()
    }
  },
  computed: {
    ...mapGetters([
      'storageGetter', 'storageNumGetter'
    ]),
  },
}
</script>
