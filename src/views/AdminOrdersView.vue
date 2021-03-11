<template>
  <div>
    <v-card v-for="order in orders" :key="order.id" class="my-2">
      <v-card-title>Order id {{ order.id }}</v-card-title>
      <v-card-subtitle>Order status {{ order.status }}</v-card-subtitle>
      <v-list>
        <v-list-item v-for="copy in order.copies" :key="copy.id">
          Book copy id {{ copy.id }}
        </v-list-item>
      </v-list>
      <v-btn
          v-if="order.status === 'PENDING'"
          color="success"
          class="my-2 mx-2"
          depressed
          text >Видати замовлення</v-btn>
<!--          @click="$router.push('/orders-list')"-->

      <v-btn
          v-if="order.status === 'TAKEN'"
          color="success"
          class="my-2 mx-2"
          depressed
          text >Отримати книги</v-btn>
<!--          @click="$router.push('/orders-list')"-->

    </v-card>
  </div>
</template>

<script>

import axios from "axios";

const endpoint = process.env.VUE_APP_ENDPOINT;

export default {
  name: "AdminOrdersView",
  data() {
    return {
      orders: []
    }
  },
  created() {
     axios.get(`${endpoint}/orders/search`).then(response => {
       this.orders = response.data
       console.log(this.orders)
     })
    // let res = [{ }];
    // this.orders = res;
  }
}
</script>
