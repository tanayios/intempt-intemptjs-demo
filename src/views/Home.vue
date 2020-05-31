<template>
  <div class="home">
    <section>
        <h1>
          Intempt Hotel Demo
        </h1>
        <h2>
          Book a Room
        </h2>
      <products></products>
    </section>
  </div>
</template>
<script>
  import Products from "../components/Products";
  export default {
    name: 'home',
    components: {
      Products,
    }
  }
</script>

<style lang="scss" scoped>
    h1 {
        text-align: center;
    }
    h2 {
        text-align: center;
    }

</style>