<template>
  <v-container>
    <v-row>
      <v-col class="col-12 col-md-6 col-lg-3">
        <v-card>
          <v-card-title>
            Filters
          </v-card-title>
          <v-card-text>
            <v-autocomplete
                v-model="selectedUserId"
                label="User ID"
                dense
                outlined
                @change="getContent"
            ></v-autocomplete>

            <v-autocomplete
                v-model="selectedStatus"
                label="Status"
                dense
                outlined
                @change="getContent"
            ></v-autocomplete>

            <v-btn text block color="primary" @click="clearFilters">
              Clear filters
            </v-btn>
          </v-card-text>
        </v-card>
      </v-col>

      <v-col class="col-12 col-md-6 col-lg-9">
        <v-row>
          <v-col class="col-12 col-lg-6" v-for="order in orders" :key="order.id">
            <v-card>
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
                  @click="changeStatus(order, 'TAKEN')"
                  text>Видати
              </v-btn>
              <v-btn
                  v-if="order.status === 'PENDING'"
                  color="error"
                  class="my-2 mx-2"
                  depressed
                  text
                  @click="changeStatus(order, 'CANCELED')"
              >Відмінити
              </v-btn>

              <v-btn
                  v-if="order.status === 'TAKEN'"
                  color="success"
                  class="my-2 mx-2"
                  depressed
                  @click="changeStatus(order, 'RETURNED')"
                  text>Отримати
              </v-btn>
            </v-card>
          </v-col>
        </v-row>
      </v-col>
    </v-row>
  </v-container>
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
    axios.get(`${endpoint}/orders/search`,
        {
          params: {
            userId: "",
          }
        }
    ).then(response => {
      this.orders = response.data
    })
  },
  methods: {
    changeStatus(order, status) {
      // get order by id
      order.status = status
      console.log("Order")
      console.log(order)
      axios.put(`${endpoint}/orders/${order.id}`,
          order
      ).then(response => {
        this.orders.forEach(function (order) {
          if (order.id === response.id)
            order = response
        });
      })
    }
  }
}
</script>
