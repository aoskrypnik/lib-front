<template>
  <v-dialog
      v-model="dialog"
      width="500"
  >

    <template v-slot:activator="{ on, attrs }">
      <v-btn
          text
          color="error"
          class="ml-2"
          v-bind="attrs"
          v-on="on"
      >
        <v-icon left class="d-none d-sm-inline-flex">mdi-trash-can-outline</v-icon>
        <v-icon class="d-inline-flex d-sm-none">mdi-trash-can-outline</v-icon>
        <span class="d-none d-sm-inline-flex">Delete</span>
      </v-btn>
    </template>

    <v-card>
      <v-card-title class="headline grey lighten-3">
        Delete book
      </v-card-title>

      <v-card-text class="my-3">
        Do you really want to delete "{{ book.title }}"?
      </v-card-text>

      <v-divider></v-divider>

      <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn
            text
            @click="dialog = false"
        >
          No, cancel
        </v-btn>
        <v-btn
            color="error"
            text
            @click="deleteBook(book.isbn)"
        >
          Yes, delete
        </v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>

<script>
import axios from "axios";

const endpoint = process.env.VUE_APP_ENDPOINT;

export default {
  name: "DeleteModal",
  data() {
    return {
      dialog: false,
    }
  },
  props: [
    'book'
  ],
  methods: {
    deleteBook(isbn) {
      axios.delete(`${endpoint}/books/${isbn}`)
          .then(response => {
            this.$emit("delete-book", isbn)
            console.log(response)
          })
          .catch(err => console.log(err))
      this.dialog = false
    }
  }
}
</script>
