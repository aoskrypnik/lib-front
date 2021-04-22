<template>
  <v-card :disabled="roleGetter!=='ADMINISTRATOR' && !available" class="book-card" height="100%">
    <div class="d-flex">
      <v-img
          :src="`${book.imageLink}`"
          max-width="150px" contain
          class="book-cover"
      ></v-img>
      <div>
        <v-card-title> {{ book.title }}</v-card-title>
        <v-card-subtitle>
          <span v-for="author in book.authors" :key="author.id">
            {{ author.authorName }}<span v-if="author !== book.authors[book.authors.length-1]">,</span>
          </span>
        </v-card-subtitle>
        <v-card-text>
          <div class="chip-group">
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
            <v-chip small outlined label
                    color="error" class="chip"
                    v-if="!available"
            >
              Not available
            </v-chip>
            <v-chip small outlined label
                    color="success" class="chip"
                    v-else
            >
              Available
            </v-chip>
          </div>
        </v-card-text>
        <v-spacer></v-spacer>
        <v-card-actions class="card-actions">
          <orderModal
              :book=book
              v-if="roleGetter==='READER' && available"
          ></orderModal>
          <div v-else-if="roleGetter==='ADMINISTRATOR'">
            <v-btn
                class="ml-2"
                text
                @click="$router.push({ name: 'BookEditForm', params: {id: book.isbn }})"
            >
              <v-icon left class="d-none d-sm-inline-flex">mdi-pencil</v-icon>
              <v-icon class="d-inline-flex d-sm-none">mdi-pencil</v-icon>
              <span class="d-none d-sm-inline-flex">Edit</span>
            </v-btn>
            <deleteModal :book="book" @delete-book="deleteBook"></deleteModal>
          </div>
        </v-card-actions>
      </div>
    </div>
  </v-card>
</template>

<script>
import OrderModal from "@/components/OrderModal";
import DeleteModal from "@/components/DeleteModal";
import {mapGetters} from "vuex";

export default {
  name: "Book",

  components: {
    OrderModal,
    DeleteModal
  },

  props: [
    'book'
  ],

  data() {
    return {
      available: true
    }
  },

  created() {
    this.available = this.book.copies.map(copy => copy.isAvailable).some(x => x === true)
  },

  computed: {
    ...mapGetters([
      'roleGetter'
    ])
  },

  methods: {
    deleteBook(isbn) {
      console.log(isbn)
      this.$emit("delete-book", isbn)
    }
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

.chip-group {
  min-height: 64px;
}

</style>
