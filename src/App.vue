<template>
  <div class="body" id="app">
    <main>
      <router-view></router-view>
    </main>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: 'App',
  methods: {
    authenticateUser() {
        const urlBase = process.env.VUE_APP_API_URL;
        axios.post(urlBase + 'oauth/token', {
        grant_type: 'client_credentials',
        client_id: process.env.VUE_APP_API_ID,
        client_secret: process.env.VUE_APP_API_KEY,
        scope: '',
        })
        .then((response) => {
            localStorage.setItem('accessToken', response.data.access_token);
        });
        
    },
  },
  mounted() {
    this.authenticateUser();
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

</style>
