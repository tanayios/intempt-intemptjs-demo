<template>
    <v-app-bar
        app
        color="primary"
        dark
        height="50"
    >
        <v-layout>
            <router-link to="/">
                <img src="https://app.intempt.com/img/intempt_logo_white.0f3a5341.svg">
            </router-link>

            <v-spacer></v-spacer>

            <v-btn text @click="ratingDialog = true" style="margin-right: 20px">Rate the Restaurant</v-btn>

            <div v-if="!$auth.loading">
                <a v-if="!$auth.isAuthenticated" @click="login" class="white--text">Sign in</a>
                <div v-if="$auth.isAuthenticated">
                    <a @click="logout" class="white--text">Log out</a>
                    <v-avatar width="25" height="auto">
                        <img :src="$auth.user.picture">
                    </v-avatar>
                    <span class="white--text">{{ $auth.user.name }}</span>
                </div>
            </div>

            <div v-else>
                <v-progress-circular
                        indeterminate
                        color="primary"
                ></v-progress-circular>
            </div>
        </v-layout>

        <v-dialog v-model="ratingDialog" width="300">
            <v-card>
                <v-card-title class="justify-center">Rate the Restaurant</v-card-title>
                <br/>
                <v-card-subtitle class="text-center">Rate your experience below</v-card-subtitle>
                <v-card-actions class="justify-center">
                    <v-rating hover v-model="rating">
                        <template v-slot:item="props">
                            <v-icon
                                    :color="props.isFilled ? 'blue' : 'grey lighten-1'"
                                    @click="onRatingChange(props)"
                            >
                                mdi-star
                            </v-icon>
                        </template>
                    </v-rating>
                </v-card-actions>
            </v-card>
        </v-dialog>

    </v-app-bar>
</template>

<script>
    // Intempt Source ID Start
    const intemptSourceId = "103165180146221056"
    // Intempt Source ID End

    export default {
        name: 'Nav',
        data() {
            return {
                ratingDialog: false,
                rating: 0,
            }
        },
        methods: {
            // Log the user in
            login() {
                this.$auth.loginWithRedirect();
            },
            // Log the user out
            logout() {
                this.$auth.logout({
                    returnTo: window.location.origin
                });
            },
            onRatingChange(props) {
                this.rating = props.index + 1
                const intempt = window._Intempt.clients[intemptSourceId]
                intempt.track("restaurant-rating", {
                    "guestName": this.$auth.user.name,
                    "guestEmail": this.$auth.user.email,
                    "starsRated": this.rating
                })
                console.log(intempt)
                this.ratingDialog = false
            }
        }
    }
</script>

<style lang="scss" scoped>
    a {
        font-weight: bold;
        text-decoration: none;
        color: rgba(206, 22, 96, 0.38);
        &.router-link-exact-active {
            color: rgba(82, 35, 69, 0.74);
        }
    }
    img {
        max-width: 100%;
        height: 30px;
        vertical-align: middle;
        border-style: none;

    }
    .icon {
        margin-right: 10px;
    }
</style>