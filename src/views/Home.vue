<template>
    <div>
        <v-container>
            <v-row>
                <v-col class="col-md-3">
                    <v-card>
                        <v-card-title>
                            Фільтри
                        </v-card-title>
                        <v-card-text>
                            <v-form>
                                <v-checkbox
                                        v-model="isAvailableOnly"
                                        label="Тільки в наявності"
                                        @change="getContent"
                                >
                                </v-checkbox>
                                <v-autocomplete
                                        v-model="selectedAuthor"
                                        :items="authorNames"
                                        label="Автори"
                                        outlined
                                        chips
                                        small-chips
                                        @change="getContent"
                                ></v-autocomplete>

                                <v-autocomplete
                                        v-model="selectedGenre"
                                        :items="genreNames"
                                        label="Genres"
                                        outlined
                                        chips
                                        small-chips
                                        @change="getContent"
                                ></v-autocomplete>

                                <v-btn text block color="primary" @click="clearFilters">
                                    Clear filters
                                </v-btn>
                            </v-form>
                        </v-card-text>

                    </v-card>
                </v-col>

                <v-col class="col-md-9">
                    <v-row>
                        <v-col class="col-sm-12 col-md-6" v-for="book in books" :key="book.isbn">
                            <Book :book="book" @delete-book="deleteBook"/>
                        </v-col>
                    </v-row>
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
                selectedGenre: ''
            }
        },
        created() {
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
                            author: this.selectedAuthor,
                            genre: this.selectedGenre,
                            isAvailableOnly: this.isAvailableOnly,
                        }
                });
                axios.get(`${endpoint}/books`, {
                    params: {
                        author: this.selectedAuthor,
                        genre: this.selectedGenre,
                        isAvailableOnly: this.isAvailableOnly,
                    }
                })
                    .then(response => {
                        this.books = response.data
                        console.log(response.data)
                    })
            },
            clearFilters() {
                this.selectedAuthor = ''
                this.selectedGenre = ''
                this.isAvailableOnly = false
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
