<template>
  <ListView
    v-if="results.length > 0"
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
          <StackLayout :class="['front', { active: showBack[index] === true }]">
            <StackLayout orientation="horizontal">
              <Image :src="images[index].poster" stretch="aspectFill"></Image>
              <Label class="cardContent" :text="result.name" textWrap="true" />
            </StackLayout>
            <ScrollView class="footer" orientation="horizontal">
              <StackLayout
                orientation="horizontal"
                horizontalAlignment="center"
              >
                <Label
                  class="times"
                  v-for="el in result.times"
                  :text="`${el.ts.slice(11, 16)}`"
                />
              </StackLayout>
            </ScrollView>
          </StackLayout>
          <StackLayout :class="['back', { active: showBack[index] === true }]">
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
</template>

<script>
import axios from "axios";
import Vue from "nativescript-vue";
import Image from "tns-core-modules/ui/image";

// socketIO is used for movie image and movie description requests
const SocketIO = require("nativescript-socket.io");
const socket = SocketIO.connect("https://movietime-server.herokuapp.com/");

export default {
  name: "MovieDay5",
  components: {},
  props: {
    unfilteredResults: Array
  },
  data: function() {
    return {
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
      error: false,
      results: Array
    };
  },
  watch: {},
  methods: {
    requestImages() {
      let movieNames = this.results.map(function(obj) {
        return obj.name;
      });
      // emits the movie names array to the server, so the server can search for movie posters
      socket.emit("request images", { data: movieNames });
    },
    refreshView() {
      this.$refs.listView.nativeView.refresh();
    },
    loaded() {
      this.requestImages;
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
  },
  mounted() {
    this.results = this.unfilteredResults[5];
    socket.on("image links", data => {
      // if (typeof data != "undefined" && data != undefined) {
      this.images = data;
      //  }
    });
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
