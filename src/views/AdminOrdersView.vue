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
          <v-col class="col-12 col-lg-4" v-for="order in orders" :key="order.id">

            <v-card class="d-flex flex-column" :class="{ 'lg-height': $vuetify.breakpoint.lg }">
              <v-card-title>Order ID: {{ order.id }}</v-card-title>
              <v-card-subtitle>Order status: {{ order.status }}</v-card-subtitle>
              <v-card-text>
                  <span class="copy-id" v-for="copy in order.copies" :key="copy.id">
                    Book copy ID: {{ copy.id }}
                  </span>
              </v-card-text>

              <v-spacer></v-spacer>

              <v-card-actions class="d-flex flex-row">
                <v-btn
                    v-if="order.status === 'PENDING'"
                    color="success"
                    class="ma-2 flex-grow-1"
                    @click="changeStatus(order, 'TAKEN')"
                    text
                >
                  <v-icon left>mdi-arrow-right</v-icon>
                  TAKEN
                </v-btn>
                <v-btn
                    v-if="order.status === 'PENDING'"
                    color="error"
                    class="ma-2 flex-grow-1"
                    @click="changeStatus(order, 'CANCELED')"
                    text
                >
                  <v-icon left>mdi-cancel</v-icon>
                  CANCEL
                </v-btn>
                <v-btn
                    v-if="order.status === 'TAKEN'"
                    color="success"
                    class="ma-2 flex-grow-1"
                    @click="changeStatus(order, 'RETURNED')"
                    text
                >
                  <v-icon left>mdi-arrow-left</v-icon>
                  RETURNED
                </v-btn>

              </v-card-actions>
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

<style scoped>
.copy-id {
  display: block;
}

.lg-height {
  min-height: 248px;
}

</style>