<template>
    <v-container>
        <v-row>
            <v-col class="col-12 col-md-6 col-lg-3">
                <v-card>
                    <v-card-title>
                        Filters
                    </v-card-title>
                    <v-card-text>
                        <v-text-field
                                v-model="selectedUserId"
                                label="User ID"
                                dense
                                outlined
                                @change="getContent"
                        ></v-text-field>

                        <v-autocomplete
                                v-model="selectedStatus"
                                label="Status"
                                :items="orderStatuses"
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

                        <v-card class="d-flex flex-column" :class="{ 'lg-height': $vuetify.breakpoint.lg }">
                            <v-card-title>Order ID: {{ order.id }}</v-card-title>
                            <v-card-subtitle>Order status: {{ order.status }}</v-card-subtitle>
                            <v-card-text>
                                <div>
                                    User id: {{ order.user }}
                                </div>
                                <div class="copy-id" v-for="book in order.books" :key="book.id">
                                    Book: {{ book.title }}
                                </div>
                                <div>
                                    Registered date: {{ formatDate(order.registeredDate) }}
                                </div>
                                <div>
                                    Taken date: {{ formatDate(order.takenDate) }}
                                </div>
                                <div>
                                    Estimated return date: {{ formatDate(order.estimatedReturnDate) }}
                                </div>
                                <div>
                                    Actual return date: {{ formatDate(order.actualReturnDate) }}
                                </div>
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
                orders: [],
                selectedStatus: '',
                selectedUserId: '',
                orderStatuses: ['PENDING', 'TAKEN', 'RETURNED', 'CANCELED']
            }
        },

        created() {
            this.selectedStatus = this.$route.query.status || ''
            this.selectedUserId = this.$route.query.userId || ''
            this.getContent()
        },

        methods: {
            changeStatus(order, status) {
                const index = this.orders.indexOf(order)
                order.status = status
                order.user = null
                console.log(order)
                axios.put(`${endpoint}/orders/${order.id}`, order)
                    .then(response => {
                        this.orders.splice(index, 1, response.data);
                    })
            },

            getContent() {
                this.$router.push({
                    query:
                        {
                            status: this.selectedStatus,
                            userId: this.selectedUserId,
                        }
                });
                axios.get(`${endpoint}/orders/search`, {
                    params: {
                        status: this.selectedStatus,
                        userId: this.selectedUserId,
                    }
                })
                    .then(response => {
                        this.orders = response.data
                    })
            },

            clearFilters() {
                this.selectedStatus = ''
                this.selectedUserId = ''
                this.getContent()
            },

            formatDate(date) {
                if (date === null || date === undefined || date === '') {
                    return 'none'
                }
                let d = new Date(date),
                    month = '' + (d.getMonth() + 1),
                    day = '' + d.getDate(),
                    year = d.getFullYear();

                if (month.length < 2)
                    month = '0' + month;
                if (day.length < 2)
                    day = '0' + day;

                return [year, month, day].join('-');
            }
        },
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
