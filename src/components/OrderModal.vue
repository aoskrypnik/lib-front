<template>
  <div>
    <v-dialog
        v-model="dialog"
        width="500"
    >
      <template v-slot:activator="{ on, attrs }">
        <v-btn
            v-if="notContains()"
            text color="primary"
            v-bind="attrs"
            v-on="on" @click="addToStorage()"
            block
        >
          <v-icon left class="d-none d-sm-inline-flex">mdi-basket-outline</v-icon>
          <v-icon class="d-inline-flex d-sm-none">mdi-basket-outline</v-icon>
          <span class="d-none d-sm-inline-flex">Order</span>
        </v-btn>
        <v-btn
            v-else
            text
            @click="removeBook()"
        >
          <v-icon left class="d-none d-sm-inline-flex">mdi-delete-outline</v-icon>
          <v-icon class="d-inline-flex d-sm-none">mdi-delete-outline</v-icon>
          <span class="d-none d-sm-inline-flex">Remove from order</span>
        </v-btn>
      </template>

      <v-card>
        <v-card-title class="headline grey lighten-3">
          Order
        </v-card-title>

        <v-card-text class="my-3">
          Do you want to add more books or create order?
        </v-card-text>

        <v-divider></v-divider>

        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn
              color="primary"
              text
              @click="dialog = false"
          >
            Add more books
          </v-btn>
          <v-btn
              color="primary"
              depressed
              @click="$router.push('/book-order')"
          >
            Create order
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </div>
</template>

<script>
import {mapGetters, mapMutations} from "vuex";

export default {
  name: "OrderModal",
  props: ['book'],
  data() {
    return {
      dialog: false,
    }
  },
  methods: {
    ...mapMutations([
      'storageAddMutation',
    ]),
    addToStorage() {
      this.storageAddMutation({book: this.book})
    },
    notContains() {
      return !this.storageGetter.map(sb => sb.isbn).includes(this.book.isbn)
    },
    ...mapMutations([
      'storageRemoveMutation',
    ]),
    removeBook() {
      this.storageRemoveMutation({book: this.book})
    },
  },
  computed: {
    ...mapGetters([
      'storageGetter',
    ]),
  }
}
</script>

<style scoped>

</style>
