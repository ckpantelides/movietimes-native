<template>
  <StackLayout>
    <ActivityIndicator :busy="loading" height="20" />
    <label v-if="error" class="error" text="Sorry I couldn't find any movies" />
    <label
      v-if="error"
      class="error"
      text="Please check your internet connection"
    />
    <ListView
      ref="listView"
      @loaded="loaded"
      for="(result, index) in results"
      height="100%"
    >
      <v-template>
        <card-view
          @tap="flip(index)"
          class="cardStyle"
          margin="10"
          elevation="40"
          radius="1"
          height="200"
        >
          <StackLayout>
            <StackLayout
              :class="['front', { active: showBack[index] === true }]"
            >
              <StackLayout orientation="horizontal">
                <Image :src="images[index].poster" stretch="aspectFill"></Image>
                <Label
                  class="cardContent"
                  :text="result.name"
                  textWrap="true"
                />
              </StackLayout>
              <ScrollView class="footer" orientation="horizontal">
                <StackLayout
                  orientation="horizontal"
                  horizontalAlignment="center"
                >
                  <Label
                    class="times"
                    v-for="el in result.schedules[0].performances"
                    :text="`${el.ts.slice(11, 16)}`"
                  />
                </StackLayout>
              </ScrollView>
            </StackLayout>
            <StackLayout
              :class="['back', { active: showBack[index] === true }]"
            >
              <Label
                class="cardContent"
                :text="images[index].blurb"
                textWrap="true"
              />
            </StackLayout>
          </StackLayout>
        </card-view>
      </v-template>
    </ListView>
  </StackLayout>
</template>

<script>
import axios from "axios";
import Vue from "nativescript-vue";
import Image from "tns-core-modules/ui/image";

// localStorage will be used to cache results from API requests
import localStorage from "nativescript-localstorage";

// socketIO is used for movie image and movie description requests
const SocketIO = require("nativescript-socket.io");
const socket = SocketIO.connect("https://movietime-server.herokuapp.com/");

const API = "https://thelist-api.herokuapp.com/filmtimes";

