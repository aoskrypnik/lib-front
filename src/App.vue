<template>
  <v-app>
    <v-app-bar app>
      <v-container class="d-flex">
        <v-toolbar-title
            style="cursor: pointer"
            @click="$router.push('/')"
        >
          <v-icon left color="primary" class="d-none d-sm-inline-flex">mdi-bookshelf</v-icon>
          <v-icon color="primary" class="d-inline-flex d-sm-none">mdi-bookshelf</v-icon>
          <span class="d-none d-sm-inline-flex">Library</span>
        </v-toolbar-title>
        <v-spacer/>

        <v-btn
            v-if="roleGetter === 'READER'"
            class="mr-4"
            text
            @click="$router.push('/orders-list')"
        >
          <v-icon left class="d-none d-sm-inline-flex">mdi-history</v-icon>
          <v-icon class="d-inline-flex d-sm-none">mdi-history</v-icon>
          <span class="d-none d-sm-inline-flex">My orders</span>
        </v-btn>
        <v-btn
            v-if="roleGetter === 'ADMINISTRATOR'"
            class="mr-4"
            text
            @click="$router.push('/admin-list')"
        >
          <v-icon left class="d-none d-sm-inline-flex">mdi-basket-outline</v-icon>
          <v-icon class="d-inline-flex d-sm-none">mdi-basket-outline</v-icon>
          <span class="d-none d-sm-inline-flex">Orders</span>
        </v-btn>

        <v-badge
            v-if="roleGetter === 'READER' && storageNumGetter!==0"
            color="primary"
            overlap
            class="mr-4"
            :content="storageNumGetter"
        >
          <v-btn text @click="$router.push('/book-order')">
            <v-icon left class="d-none d-sm-inline-flex">mdi-basket-outline</v-icon>
            <v-icon class="d-inline-flex d-sm-none">mdi-basket-outline</v-icon>
            <span class="d-none d-sm-inline-flex">Current order</span>
          </v-btn>
        </v-badge>

        <!--        <v-btn-->
        <!--            class="mr-4"-->
        <!--            text-->
        <!--            @click="$router.push('/sprint-plan')"-->
        <!--        >-->
        <!--          Sprint plan-->
        <!--        </v-btn>-->
        <v-btn
            v-if="roleGetter === 'ADMINISTRATOR'"
            text
            class="mr-4"
            @click="$router.push('/save-book')"
        >
          <v-icon left class="d-none d-sm-inline-flex">mdi-book-plus-multiple-outline</v-icon>
          <v-icon class="d-inline-flex d-sm-none">mdi-book-plus-multiple-outline</v-icon>
          <span class="d-none d-sm-inline-flex">Add book</span>
        </v-btn>

        <div>
          <v-btn
              v-if="usernameGetter === null"
              class="mr-4"
              text
              color="primary"
              @click="$router.push('/login')"
          >
            <v-icon left class="d-none d-sm-inline-flex">mdi-account</v-icon>
            <v-icon class="d-inline-flex d-sm-none">mdi-account</v-icon>
            <span class="d-none d-sm-inline-flex">Login</span>
          </v-btn>
          <v-btn
              v-if="usernameGetter === null"
              color="primary"
              depressed
              @click="$router.push(`/registration`)"
          >
            Register
          </v-btn>
          <v-btn
              v-else
              class="mr-4"
              text
              color="error"
              @click="logoutAction"
          >
            <v-icon left class="d-none d-sm-inline-flex">mdi-logout</v-icon>
            <v-icon class="d-inline-flex d-sm-none">mdi-logout</v-icon>
            <span class="d-none d-sm-inline-flex">Logout</span>
          </v-btn>
        </div>

      </v-container>
    </v-app-bar>

    <v-main>
      <v-container>
        <router-view></router-view>
      </v-container>
    </v-main>

  </v-app>
</template>

<script>
import {mapActions, mapGetters, mapMutations} from "vuex";

export default {
  name: 'App',

  created() {
    this.userMutation({
      username: localStorage.getItem("username"),
      role: localStorage.getItem("role")
    })
    this.storageRestoreMutation()
    this.loadGenresAction()
    this.loadAuthorsAction()
  },

  computed: {
    ...mapGetters([
      'usernameGetter', 'roleGetter', 'genresGetter', 'authorsGetter', 'storageNumGetter'
    ]),
  },

  methods: {
    ...mapActions([
      'logoutAction',
      'loadGenresAction',
      'loadAuthorsAction',
    ]),
    ...mapMutations([
      'userMutation',
      'genresMutation',
      'authorsMutation',
      'storageRestoreMutation'
    ]),
  }
};
</script>
