/* eslint-disable quotes */
<template>
  <div class="max-w-sm m-auto my-8">
    <div class="border p-10 border-grey-light shadow rounded">
      <h3 class="text-2xl mb-6 text-grey-darkest">Sign In</h3>
      <form @submit.prevent="signin">
        <div class="text-red" v-if="error">{{ error }}</div>
        <div class="mb-6">
          <label for="email" class="label">E-mail Address</label>
          <input type="email" v-model="email" class="input" id="email" placeholder="user@email.com" />
        </div>

        <div class="mb-6">
          <label for="password" class="label">Password</label>
          <input type="password" v-model="password" class="input" id="email" />
        </div>

        <button
          type="submit"
          class="bg-green-500 hover:bg-green-700 text-white font-sans font-bold py-2 px-4 border border-blue-700 rounded w-full"
        >Sign In</button>

        <div class="my-4">
          <router-link to="/signup" class="link">Sign up</router-link>
        </div>
      </form>
    </div>
  </div>
</template>

<script>
export default {
  name: "Signin",
  data() {
    return {
      email: "",
      password: "",
      error: ""
    };
  },
  created() {
    this.checkSignedIn();
  },
  updated() {
    this.checkSignedIn();
  },
  methods: {
    signin() {
      this.$http.plain
        .post("/signin", { email: this.email, password: this.password })
        .then(response => this.signInSuccessful(response))
        .catch(error => this.signinFailed(error));
    },
    signInSuccessful(response) {
      if (!response.data.csrf) {
        this.signinFailed(response);
        return;
      }
      localStorage.csrf = response.data.csrf;
      localStorage.signedIn = true;
      this.error = "";
      this.$router.replace("/records");
    },
    signinFailed(error) {
      this.error =
        (error.response && error.data && error.response.data.error) || "";
      delete localStorage.csrf;
      delete localStorage.signedIn;
    },
    checkSignedIn() {
      if (localStorage.signedIn) {
        this.$router.replace("/records");
      }
    }
  }
};
</script>
