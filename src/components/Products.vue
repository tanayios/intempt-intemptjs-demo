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
                    {{ selectedRoom.name }}
                </v-card-title>

                <v-card-text class="text-center">
                    {{ selectedRoom.description }}
                </v-card-text>

                <div class="image-center">
                    <v-checkbox
                            v-model="spa"
                            label="Add full day Hotel SPA access"
                            false-value="0"
                            true-value="3000"
                            @change="interestedSpa"
                    >
                    </v-checkbox>
                    <img width="180" height="auto" src="../assets/images/spa.jpg" style="margin-left: 40px">
                </div>

                <br/>

                <StripeElements
                        ref="elementsRef"
                        :pk="publishableKey"
                        :amount="selectedRoom.price"
                        locale="auto"
                        @token="tokenCreated"
                        @loading="loading = $event"
                >

                </StripeElements>

                <v-divider></v-divider>

                <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn
                            color="primary"
                            text
                            @click="submit"
                    >
                        Book for ${{ selectedRoom.price / 100}} <span v-if="spa === '3000'"> + ${{ spa / 100 }}</span>
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
                publishableKey: "pk_test_ljAFYrnnGBQFZKlS3RfRsHXo00O5vWWmBk",
                token: null,
                charge: null,
                currency: 'eur',

                products: [],
                prices: [],
                productsFinal: [],
                bookingDialog: false,

                selectedRoom: {
                    id: "",
                    name: "",
                    description: "",
                    price: "",
                    images: "",
                },

                spaInterest: false,
                spa: "0",
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
                        this.selectedRoom.id = this.productsFinal[i].id
                        this.selectedRoom.name = this.productsFinal[i].name
                        this.selectedRoom.description = this.productsFinal[i].description
                        this.selectedRoom.price = this.productsFinal[i].unit_amount
                        this.selectedRoom.images = this.productsFinal[i].images
                        window.clients_product = {
                            name: this.selectedRoom.name,
                            property1: this.selectedRoom.price,
                            property2: this.selectedRoom.id
                        };
                        console.log(window.clients_product)
                    }
                }
            },
            submit: function() {
                this.$refs.elementsRef.submit();
            },
            tokenCreated: function(token) {
                this.token = token;
                // for additional charge objects go to https://stripe.com/docs/api/charges/object
                this.charge = {
                    source: token.id,
                    amount: this.selectedRoom.price + parseInt(this.spa), // the amount you want to charge the customer in cents. $100 is 1000 (it is strongly recommended you use a product id and quantity and get calculate this on the backend to avoid people manipulating the cost)
                    description: this.selectedRoom.name, // optional description that will show up on stripe when looking at payments
                    currency: this.currency
                }
                this.sendTokenToServer(this.charge);
            },
            sendTokenToServer: function(charge) {
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
                    // eslint-disable-next-line no-unused-vars
                }).then(response => {
                    // Intempt Custom Track - (Purchase Complete) on Axios Post Success
                    const intempt = window._Intempt.clients[intemptSourceId]
                    intempt.track("hotel-booking", {
                        "guestName": this.$auth.user.name,
                        "guestEmail": this.$auth.user.email,
                        "hotelRoomName": this.selectedRoom.name,
                        "hotelRoomDesc": this.selectedRoom.description,
                        "roomPrice": charge.amount,
                        "spaInterest": this.spaInterest,
                        "spa": parseInt(this.spa),
                    })
                    console.log(intempt)
                    // eslint-disable-next-line no-unused-vars
                }).catch(error => {
                    const intempt = window._Intempt.clients[intemptSourceId]
                    intempt.track("hotel-booking-fail", {
                        "guestName": this.$auth.user.name,
                        "guestEmail": this.$auth.user.email,
                        "hotelRoomName": this.selectedRoom.name,
                        "hotelRoomDesc": this.selectedRoom.description,
                        "roomPrice": charge.amount,
                        "spaInterest": this.spaInterest,
                        "spa": parseInt(this.spa),
                    })
                })
            },
            interestedSpa: function() {
                this.spaInterest = true
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
    .image-center {
        display: flex;
        align-items: center;
        justify-content: center;
    }
</style>