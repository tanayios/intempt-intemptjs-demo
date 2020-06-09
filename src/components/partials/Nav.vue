<template>
    <v-app-bar
        app
        color="primary"
        dark
        height="50"
    >
        <v-layout justify-space-between>
            <router-link to="/">
                <img src="https://app.intempt.com/img/intempt_logo_white.0f3a5341.svg">
            </router-link>
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
        </v-layout>
    </v-app-bar>
</template>

<script>
    // Intempt Source ID Start
    const intemptSourceId = "103165180146221056"
    // Intempt Source ID End
    export default {
        name: 'Nav',
        mounted() {
            // Intempt identify user on page load if logged in
            const intempt = window._Intempt.clients[intemptSourceId];
            intempt.identify({'identifier' : this.$auth.user.id});
            console.log(intempt)
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