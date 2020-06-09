<template>
    <div>
        <v-card
                class="product"
                v-for="product in productsFinal"
                :key="product.id"
        >
            <img class="product-image" :src="product.images">
            <v-card-title class="product-title">{{product.name}}</v-card-title>
            <v-row>
                <v-col class="pa-0">
                    <v-card-subtitle class="product-subtitle">{{product.description}}</v-card-subtitle>
                </v-col>
                <v-col class="pa-0" md="3">
                    <span class="product-price">${{ product.unit_amount / 100 }}</span>
                    <br/>
                    <span class="product-unit"> per night</span>
                </v-col>
            </v-row>
            <v-card-text class="product-address">
                <font-awesome-icon class="icon" :icon="['fas', 'map-marker-alt']" style="margin-right: 5px"/>
                {{product.address}}
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
                    {{ bookingItemName }}
                </v-card-title>

                <v-card-text>
                    {{ bookingItemPrice / 100 }}
                </v-card-text>

                <StripeElements
                        ref="elementsRef"
                        :pk="publishableKey"
                        :amount="amount"
                        locale="auto"
                        @token="tokenCreated"
                        @loading="loading = $event"
                >

                </StripeElements>

                <v-divider></v-divider>

                <v-card-actions class="justify-space-between">
                    <v-spacer></v-spacer>
                    <v-btn
                            color="error"
                            text
                            @click="bookingDialog = false"
                    >
                        Cancel
                    </v-btn>
                    <v-btn
                            color="primary"
                            text
                            @click="submit"
                    >
                        Book
                    </v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>
    </div>
</template>

<script>
    // Intempt Source ID Start
    const intemptSourceId = "103165180146221056"
    // Intempt Source ID End

    // Import the dependencies
    import { ContentType, Authorization } from "../../stripe_config.json";
    import { StripeElements } from 'vue-stripe-checkout';
    import axios from 'axios';
    import qs from 'querystring';
    const stripeProducts = "https://api.stripe.com/v1/products"
    const stripePrices = "https://api.stripe.com/v1/prices"
    const stripeCharges = "https://api.stripe.com/v1/charges";

    const stripeAuthHeader = {
        'Content-Type': ContentType,
        'Authorization': Authorization,
    }
    export default {
        name: "Products.vue",
        components: {
            StripeElements
        },
        data() {
            return {
                // Payment Variables
                loading: false,
                amount: 1000,
                publishableKey: "pk_test_ljAFYrnnGBQFZKlS3RfRsHXo00O5vWWmBk",
                token: null,
                description: null,
                charge: null,
                currency: 'eur',

                products: [],
                prices: [],
                productsFinal: [],
                bookingDialog: false,
                bookingItemName: null,
                bookingItemPrice: null,
            }
        },
        mounted() {
            // Intempt - Set category
            window.clients_category = 'Luxury Hotel Rooms';
            console.log(window.clients_category)
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

                const plansMerge = [...this.products.map((item, i) => {
                    return {
                        ...item,
                        ...item.metadata,
                        ...this.prices[i]
                    };
                })];
                this.productsFinal = plansMerge.filter(function (el) {
                    return el.active === true
                });
                console.log(this.productsFinal)
            });
        },
        methods: {
            // Intempt - Set product on "Book Now" click
            setProduct: function(id) {
                if (!this.bookingDialog) {
                    this.bookingDialog = true
                }
                for (let i = 0; i < this.productsFinal.length; i++) {
                    if (id == this.productsFinal[i].id) {
                        window.clients_product = {
                            name: this.productsFinal[i].name,
                            property1: this.productsFinal[i].unit_amount,
                            property2: this.productsFinal[i].id
                        };
                        console.log(window.clients_product)
                    }
                }
                this.bookingItemName = window.clients_product.name
                this.bookingItemPrice = window.clients_product.property1
            },
            submit () {
                this.$refs.elementsRef.submit();
            },
            tokenCreated (token) {
                this.token = token;
                // for additional charge objects go to https://stripe.com/docs/api/charges/object
                this.charge = {
                    source: token.id,
                    amount: this.amount, // the amount you want to charge the customer in cents. $100 is 1000 (it is strongly recommended you use a product id and quantity and get calculate this on the backend to avoid people manipulating the cost)
                    description: this.description, // optional description that will show up on stripe when looking at payments
                    currency: this.currency
                }
                this.sendTokenToServer(this.charge);
            },
            sendTokenToServer (charge) {
                const stripeAuthHeader = {
                    "Content-Type": ContentType,
                    "Authorization": Authorization
                };
                let data  = {
                    source: charge.source,
                    amount: charge.amount,
                    description: charge.description,
                    currency: charge.currency
                };
                axios.post(stripeCharges, qs.stringify(data), {
                    headers: stripeAuthHeader
                }).then(response => {
                    console.log(response)
                    // Intempt Custom Track - (Purchase Complete) on Axios Post Success
                    const intempt = window._Intempt.clients[intemptSourceId]
                    intempt.track("hotel-booking", {
                        "hotelRoomName": charge.description,
                        "roomPrice": charge.amount,
                    })
                    console.log(intempt)
                }).catch(error => {
                    console.log(error)
                    const intempt = window._Intempt.clients[intemptSourceId]
                    intempt.track("hotel-booking-fail", {
                        "hotelRoomName": charge.description,
                        "roomPrice": charge.amount,
                    })
                })
            }
        },
    }
</script>

<style scoped>
    .product {
        width: 350px;
        max-height: 430px;
        display: inline-block;
        justify-content: center;
        margin-left: 23px;
    }
    .product-image {
        width: 350px;
        max-height: 200px;
    }
    .product-subtitle {
        text-transform: uppercase;
        color: #3C49C3 !important;
        font-size: 10px !important;
    }
    .product-price {
        background-color: lightcoral;
        border-radius: 2px;
        padding: 5px 5px 5px 5px;
        font-size: 16px;
    }
    .product-unit {
        font-size: 13px;
    }
</style>