<template>
  <v-app>
    <v-content class="black" app>
      <div class="wrap cover">
        <div class="cover svg-photo">
          <v-app-bar color="black" class="trans-bar">
            <!-- Controller for nav Drawer -->
            <v-btn
              @click.stop="drawer = !drawer"
              v-if="$vuetify.breakpoint.xs"
              depressed
            >
              <v-icon>mdi-view-sequential</v-icon>
            </v-btn>
            <router-link to="/">
              <v-img
                src="../../assets/imgs/El-8alaba.png"
                max-height="200"
                max-width="200"
                id="logo-img"
                class="mt-4"
              ></v-img>
            </router-link>
            <v-spacer v-if="$vuetify.breakpoint.smAndUp"></v-spacer>
            <!-- List if Normal Desktops -->
            <ul v-if="$vuetify.breakpoint.smAndUp">
              <v-btn
                v-for="i in buttons.length"
                :key="i"
                class="btn-green text-none"
                type="li"
                left
                text
                large
                :id="buttons[i - 1].id"
                :to="buttons[i - 1].route"
              >
                {{ buttons[i - 1].name }}</v-btn
              >
            </ul>
          </v-app-bar>
          <!-- Drawer for mobile and Tablets-->
          <v-navigation-drawer
            v-if="$vuetify.breakpoint.xs"
            v-model="drawer"
            absolute
            bottom
            temporary
          >
            <v-list nav dense>
              <v-list-item-group
                v-for="i in buttons.length"
                :key="i"
                class="btn-green text-none"
              >
                <v-list-item :to="buttons[i - 1].route">
                  <v-list-item-title>{{
                    buttons[i - 1].name
                  }}</v-list-item-title>
                </v-list-item>
              </v-list-item-group>
            </v-list>
          </v-navigation-drawer>
          <div class="text-main cover">
            <v-container fill-height>
              <v-row>
                <v-col
                  cols="12"
                  justify="center"
                  class="font-weight-bold "
                  id="welcome-text-1"
                  :class="{
                    'display-1': $vuetify.breakpoint.xs,
                    'display-3': $vuetify.breakpoint.sm,
                    'display-4': $vuetify.breakpoint.mdAndUp,
                  }"
                >
                  Music for everyone.
                </v-col>
                <v-col
                  justify="center"
                  id="welcome-text-2"
                  :class="{
                    title: $vuetify.breakpoint.smAndDown,
                    'display-1': $vuetify.breakpoint.mdAndUp,
                  }"
                  class="margin-bottom-small"
                >
                  Millions of songs. Free for all.
                </v-col>
                <v-col cols="12">
                  <v-btn
                    to="/download"
                    color="#1db954"
                    class="text-main"
                    rounded
                    depressed
                    large
                    id="Big-download-btn"
                  >
                    Get spotify el8alaba free
                  </v-btn>
                </v-col>
              </v-row>
            </v-container>
          </div>
        </div>
      </div>
    </v-content>
    <v-footer app absolute dark class="trans-bar">
      <v-btn to="/about">About</v-btn>
      <v-btn to="/help">Help</v-btn>
      <v-spacer></v-spacer>
      <div>&copy; {{ new Date().getFullYear() }}</div>
    </v-footer>
  </v-app>
</template>

<script>
import cookies from '@/store/modules/auth/cookies';
/**
 * @vue-data {Booelan} drawer - Flag for the Navigation drawer
 * @vue-data {Array} Buttons - Array of Objects of the Buttons
 */
export default {
  name: 'Landing',
  /* istanbul ignore next */
  // Re-route to home if a user is logged in
  beforeRouteEnter(to, from, next) {
    next(() => {
      // Find the loggedIn cookie
      const loggedIn = document.cookie.search(/loggedIn=.+/) !== -1;
      if (loggedIn) {
        next('/home');
      } else {
        // Remove the current user
        // Remove the loggedIn cookie
        // Continue
        cookies.clearData(['currentUser'], ['loggedIn']);
        next();
      }
    });
  },

  data: () => ({
    buttons: [
      {
        name: 'DOWNLOAD',
        route: '/download',
        id: 'download-btn',
      },
      {
        name: 'PREMIUM',
        route: '/premium',
        id: 'premium-btn',
      },
      {
        name: 'LOGIN',
        route: '/login',
        id: 'login-btn',
      },
      {
        name: 'SIGN UP',
        route: '/signup',
        id: 'signup-btn',
      },
    ],
    drawer: false,
  }),
};
</script>

<style scoped>
.svg-photo {
  background-image: url('https://www.scdn.co/i/home/hero-burst-mobile.svg');
  background-size: cover;
}
.wrap {
  background-color: #f037a5;
  background: -webkit-gradient(
    linear,
    left top,
    left bottom,
    color-stop(-60%, #f037a5),
    color-stop(140%, #fae62d)
  );
  background: linear-gradient(#f037a5 -60%, #fae62d 140%);
}
.cover {
  width: 100%;
  height: 100%;
}
.text-main {
  color: #ffffff;
  text-align: center;
}
.margin-bottom-small {
  margin-bottom: 5%;
}
.trans-bar {
  opacity: 0.7;
  position: fixed;
}
.btn-green {
  color: #ffffff;
}
.btn-green:hover {
  color: #20d15e;
}
#small-button {
  width: 10px;
}
</style>
