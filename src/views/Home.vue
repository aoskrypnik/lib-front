<template>
  <v-container>
    <v-row>
      <v-col class="col-12 col-md-6 col-lg-3">
        <v-card>
          <v-card-title>
            Filters
          </v-card-title>
          <v-card-text>
            <v-checkbox
                class="mt-0"
                v-model="availableOnly"
                label="Available only"
                @change="getContent"
            >
            </v-checkbox>

            <v-text-field
                v-model="title"
                label="Title"
                outlined
                dense
                clearable
                @change="getContent"
            ></v-text-field>

            <v-autocomplete
                v-model="selectedAuthor"
                :items="authorNames"
                label="Author"
                dense
                outlined
                @change="getContent"
            ></v-autocomplete>

            <v-autocomplete
                v-model="selectedGenre"
                :items="genreNames"
                label="Genre"
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
          <v-col class="col-12 col-lg-6" v-for="book in books" :key="book.isbn">
            <Book :book="book" @delete-book="deleteBook"/>
          </v-col>
        </v-row>
        <div class="text-center mt-8">
          <v-pagination
              v-model="page"
              :length="totalPages"
              @input="getContent"
          ></v-pagination>
        </div>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>

import Book from "@/components/Book";
import axios from "axios";
import {mapGetters} from "vuex";

const endpoint = process.env.VUE_APP_ENDPOINT;
const size = 6;

export default {
  name: 'Home',
  components: {
    Book,
  },
  data() {
    return {
      books: [],
      availableOnly: false,
      title: '',
      selectedAuthor: '',
      selectedGenre: '',
      totalPages: 1,
      page: 1
    }
  },
  created() {
    this.title = this.$route.query.title
    this.page = +this.$route.query.page || 1
    this.selectedAuthor = this.$route.query.author || ''
    this.selectedGenre = this.$route.query.genre || ''
    this.availableOnly = this.$route.query.availableOnly === 'true'
    this.getContent()
  },
  methods: {
    deleteBook(isbn) {
      this.books = this.books.filter(book => book.isbn !== isbn)
    },
    getContent() {
      this.$router.push({
        query:
            {
              title: this.title,
              page: this.page,
              author: this.selectedAuthor,
              genre: this.selectedGenre,
              availableOnly: this.availableOnly,
            }
      });
      axios.get(`${endpoint}/books`, {
        params: {
          title: this.title,
          page: this.page - 1,
          author: this.selectedAuthor,
          genre: this.selectedGenre,
          availableOnly: this.availableOnly,
          size: size
        }
      })
          .then(response => {
            this.books = response.data.content
            this.totalPages = response.data.totalPages
          })
    },
    clearFilters() {
      this.title = ''
      this.selectedAuthor = ''
      this.selectedGenre = ''
      this.availableOnly = false
      this.page = 1
      this.getContent()
    }
  },
  computed: {
    ...mapGetters([
          'genresGetter',
          'authorsGetter'
        ]),
    genreNames: function () {
      return this.genresGetter.map(genre => genre.genreName)
    },
    authorNames: function () {
      return this.authorsGetter.map(author => author.authorName)
    }
  }
}
</script>
