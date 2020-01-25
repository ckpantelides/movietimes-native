<template>
  <component
    :is="currentComponent"
    @cinemaChosen="loadMovieTimes"
    @navigateHome="reloadCinemas"
    @newCinemaSearch="newCinemaSearch"
    :IDtoSearch="cinemaIDprop"
    :newLocation="newLocation"
    :cinemaName="cinemaNameProp"
  ></component>
</template>

<script>
import Vue from "nativescript-vue";
import Cinemas from "../components/Cinemas.vue";
import MovieTimes from "../components/MovieTimes.vue";
import NewCinemaSearch from "../components/NewCinemaSearch.vue";

// imported to access back Android backbutton
import {
  AndroidApplication,
  AndroidActivityBackPressedEventData
} from "application";
import * as application from "application";

// allows card-view element (cards are used as box shadow is not available)
Vue.registerElement(
  "CardView",
  () => require("@nstudio/nativescript-cardview").CardView
);

// prevents default back button behaviour
application.android.on(AndroidApplication.activityBackPressedEvent, data => {
  data.cancel = true;
});

export default {
  name: "app",
  components: {
    Cinemas,
    MovieTimes,
    NewCinemaSearch
  },
  data() {
    return {
      currentComponent: Cinemas,
      cinemaIDprop: String,
      newLocation: String,
      cinemaNameProp: String,
      homeAfterNewSearch: false
    };
  },
  methods: {
    loadMovieTimes(payload) {
      this.cinemaIDprop = payload.place_id;
      this.cinemaNameProp = payload.name;
      this.currentComponent = MovieTimes;
    },
    reloadCinemas() {
      if (this.homeAfterNewSearch === true) {
        this.currentComponent = NewCinemaSearch;
      } else this.currentComponent = Cinemas;
    },
    newCinemaSearch(data) {
      this.newLocation = data;
      this.homeAfterNewSearch = true;
      this.currentComponent = NewCinemaSearch;
    }
  }
};
</script>

<style scoped>
.title {
  font-family: Josefin Sans, sans-serif;
  font-size: 30;
  color: white;
  margin-top: 0;
  text-align: center;
  margin-top: 5%;
  font-weight: bold;
}

.message {
  vertical-align: center;
  text-align: center;
  font-size: 20;
  color: #333333;
}

Page {
  background: linear-gradient(to bottom, #53ba82 30%, #f3f3f3 0%);
  height: 100%;
  margin: 0;
  padding: 0;
}

.cardStyle {
  background-color: #fff;
  color: rgb(43, 43, 43);
  overflow: auto;
  font-family: Josefin Sans, sans-serif;
}

.cardContent {
  padding: 20;
  font-size: 15;
  font-family: Josefin Sans, sans-serif;
  text-align: center;
}
</style>
