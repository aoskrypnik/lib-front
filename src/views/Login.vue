<template>
  <v-container>
    <v-row>
      <v-col class="col-md-6 mx-auto">
        <v-card>
          <v-toolbar dark flat color="primary">
            <v-toolbar-title>Login</v-toolbar-title>
          </v-toolbar>
          <v-card-text>
            <v-form
                ref="form"
                v-model="valid"
                lazy-validation
            >
              <v-alert
                  border="top"
                  color="red lighten-2"
                  dark
                  v-if="!validCreds"
              >
                Credentials are not valid :(
              </v-alert>

              <v-text-field
                  @click="deleteAlert"
                  v-model="username"
                  :rules="rules"
                  label="Username"
                  prepend-icon="mdi-account"
                  required
              ></v-text-field>

              <v-text-field
                  @click="deleteAlert"
                  v-model="password"
                  :rules="rules"
                  :type="'password'"
                  label="Password"
                  prepend-icon="mdi-lock"
                  required
              ></v-text-field>

              <div class="d-flex">
                <v-btn
                    :disabled="!valid"
                    color="primary"
                    depressed
                    class="ml-auto"
                    @click="submit"
                >
                  Log in
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
import {mapActions} from "vuex";

export default {
  name: "Login",

  data: () => ({
    valid: false,
    username: '',
    password: '',
    rules: [
      v => !!v || 'Is required',
    ],
    validCreds: true,
    redirect: ''
  }),

  created() {
    this.redirect = this.$route.params.redirect
  },

  methods: {
    ...mapActions([
      'loginAction',
    ]),
    submit() {
      if (this.$refs.form.validate()) {
        this.loginAction({username: this.username, password: this.password})
            .then(promiseResponse => this.validCreds = promiseResponse)
            .catch(promiseResponse => this.validCreds = promiseResponse)
      }
    },
    deleteAlert() {
      this.validCreds = true
    }
  },
}
</script>