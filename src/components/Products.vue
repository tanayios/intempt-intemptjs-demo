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
                <v-select
                        label="Guests"
                        :items="[1,2,3,4,5,6]"
                >
                </v-select>
                <v-layout row wrap>
                    <v-menu
                            v-model="fromDateMenu"
                            :close-on-content-click="false"
                            transition="scale-transition"
                            offset-y
                    >
                        <template v-slot:activator="{ on }">
                            <v-text-field
                                    :label="todayDate"
                                    readonly
                                    :value="fromDateDisplay"
                                    v-on="on"
                                    height="50"
                            ></v-text-field>
                        </template>
                        <v-date-picker
                                show-current
                                v-model="fromDateVal"
                                no-title
                                @input="fromDateMenu = false"
                        ></v-date-picker>
                    </v-menu>
                </v-layout>
                <v-btn
                        color="primary"
                        @click="setProduct(product.id)"
                        :disabled="!$auth.isAuthenticated"
                >
                    Book Now
                </v-btn>
            </v-card-actions>
        </v-card>
    </div>
</template>

<script>
    import axios from 'axios';
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
        data() {
            return {
                products: [],
                addedToCart: [],
                fromDateMenu: false,
                fromDateVal: null,
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
            // Intempt - Set product on "Add to Cart" click
            setProduct: function(id) {
                for (let i = 0; i < this.products.length; i++) {
                    if (id == this.products[i].id) {
                        window.clients_product = {
                            name: this.products[i].name,
                            property1: this.products[i].description,
                            property2: this.products[i].id
                        };
                        console.log(window.clients_product)
                    }
                }
            }
        },
        computed: {
            todayDate() {
                let todayDate = new Date()
                return todayDate.getFullYear() + "-" + todayDate.getMonth() + "-" + todayDate.getDay()
            },
            fromDateDisplay() {
                return this.fromDateVal;
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