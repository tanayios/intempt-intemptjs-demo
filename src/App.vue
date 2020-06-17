<template>
  <v-app>
    <Nav></Nav>
    <div class="main-div align-center">
      <router-view/>
    </div>
  </v-app>
</template>

<script>
  // Intempt Source ID Start
  const intemptSourceId = "103165180146221056"
  // Intempt Source ID End

  import Nav from './components/partials/Nav.vue';

  export default {
    name: 'App',

    components: {
      Nav,
    },
    data: () => ({
      //
    }),

    mounted() {
      // Intempt identify user on page load if logged in
      const intempt = window._Intempt.clients[intemptSourceId];
      intempt.identify({'identifier' : this.$auth.user.email});
      console.log(intempt)
    },
  };
</script>

<style>
  .main-div {
    margin-top: 50px;
  }
</style>
