<template>
  <v-app-bar color="#0C0C0C00" flat dense dark app height="60">
    <v-container>
      <v-row>
        <v-btn
          color="#04040470"
          small
          fab
          id="left-chevron"
          class="mx-2 mt-2"
          @click="changeRoute(-1)"
          :disabled="backwardstate"
        >
          <v-icon>mdi-chevron-left</v-icon>
        </v-btn>

        <v-btn
          color="#04040470"
          small
          fab
          class="mx-2 mt-2"
          id="right-chevron"
          @click="changeRoute(1)"
          :disabled="backtimes == 0"
        >
          <v-icon>mdi-chevron-right</v-icon>
        </v-btn>
        <v-col cols="12" xs="12" sm="6" md="3" lg="4" justify class="mt-3">
          <v-text-field
            prepend-inner-icon="mdi-magnify"
            rounded
            placeholder="Search for artists or tracks"
            background-color="white"
            height="30"
            class="searchbar-pos"
            light
            id="search-bar"
            v-if="$store.state.searching"
            v-model="$route.params.id"
            v-on:input="searching()"
          >
          </v-text-field>
          <span>{{ this.$store.state.InputSearch }}</span>
        </v-col>

        <v-spacer></v-spacer>
        <v-col v-if="this.$route.path !== '/premium' && product !== 'premium' "
        lg="2" md="2" sm="4" xs="12" class="pr-0 mr-0">
          <v-btn
            v-if ="loggedIn && UserInfo.Type != 'premium'"
            rounded
            outlined
            class="mx-4"
            route
            to="/premium"
            id="upgrade-btn"
            >Upgrade</v-btn
          >
        </v-col>
        <v-col md="2" sm="4" xs="12" class="pr-0 mr-5" v-if="loggedIn" >
          <div class="text-center justify-end" id="user-btn">
            <v-menu offset-y id="user-menu">
              <template v-slot:activator="{ on }">
                <v-btn
                  rounded
                  depressed
                  color="#27272797"
                  v-on="on"
                  class=" text-user text-none ml-0"
                  small
                  id="inner-user-btn"
                  ><v-avatar size="25" class="mr-1 ml-0"
                    ><v-img :src="UserInfo.photo"></v-img
                  ></v-avatar>
                  <div>
                    {{ userName }}
                  </div>
                  <v-avatar size="20" class="mx-1">
                    <v-icon>mdi-chevron-down</v-icon>
                  </v-avatar>
                </v-btn>
              </template>

              <v-list dense color="grey darken-4" dark>
                <v-list-item
                  v-for="(item, index) in items"
                  :key="index"
                  :to="item.route"
                  :id="item.id"
                >
                  <v-list-item-title>{{ item.title }}</v-list-item-title>
                </v-list-item>
              </v-list>
            </v-menu>
          </div>
        </v-col>
      </v-row>
    </v-container>
  </v-app-bar>
</template>

<script>
import api from 'api-client';
/**
 * @vue-data {String} input - User Search Input
 * @vue-data {Number} backtimes - number of clicks on the back arrow now
 * @vue-data {Object} UserInfo - Info about the current User
 * @vue-data {Object} items - The Dropdown menu items
 * @vue-computed {String} userName
 * @vue-computed {Object} backwardstate
*/
export default {
  name: 'Topbar',
  /* istanbul ignore next */
  async created() {
    this.UserInfo.Type = await api.getCurrentUserProfile().then((Resp) => Resp.data.product);
    this.loggedIn = document.cookie.search(/loggedIn=.+/) !== -1;
    const currentUser = JSON.parse(localStorage.getItem('currentUser'));
    if (!currentUser) return;
    if (!currentUser.data.image) return;
    this.product = currentUser.data.product;
    if (!currentUser.data.image[0].url) return;
    this.UserInfo.photo = currentUser.data.image[0].url;
  },
  data: () => ({
    product: 'free',
    loggedIn: false,
    input: '',
    backtimes: 0,
    UserInfo: {
      Type: 'premium',
      name: 'John Doe',
      photo:
        'https://images.pexels.com/photos/2444429/pexels-photo-2444429.jpeg?auto=compress&cs=tinysrgb&dpr=1&w=500',
    },
    items: [
      {
        title: 'Account',
        route: '/account',
        id: 'account-item',
      },
      {
        title: 'Log Out',
        route: '/logout',
        id: 'logout-item',
      },
    ],
  }),
  methods: {
    /**
     * Changes the Route to forwad or backward for the Arrows
     */
    changeRoute(direction) {
      if (direction === -1) {
        this.backtimes += 1;
      } else { this.backtimes -= 1; }
      this.$router.go(direction);
    },
    /**
     * Changes the Route to Search for the Input
     */
    searching() {
      this.input = this.$route.params.id;
      this.$router.replace(`/home/search/${this.input}`);
    },
  },
  computed: {
    userName: {
      get() {
        let name;
        try {
          name = JSON.parse(localStorage.getItem('currentUser')).data.name;
        } catch (error) {
          name = this.$store.state.currentUser.username;
        }
        if (name === undefined) name = 'User';
        return name;
      },
    },
    backwardstate() {
      if (
        this.$store.state.prevRoute === '/login'
        || this.$store.state.prevRoute === '/signup'
        || this.$store.state.prevRoute === '/'
        || this.$store.state.prevRoute === '/account/overview'
      ) {
        return true;
      }
      return false;
    },
  },
};
</script>

<style scoped>
.text-user {
  color: white;
  font-weight: bold;
  float: right;
}
.searchbar-pos {
  transform: translate(0, -10px);
  position: fixed;
  max-width: 400px;
}
#upgrade-btn {
  border: 2px solid white;
  font-size: 12px;
  width: 70%;
  float: right;
}
#upgrade-btn:hover {
  background-color: rgba(43, 43, 43, 0.329);
}
#user-btn {
  display: flex;
  align-items: center;
}
#inner-user-btn {
  padding: 5px 3px 5px 3px;
  height: 90%;
}
.div {
  font-family: spotify-circular, spotify-circular-cyrillic,
    spotify-circular-arabic, spotify-circular-hebrew, Helvetica Neue, Helvetica,
    Arial, Hiragino Kaku Gothic Pro, Meiryo, MS Gothic, sans-serif;
}
</style>
