<template>
  <Page actionBarHidden="true">
    <StackLayout>
      <Image
        v-if="showSearchIcon"
        @tap="revealSearchBar"
        height="16"
        class="searchIcon"
        src="~/assets/images/search.png"
      />
      <SearchBar
        v-if="showSearchBar"
        hint="City, town or placename"
        v-model="location"
        @submit="getNewCinemas"
        height="30"
        class="search"
      />
      <label class="title" text="Cinemas" />
      <ActivityIndicator :busy="loading" height="20" />
      <label
        v-if="error"
        class="error"
        text="Sorry I couldn't find any cinemas"
      />
      <label v-if="error" class="error" text="Try searching somewhere else" />
      <ListView for="result in results" height="100%">
        <v-template>
          <card-view
            @tap="cinemaChosen(result)"
            class="cardStyle"
            margin="10"
            elevation="40"
            radius="1"
            height="200"
          >
            <StackLayout>
              <label class="cardContent" :text="result.name" textWrap="true" />
              <label
                class="footer"
                :text="`${result.address} ${result.postal_code}`"
                textWrap="true"
              />
              <label
                class="footer"
                :text="`${result._distance} km`"
                textWrap="true"
              />
            </StackLayout>
          </card-view>
        </v-template>
      </ListView>
    </StackLayout>
  </Page>
</template>

<script>
import axios from "axios";
import Vue from "nativescript-vue";

const API = "https://thelist-api.herokuapp.com/cinema-search";

// appSettings will be used to cache results
const appSettings = require("tns-core-modules/application-settings");

export default {
  name: "NewCinemsSearch",
  props: {
    newLocation: String
  },
  data() {
    return {
      cinemaID: String,
      results: [],
      loading: true,
      location: "",
      showSearchBar: false,
      showSearchIcon: true,
      error: false
    };
  },
  methods: {
    getCinemas(url) {
      axios
        .get(url, {
          params: {
            searchInput: `${this.newLocation} UK`
          }
        })
        .then(response => {
          this.results = response.data;
          appSettings.setString("cachedLocation", this.newLocation);
          appSettings.setString(
            "cachedNewSearchResults",
            JSON.stringify(response.data)
          );

          this.loading = false;
          this.error = false;
        })
        .catch(error => {
          this.loading = false;
          this.error = true;
        });
    },
    getNewCinemas() {
      this.results = [];
      this.loading = true;
      console.log("New search");
      axios
        .get(API, {
          params: {
            searchInput: `${this.location} UK`
          }
        })
        .then(response => {
          this.results = response.data;
          appSettings.setString(
            "cachedNewSearchResults",
            JSON.stringify(response.data)
          );
          this.loading = false;
          this.error = false;
        })
        .catch(error => {
          this.loading = false;
          this.error = true;
        });
    },
    // cinema data emitted to App.vue, so it can be used by MovieTimes
    cinemaChosen(cinema) {
      this.$emit("cinemaChosen", cinema);
    },
    revealSearchBar() {
      this.showSearchBar = true;
      this.showSearchIcon = false;
    }
  },
  mounted() {
    let d = new Date();
    let date = d.getDate() + "." + d.getMonth() + "." + d.getFullYear();

    if (
      this.newLocation == appSettings.getString("cachedLocation") &&
      date == appSettings.getString("cachedNewSearchDate")
    ) {
      // use cache
      this.results = JSON.parse(
        appSettings.getString("cachedNewSearchResults")
      );
      this.loading = false;
      console.log("Cache used");
    } else {
      // else perform axios request and set new values for cache
      this.getCinemas(API);
      console.log("New axios request");
      this.secondSearch = false;
      appSettings.setString("cachedNewSearchDate", date);
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
  margin-top: 2%;
  font-weight: bold;
}

.search {
  font-family: Josefin Sans, sans-serif;
  font-size: 15;
}

.searchIcon {
  margin-top: 3%;
}

.message {
  vertical-align: center;
  text-align: center;
  font-size: 20;
  color: #333333;
}

Page {
  background: linear-gradient(to bottom, hsl(171, 100%, 41%) 30%, #f3f3f3 0%);
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
  font-weight: bold;
}

.footer {
  padding-bottom: 5;
  text-align: center;
}

.error {
  font-size: 15;
  font-family: Josefin Sans, sans-serif;
  text-align: center;
  font-weight: bold;
}
</style>
