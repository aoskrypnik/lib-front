<template>
  <v-card class="book-card">
    <div class="d-flex">
      <v-img
          :src="`${book.imageLink}`"
          max-width="150px"
          height="220px"
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
            <v-chip small outlined label class="chip"> {{ book.publishYear }} </v-chip>
            <v-chip small outlined label class="chip"> {{ book.publishCountry }}</v-chip>
            <v-chip small outlined label
                    color="danger" class="chip"
                    v-if="book.copiesNum === 0"
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
        <v-card-actions class="card-actions">
          <v-btn text class="ml-auto"
                 v-if="book.copiesNum === 0"
          >
            <v-icon left>mdi-bell-outline</v-icon>
            Повідомити
          </v-btn>
          <div v-else>
            <orderModal
                :book=book
                v-if="roleGetter==='READER'"
            ></orderModal>
            <div v-else-if="roleGetter==='ADMINISTRATOR'">
              <v-btn
                  class="ml-2"
                  text
                  @click="$router.push({ name: 'BookEditForm', params: {id: book.isbn }})"
              >
                <v-icon left>mdi-pencil</v-icon>
                Edit
              </v-btn>
              <deleteModal :book="book" @delete-book="deleteBook"></deleteModal>
            </div>
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
  computed: {
    ...mapGetters([
      'roleGetter'
    ]),
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

</style>