<template>
  <div class="about">
    <div class="container">
      <h1>About Our Shop</h1>
    </div>
    <div class="container">
      <p class="description">Lorem ipsum dolor sit amet consectetur adipisicing elit. Eius quia aperiam eligendi dolorum reprehenderit ea amet, aliquid dolorem beatae, iste aliquam ullam. Sequi ab eligendi consectetur neque laudantium, libero asperiores.</p>
      <p class="description">Lorem ipsum dolor sit amet consectetur adipisicing elit. Eius quia aperiam eligendi dolorum reprehenderit ea amet, aliquid dolorem beatae, iste aliquam ullam. Sequi ab eligendi consectetur neque laudantium, libero asperiores.</p>
    </div>
  </div>
</template>

<style lang="scss" scoped>
  .description {
    margin-top: 50px;
  }
</style>

<script>
  export default {
    name: "About.vue",
    data() {
      return {
        products: [],
        addedToCart: [],
      }
    },
    mounted() {
      let intempt;
      window.addEventListener("intempt.intempt-demo.ready", function() {
       intempt = window._intempt.clients["98095539317252096"].track("About page", {
          timestamp: new Date().getTime(),
          "About page": "Visited",
        })
      });
      console.log(intempt)
    },
  }
</script>