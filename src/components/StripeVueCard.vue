<template>
    <div>
        <stripe-elements
                ref="elementsRef"
                :pk="publishableKey"
                :amount="amount"
                locale="auto"
                @token="tokenCreated"
                @loading="loading = $event"
        >

        </stripe-elements>
    </div>
</template>

<script>
    import { StripeElements } from 'vue-stripe-checkout';
    import axios from 'axios';
    import qs from 'querystring';
    let stripeCharges = "https://api.stripe.com/v1/charges";
    import { ContentType, Authorization } from "../../stripe_config.json";

    export default {
        components: {
            StripeElements
        },
        props: {
            cardOptions: {
                type: Object,
                default: () => ({})
            }
        },
        data: () => ({
            loading: false,
            amount: 1000,
            publishableKey: "pk_test_ljAFYrnnGBQFZKlS3RfRsHXo00O5vWWmBk",
            token: null,
            description: null,
            charge: null,
            currency: 'eur'
        }),
        methods: {
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
                    let intempt = window._Intempt.clients["100393177026797568"]
                    intempt.track("purchase-complete", {
                        "items": charge.description,
                        "totalprice": charge.amount,
                        "ispaid": true,
                        "timestamp": new Date().getTime(),
                        "intempt.visit.trackcharge": charge.amount
                    })
                    console.log(intempt)

                })
            }
        }
    }
</script>