export default {
  name: "MovieDay1",
  components: {},
  props: {
    IDtoSearch: String
  },
  data: function() {
    return {
      results: [],
      unfilteredResults: [],
      images: [
        {
          poster: "~/assets/images/placeholder.png",
          blurb: "Description loading..."
        },
        {
          poster: "~/assets/images/placeholder.png",
          blurb: "Description loading..."
        },
        {
          poster: "~/assets/images/placeholder.png",
          blurb: "Description loading..."
        },
        {
          poster: "~/assets/images/placeholder.png",
          blurb: "Description loading..."
        },
        {
          poster: "~/assets/images/placeholder.png",
          blurb: "Description loading..."
        },
        {
          poster: "~/assets/images/placeholder.png",
          blurb: "Description loading..."
        },
        {
          poster: "~/assets/images/placeholder.png",
          blurb: "Description loading..."
        },
        {
          poster: "~/assets/images/placeholder.png",
          blurb: "Description loading..."
        },
        {
          poster: "~/assets/images/placeholder.png",
          blurb: "Description loading..."
        },
        {
          poster: "~/assets/images/placeholder.png",
          blurb: "Description loading..."
        },
        {
          poster: "~/assets/images/placeholder.png",
          blurb: "Description loading..."
        },
        {
          poster: "~/assets/images/placeholder.png",
          blurb: "Description loading..."
        },
        {
          poster: "~/assets/images/placeholder.png",
          blurb: "Description loading..."
        },
        {
          poster: "~/assets/images/placeholder.png",
          blurb: "Description loading..."
        },
        {
          poster: "~/assets/images/placeholder.png",
          blurb: "Description loading..."
        },
        {
          poster: "~/assets/images/placeholder.png",
          blurb: "Description loading..."
        },
        {
          poster: "~/assets/images/placeholder.png",
          blurb: "Description loading..."
        },
        {
          poster: "~/assets/images/placeholder.png",
          blurb: "Description loading..."
        },
        {
          poster: "~/assets/images/placeholder.png",
          blurb: "Description loading..."
        },
        {
          poster: "~/assets/images/placeholder.png",
          blurb: "Description loading..."
        },
        {
          poster: "~/assets/images/placeholder.png",
          blurb: "Description loading..."
        },
        {
          poster: "~/assets/images/placeholder.png",
          blurb: "Description loading..."
        },
        {
          poster: "~/assets/images/placeholder.png",
          blurb: "Description loading..."
        },
        {
          poster: "~/assets/images/placeholder.png",
          blurb: "Description loading..."
        },
        {
          poster: "~/assets/images/placeholder.png",
          blurb: "Description loading..."
        },
        {
          poster: "~/assets/images/placeholder.png",
          blurb: "Description loading..."
        },
        {
          poster: "~/assets/images/placeholder.png",
          blurb: "Description loading..."
        },
        {
          poster: "~/assets/images/placeholder.png",
          blurb: "Description loading..."
        },
        {
          poster: "~/assets/images/placeholder.png",
          blurb: "Description loading..."
        },
        {
          poster: "~/assets/images/placeholder.png",
          blurb: "Description loading..."
        },
        {
          poster: "~/assets/images/placeholder.png",
          blurb: "Description loading..."
        }
      ],
      showBack: [
        false,
        false,
        false,
        false,
        false,
        false,
        false,
        false,
        false,
        false,
        false,
        false,
        false,
        false,
        false,
        false,
        false,
        false,
        false,
        false,
        false,
        false,
        false,
        false,
        false,
        false,
        false,
        false,
        false,
        false
      ],
      loading: true,
      error: false
    };
  },
  watch: {
    images: function(val) {
      // when the images are received via socketio, this view will be reloaded
      this.refreshView();
    }
  },
  methods: {
    getMovies(url) {
      axios
        .get(url, {
          params: {
            cinemaID: this.IDtoSearch
          }
        })
        .then(response => {
          this.unfilteredResults = response.data;
          this.loading = false;
          this.filterResults();
        })
        .catch(error => {
          this.error = true;
          this.loading = false;
        });
    },
    filterResults() {
      // Adds 1 day to today's date
      let longDate = new Date(new Date().getTime() + 1 * 24 * 60 * 60 * 1000);
      var formattedDate = longDate.toISOString().slice(0, 10);

      // only include performances that match the relevant date (above)
      let filteredPerformances = this.unfilteredResults.map(function(
        CompareWithDate
      ) {
        CompareWithDate.schedules[0].performances = CompareWithDate.schedules[0].performances.filter(
          x => x.ts.slice(0, 10) === formattedDate
        );
        return CompareWithDate;
      });
      // filters out films without any performances on the relevant date
      this.results = filteredPerformances.filter(
        obj => obj.schedules[0].performances.length > 0
      );
      this.requestImages();
    },
    requestImages() {
      let movieNames = [];
      let arr = this.results;
      // get an array of the movie array
      for (let i = 0; i < arr.length; i++) {
        movieNames.push(arr[i].name);
      }
      // emits the movie names array to the server, so the server can search for movie posters
      socket.emit("request images", { data: movieNames });
    },
    refreshView() {
      this.$refs.listView.nativeView.refresh();
    },
    loaded() {
      // this.buildUrl();
      this.getMovies(API);
      socket.on("image links", data => {
        if (typeof data != "undefined" && data != undefined) {
          this.images = data;
        }
      });
    },
    flip(i) {
      if (this.showBack[i] === false) {
        this.showBack[i] = true;
        this.refreshView();
      } else if (this.showBack[i] === true) {
        this.showBack[i] = false;
        this.refreshView();
      }
    }
  }
};
</script>

<style scoped>
.title {
  font-family: Josefin Sans, sans-serif;
  font-size: 20;
  color: white;
  margin-top: 0;
  text-align: center;
  margin-top: 5%;
  font-weight: bold;
  padding-left: 5;
  padding-right: 5;
}

.message {
  vertical-align: center;
  text-align: center;
  font-size: 20;
  color: #333333;
}

.times {
  padding-bottom: 10;
  padding-right: 10;
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
  font-weight: bold;
  text-align: center;
}

.front {
  visibility: visible;
}
.front.active {
  visibility: collapse;
}

.back {
  visibility: collapse;
}

.back.active {
  visibility: visible;
}

.day.active {
  font-size: 16;
}

image {
  height: 50;
  width: 50;
  margin-left: 5;
}

.error {
  font-size: 15;
  font-family: Josefin Sans, sans-serif;
  text-align: center;
  font-weight: bold;
}
</style>
