<template>
  <v-card class="book-card">
    <div class="d-flex">
      <v-img
          :src="`${book.imageLink}`"
          max-width="120px"
          max-height="220px"
      ></v-img>
      <div>
        <v-card-title> {{ book.title }}</v-card-title>
        <v-card-subtitle>
          <span v-for="author in book.authors" :key="author.id">
            {{ author.authorName }}<span v-if="author !== book.authors[book.authors.length-1]">,</span>
          </span>
        </v-card-subtitle>
        <v-card-text>
          <div>
            <v-chip
                small
                outlined
                label
                class="chip"
                v-for="genre in book.genres" :key="genre.id"
            > {{ genre.genreName }}
            </v-chip>
            <v-chip small outlined label class="chip"> {{ book.pagesNum }} pages</v-chip>
            <v-chip small outlined label class="chip"> {{ book.publishYear }}</v-chip>
            <v-chip small outlined label class="chip"> {{ book.publishCountry }}</v-chip>
          </div>
        </v-card-text>
        <v-card-actions class="card-actions">
          <v-btn
              text
              @click="removeBook()"
          >
            <v-icon left class="d-none d-sm-inline-flex">mdi-delete-outline</v-icon>
            <v-icon class="d-inline-flex d-sm-none">mdi-delete-outline</v-icon>
            <span class="d-none d-sm-inline-flex">Remove from order</span>
          </v-btn>
        </v-card-actions>
      </div>
    </div>
  </v-card>
</template>

<script>
import {mapMutations} from "vuex";

export default {
  name: "BookInfoCard",

  props: [
    'book'
  ],
  methods: {

    ...mapMutations([
      'storageRemoveMutation',
    ]),
    removeBook() {
      this.storageRemoveMutation({book: this.book})
    },
  }
}
</script>

<style scoped>
.book-card {
  overflow: hidden;
}

.chip {
  margin-right: 8px;
  margin-bottom: 8px;
}

.card-actions {
  padding: 0 16px 16px;
}

</style>