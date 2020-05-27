<template>
    <v-app-bar
        app
        color="primary"
        dark
        height="50"
    >
        <img src="https://www.intempt.com/assets/images/intempt/Intempt_Whale_Logotype_Black@3x.png">
        <v-divider vertical></v-divider>
        <v-layout justify-space-between>
            <div>
                <font-awesome-icon class="icon" :icon="['fas', 'home']"/>
                <router-link to="/">Home</router-link>
            </div>
            <v-card>
                <div v-if="!$auth.loading">
                    <a v-if="!$auth.isAuthenticated" @click="login"><strong>Sign in</strong></a>

                    <div v-if="$auth.isAuthenticated">
                        <a @click="logout"><strong>Log out</strong></a>
                        <v-avatar width="25" height="auto">
                            <img :src="$auth.user.picture">
                        </v-avatar>
                        {{ $auth.user.name }}
                    </div>
                </div>
            </v-card>
            <div>
                <font-awesome-icon class="icon" :icon="['fas', 'shopping-cart']"/>
                <router-link to="/checkout">Checkout</router-link>
            </div>
        </v-layout>
    </v-app-bar>
</template>

<script>
    export default {
        name: 'Nav',
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