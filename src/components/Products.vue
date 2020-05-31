<template>
    <div>
        <v-card
                class="product"
                v-for="product in products"
                :key="product.id"
        >
            <img class="product-image" :src="product.images">
            <v-card-title class="product-title">{{product.name}}</v-card-title>
            <v-card-subtitle class="product-subtitle">{{product.description}}</v-card-subtitle>
            <v-card-text class="product-address">
                <font-awesome-icon class="icon" :icon="['fas', 'map-marker-alt']" style="margin-right: 5px"/>
                {{product.metadata.address}}
            </v-card-text>
            <v-spacer></v-spacer>
            <v-divider></v-divider>
            <v-card-actions class="justify-center">
                <v-btn
                            color="primary"
                            @click="setProduct(product.id)"
                            :disabled="!$auth.isAuthenticated"
                            :retain-focus="false"
                    >
                        Book Now
                </v-btn>
            </v-card-actions>
        </v-card>

        <v-dialog
                v-model="bookingDialog"
                width="500"
        >
            <v-card>
                <v-card-title
                        class="headline grey lighten-2 justify-center"
                        primary-title
                >
                    Book the {{ bookingItemName }}
                </v-card-title>

                <v-card-text>
                    {{ bookingItemDesc }}
                </v-card-text>

                <StripeVueCard></StripeVueCard>

                <v-divider></v-divider>

                <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn
                            class="justify-lg-end"
                            color="primary"
                            text
                            @click="pay"
                    >
                        Book Now
                    </v-btn>
                    <v-btn
                            color="error"
                            text
                            @click="bookingDialog = false"
                    >
                        Cancel
                    </v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>
    </div>
</template>

<script>
    import axios from 'axios';
    import StripeVueCard from "./StripeVueCard";

    // Import the Stripe configuration
    import { ContentType, Authorization } from "../../stripe_config.json";
    const stripeProducts = "https://api.stripe.com/v1/products"
    let stripePrices = "https://api.stripe.com/v1/prices"
    const stripeAuthHeader = {
        'Content-Type': ContentType,
        'Authorization': Authorization,
    }
    export default {
        name: "Products.vue",
        components: {
            StripeVueCard
        },
        data() {
            return {
                products: [],
                bookingDialog: false,
                bookingItemName: null,
                bookingItemDesc: null,
                bookingItemPrice: null,
            }
        },
        mounted() {
            // Intempt - Set category
            window.clients_category = 'Luxury Hotel Rooms';
            console.log(window.clients_category)

            console.log(this.products)

            for (let i = 0; i < this.products.length; i++) {
                console.log("ok")
                for (let k = 0; k < this.prices.length; k++) {
                    console.log("ok")
                    if (this.products[i].id === this.prices[k].product) {
                        console.log("id")
                    }
                }
            }
        },
        beforeMount() {
            axios.get(stripeProducts, {
                headers: stripeAuthHeader
            }).then(response => {
                this.products = response.data.data
            });

            axios.get(stripePrices, {
                headers: stripeAuthHeader
            }).then(response => {
                this.prices = response.data.data
            });
        },
        methods: {
            // Intempt - Set product on "Book Now" click
            setProduct: function(id) {
                if (!this.bookingDialog) {
                    this.bookingDialog = true
                }
                for (let i = 0; i < this.products.length; i++) {
                    if (id == this.products[i].id) {
                        window.clients_product = {
                            name: this.products[i].name,
                            property1: this.products[i].description,
                            property2: this.products[i].id
                        };
                        console.log(window.clients_product.name)
                    }
                }
                this.bookingItemName = window.clients_product.name
                this.bookingItemDesc = window.clients_product.property1
            }
        },
    }
</script>

<style scoped>
    .product {
        width: 350px;
        max-height: 400px;
        display: inline-block;
        justify-content: center;
        margin-left: 23px;
    }
    .product-image {
        width: 350px;
        max-height: 200px;
    }
    .product-title {
    }
    .product-subtitle {
        text-transform: uppercase;
        color: #3C49C3 !important;
        font-size: 10px !important;
    }
</style>