<template>
    <v-card>
        <v-card-title>Checkout</v-card-title>
        <StripeVueCard></StripeVueCard>
    </v-card>
</template>

<script>
    import StripeVueCard from "../components/StripeVueCard";
    export default {
        name: "Checkout",
        components: {
            StripeVueCard,
        }
    }
</script>

<style scoped>

</style>