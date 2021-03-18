<template>
  <div>
    <v-container>
      <v-row>
        <v-col class="col-12 col-md-6 col-lg-3">
          <v-card>
            <v-card-title>
              Filters
            </v-card-title>
            <v-card-text>
              <v-form>
                <v-checkbox
                    v-model="isAvailableOnly"
                    label="Available only"
                    @change="getContent"
                >
                </v-checkbox>
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
              </v-form>
            </v-card-text>
          </v-card>
        </v-col>

        <v-col class="col-12 col-md-6 col-lg-9">
          <v-row>
            <v-col class="col-12 col-md-12 col-lg-6" v-for="book in books" :key="book.isbn">
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
  </div>
</template>

<script>

import Book from "@/components/Book";
import axios from "axios";
import {mapGetters} from "vuex";

const endpoint = process.env.VUE_APP_ENDPOINT;
const size = 2;

export default {
  name: 'Home',
  components: {
    Book,
  },
  data() {
    return {
      books: [],
      isAvailableOnly: false,
      selectedAuthor: '',
      selectedGenre: '',
      totalPages: 1,
      page: 1
    }
  },
  created() {
    this.page = +this.$route.query.page || 1
    this.selectedAuthor = this.$route.query.author || ''
    this.selectedGenre = this.$route.query.genre || ''
    this.isAvailableOnly = this.$route.query.isAvailableOnly === 'true'
    this.getContent()
  },
  methods: {
    deleteBook(isbn) {
      this.books = this.books.filter(book => book.isbn !== isbn)
    },
    getContent() {
      console.log(this.isAvailableOnly)
      this.$router.push({
        query:
            {
              page: this.page,
              author: this.selectedAuthor,
              genre: this.selectedGenre,
              isAvailableOnly: this.isAvailableOnly,
            }
      });
      axios.get(`${endpoint}/books`, {
        params: {
          page: this.page - 1,
          author: this.selectedAuthor,
          genre: this.selectedGenre,
          isAvailableOnly: this.isAvailableOnly,
          size: size
        }
      })
          .then(response => {
            this.books = response.data.content
            this.totalPages = response.data.totalPages
            console.log(response.data)
          })
    },
    clearFilters() {
      this.selectedAuthor = ''
      this.selectedGenre = ''
      this.isAvailableOnly = false
      this.page = 1
      this.getContent()
    }
  },
  computed: {
    ...
        mapGetters([
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
