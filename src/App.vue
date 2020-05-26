<template>
  <v-app>
    <Nav></Nav>
    <div class="main-div align-center">
      <router-view/>
    </div>
  </v-app>
</template>

<script>
import Nav from './components/partials/Nav.vue';

export default {
  name: 'App',

  components: {
    Nav,
  },

  data: () => ({
    //
  }),
};
</script>

<style>
  .main-div {
    margin-top: 50px;
  }
</style>
