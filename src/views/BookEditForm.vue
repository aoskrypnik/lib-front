<template>
  <v-container>
    <v-row>
      <v-col class="col-md-6 mx-auto">
        <v-card>
          <v-toolbar dark flat color="primary">
            <v-toolbar-title>Edit book</v-toolbar-title>
          </v-toolbar>
          <v-card-text>
            <v-form ref="form">
              <v-text-field
                  disabled
                  v-model="isbn"
                  label="ISBN"
                  :rules="[v => !!v || 'isbn is required']"
                  required></v-text-field>

              <v-text-field
                  v-model="title"
                  label="Title"
                  required></v-text-field>

              <v-autocomplete
                  v-model="selectedAuthors"
                  :items="authorNames"
                  label="Authors"
                  outlined
                  chips
                  small-chips
                  multiple
                  required></v-autocomplete>

              <v-autocomplete
                  v-model="selectedGenres"
                  :items="genreNames"
                  label="Genres"
                  outlined
                  chips
                  small-chips
                  multiple
                  required></v-autocomplete>

              <div class="mb-6">
                <v-slider
                    v-model="copiesNum"
                    class="align-center"
                    label="Number of copies"
                    :min="1"
                    :max="50"
                    hide-details
                >
                  <template v-slot:append>
                    <v-text-field
                        v-model="copiesNum"
                        class="mt-0 pt-0"
                        hide-details
                        single-line
                        type="number"
                        style="width: 60px"
                    ></v-text-field>
                  </template>
                </v-slider>

                <v-slider
                    v-model="publishYear"
                    class="align-center"
                    label="Publish year"
                    :min="-2000"
                    :max="2021"
                    hide-details
                >
                  <template v-slot:append>
                    <v-text-field
                        v-model="publishYear"
                        class="mt-0 pt-0"
                        hide-details
                        single-line
                        type="number"
                        style="width: 60px"
                    ></v-text-field>
                  </template>
                </v-slider>

                <v-slider
                    v-model="pagesNum"
                    class="align-center"
                    label="Number of pages"
                    :min="1"
                    :max="3000"
                    hide-details
                >
                  <template v-slot:append>
                    <v-text-field
                        v-model="pagesNum"
                        class="mt-0 pt-0"
                        hide-details
                        single-line
                        type="number"
                        style="width: 60px"
                    ></v-text-field>
                  </template>
                </v-slider>
              </div>

              <v-autocomplete
                  v-model="publishCountry"
                  :items="countries"
                  label="Country of publishing"
                  outlined
                  required></v-autocomplete>

              <v-autocomplete
                  v-model="language"
                  :items="languages"
                  label="Language"
                  outlined
                  required></v-autocomplete>

              <v-file-input
                  @change="handleImageInput"
                  v-model="image"
                  accept=".jpg, .png"
                  label="Upload image"
                  filled
                  prepend-icon="mdi-camera"
              ></v-file-input>
              <v-img style="height: 200px" v-if="imageLink" :src="imageLink" contain/>

              <div class="d-flex mt-4">
                <v-btn
                    class="ml-auto"
                    @click="submit"
                    depressed
                    color="primary">
                  Save
                </v-btn>
              </div>
            </v-form>
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import axios from "axios";
import countries from "countries-list"
import {mapGetters} from "vuex";

const endpoint = process.env.VUE_APP_ENDPOINT;

export default {
  name: "BookEditForm",
  data: () => ({
    bookData: null,
    isbn: '',
    title: '',
    selectedAuthors: [],
    // authors: [],
    // authorNames: [],
    selectedGenres: [],
    // genres: [],
    // genreNames: [],
    copiesNum: 0,
    publishYear: 0,
    pagesNum: 0,
    countries: [],
    publishCountry: '',
    languages: [],
    language: '',
    image: null,
    imageLink: null
  }),
  created() {
    if (this.$route.params.id !== undefined) {
      axios.get(`${endpoint}/books/${this.$route.params.id}`)
          .then(response => {
            this.isbn = response.data.isbn
            this.title = response.data.title
            this.selectedAuthors = response.data.authors.map(author => author.authorName)
            this.selectedGenres = response.data.genres.map(genre => genre.genreName)
            this.copiesNum = response.data.copiesNum
            this.publishYear = response.data.publishYear
            this.pagesNum = response.data.pagesNum
            this.publishCountry = response.data.publishCountry
            this.language = response.data.language
            this.imageLink = response.data.imageLink
          })
    } else {
      this.$router.push('/')
    }
    this.countries = Object.entries(countries.countries).map(item => item[1].name)
    this.languages = Object.entries(countries.languages).map(item => item[1].name)

  },
  computed: {
    ...mapGetters([
      'genresGetter',
      'authorsGetter',
    ]),
    genreNames: function () {
      return this.genresGetter.map(genre => genre.genreName)
    },
    authorNames: function () {
      return this.authorsGetter.map(author => author.authorName)
    }
  },
  methods: {
    submit() {
      if (this.$refs.form.validate()) {
        let book = {
          isbn: this.isbn,
          title: this.title,
          authors: this.selectedAuthors.map(authorName => {
                return {id: this.authorsGetter.find(item => item.authorName === authorName).id}
              }
          ),
          genres: this.selectedGenres.map(genreName => {
                return {id: this.genresGetter.find(item => item.genreName === genreName).id}
              }
          ),
          copiesNum: this.copiesNum,
          publishYear: this.publishYear,
          pagesNum: this.pagesNum,
          publishCountry: this.publishCountry,
          language: this.language,
        }
        let formData = new FormData();
        formData.append('image', this.image)
        formData.append('book', JSON.stringify(book))
        axios.put(`${endpoint}/books/${this.isbn}`, formData, {
          headers: {
            'Content-Type': 'multipart/form-data'
          }
        }).then(response => {
          console.log(response)
          this.$router.push(`/`)
        })
      }
    },
    handleImageInput() {
      if (this.image) {
        this.imageLink = URL.createObjectURL(this.image)
      } else {
        this.imageLink = null
      }
    }
  }
}
</script>