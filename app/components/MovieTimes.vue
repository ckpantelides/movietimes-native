<template>
  <Page class="movie-times" actionBarHidden="true" ref="pageView">
    <StackLayout>
      <Image
        @tap="navigateHome"
        height="18"
        class="homeIcon"
        src="~/assets/images/home.png"
      />
      <Label class="title" text="Movie Times" />
      <ScrollView class="footer" orientation="horizontal" width="90%">
        <StackLayout orientation="horizontal" horizontalAlignment="center">
          <Label
            @tap="chooseMoviesDay('MoviesDay0')"
            :class="[
              'day',
              {
                active:
                  currentComponent === 'MoviesDay0' ||
                  this.componentOpenedFirstTime === true
              }
            ]"
            text="Today"
          />
          <Label
            @tap="chooseMoviesDay('MoviesDay1')"
            :class="['day', { active: currentComponent === 'MoviesDay1' }]"
            text="Tomorrow"
          />
          <Label
            @tap="chooseMoviesDay('MoviesDay2')"
            :class="['day', { active: currentComponent === 'MoviesDay2' }]"
            :text="daysAfterToday(2)"
          />
          <Label
            @tap="chooseMoviesDay('MoviesDay3')"
            :class="['day', { active: currentComponent === 'MoviesDay3' }]"
            :text="daysAfterToday(3)"
          />
          <Label
            @tap="chooseMoviesDay('MoviesDay4')"
            :class="['day', { active: currentComponent === 'MoviesDay4' }]"
            :text="daysAfterToday(4)"
          />
          <Label
            @tap="chooseMoviesDay('MoviesDay5')"
            :class="['day', { active: currentComponent === 'MoviesDay5' }]"
            :text="daysAfterToday(5)"
          />
          <Label
            @tap="chooseMoviesDay('MoviesDay6')"
            :class="['day', { active: currentComponent === 'MoviesDay6' }]"
            :text="daysAfterToday(6)"
          />
        </StackLayout>
      </ScrollView>

      <Label
        class="cinema-name"
        :text="cinemaName"
        horizontalAlignment="center"
      />
      <ActivityIndicator :busy="loading" height="20" />
      <component
        v-if="unfilteredResults.length > 0"
        :is="currentComponent"
        v-bind:unfilteredResults="unfilteredResults"
      ></component>
    </StackLayout>
  </Page>
</template>

<script>
import axios from "axios";
import Vue from "nativescript-vue";
import MoviesDay0 from "../components/MoviesDay0.vue";
import MoviesDay1 from "../components/MoviesDay1.vue";
import MoviesDay2 from "../components/MoviesDay2.vue";
import MoviesDay3 from "../components/MoviesDay3.vue";
import MoviesDay4 from "../components/MoviesDay4.vue";
import MoviesDay5 from "../components/MoviesDay5.vue";
import MoviesDay6 from "../components/MoviesDay6.vue";
import MoviesDayBlank from "../components/MoviesDayBlank.vue";

const API = "https://thelist-api.herokuapp.com/filmtimesapp";
// const API = "http://localhost:8000/filmtimesapp";

export default {
  name: "MovieTimes",
  props: {
    IDtoSearch: String,
    cinemaName: String
  },
  components: {
    MoviesDay0,
    MoviesDay1,
    MoviesDay2,
    MoviesDay3,
    MoviesDay4,
    MoviesDay5,
    MoviesDay6,
    MoviesDayBlank
  },
  data: function() {
    return {
      currentComponent: MoviesDayBlank,
      // Boolean for the styling/underlining of "Today"
      componentOpenedFirstTime: true,
      loading: true,
      unfilteredResults: []
    };
  },
  methods: {
    navigateHome() {
      this.$emit("navigateHome");
    },
    daysAfterToday(n) {
      var d = new Date();
      var daysAfter = parseInt(d.getDay() + n);
      var days = [
        "Sunday",
        "Monday",
        "Tuesday",
        "Wednesday",
        "Thursday",
        "Friday",
        "Saturday",
        "Sunday",
        "Monday",
        "Tuesday",
        "Wednesday",
        "Thursday",
        "Friday",
        "Saturday"
      ];
      return days[daysAfter];
    },
    getMovies(url) {
      axios
        .get(url, {
          params: {
            cinemaID: this.IDtoSearch
          }
        })
        .then(response => {
          this.loading = false;
          this.unfilteredResults = response.data;

          if (response.data.length > 0) {
            this.currentComponent = MoviesDay0;
          }
        })
        .catch(error => {
          this.error = true;
          console.log("Error with film times request");
          this.loading = false;
        });
    },
    chooseMoviesDay(chosenComponent) {
      this.currentComponent = chosenComponent;
      this.componentOpenedFirstTime = false;
      this.refreshView;
    },
    refreshView() {
      this.$refs.pageView.nativeView.refresh();
    }
  },
  mounted() {
    this.getMovies(API);
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

.homeIcon {
  margin-top: 3%;
}

.day {
  color: white;
  font-family: Josefin Sans, sans-serif;
  padding: 5;
}

.cinema-name {
  color: white;
  font-family: Josefin Sans, sans-serif;
  font-style: italic;
  padding-bottom: 0;
  margin-bottom: 0;
}

.day.active {
  text-decoration: underline;
}

.message {
  vertical-align: center;
  text-align: center;
  font-size: 20;
  color: #333333;
}

Page {
  background: linear-gradient(to bottom, hsl(204, 86%, 53%) 30%, #f3f3f3 0%);
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
