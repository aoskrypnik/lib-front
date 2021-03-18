<template>
  <v-container>
    <v-row v-for="order in orders" :key="order.id">
      <v-col>
        <v-card>
          <v-card-title>Order {{ order.id }}</v-card-title>
          <v-card-subtitle>Order status: {{ order.status }}</v-card-subtitle>
          <v-card-text>
            <span>Estimated return date: {{order.estimatedReturnDate.split('T')[0]}} </span>
            <span class="d-block" v-for="book in order.books" :key="book.id">
        Book: {{ book.title }}
      </span>
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import axios from "axios";

const endpoint = process.env.VUE_APP_ENDPOINT;

export default {
  name: "OrdersList",
  data() {
    return {
      orders: []
    }
  },
  created() {
    axios.get(`${endpoint}/orders/current-user`).then(response => {
      this.orders = response.data
      console.log(this.orders)
    })
  }
}
</script>
