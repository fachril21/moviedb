<template>
  <div>
    <div>
      <v-row>
        <v-col cols="4">
          <v-select
            v-if="category"
            :items="category.list"
            :item-text="'text'"
            :item-value="'value'"
            v-model="selected"
            solo
            dense
          ></v-select>
        </v-col>
      </v-row>
    </div>
    <div>
      <div v-if="$fetchState.pending">
        <v-overlay :z-index="zIndex" :value="overlay">
          <v-progress-circular indeterminate color="primary">
          </v-progress-circular>
        </v-overlay>
      </div>
      <p v-else-if="$fetchState.error">Error while fetching mountains</p>
      <div v-else>
        <div>
          <div class="text-center">
            <v-pagination
              v-model="page"
              :length="movies.total_pages"
              :total-visible="7"
            ></v-pagination>
          </div>
        </div>
        <masonry
          :cols="{ default: 4, 1000: 3, 700: 2, 400: 1 }"
          :gutter="{ default: '30px', 700: '15px' }"
        >
          <div v-for="(movie, index) in movies.results" :key="index.id">
            <LazyMovieThumbnail :movieObject="movie" />
          </div>
        </masonry>
        <div>
          <div class="text-center">
            <v-pagination
              v-model="page"
              :length="movies.total_pages"
              :total-visible="7"
            ></v-pagination>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import MovieThumbnail from "./MovieThumbnail.vue";
import NoSSR from "vue-no-ssr";
export default {
  data() {
    return {
      overlay: true,
      zIndex: 0,
      movies: [],
      selected: "top_rated",
      page: 1,
      category: {
        list: [
          { value: "top_rated", text: "Top Rated" },
          { value: "popular", text: "Popular" },
          { value: "now_playing", text: "Now Playing" },
          { value: "upcoming", text: "Upcoming" }
        ],
        top_rated: {
          id: 1,
          value: "Top Rated",
          api_endpoint:
            "https://api.themoviedb.org/3/movie/top_rated?api_key=" +
            process.env.API_KEY +
            "&language=en-US&page="
        },
        popular: {
          id: 2,
          value: "Popular",
          api_endpoint:
            "https://api.themoviedb.org/3/movie/popular?api_key=" +
            process.env.API_KEY +
            "&language=en-US&page="
        },
        now_playing: {
          id: 3,
          value: "Now Playing",
          api_endpoint:
            "https://api.themoviedb.org/3/movie/now_playing?api_key=" +
            process.env.API_KEY +
            "&language=en-US"
        },
        upcoming: {
          id: 4,
          value: "Upcoming",
          api_endpoint:
            "https://api.themoviedb.org/3/movie/upcoming?api_key=" +
            process.env.API_KEY +
            "&language=en-US&page="
        }
      }
    };
  },
  components: {
    MovieThumbnail,
    "no-ssr": NoSSR
  },
  mounted() {
    if (typeof this.$redrawVueMasonry === "function") {
      this.$redrawVueMasonry();
    }

    this.$watch(
      vm => [vm.page],
      async () => {
        this.$fetch();
        this.movies = await fetch(
          this.category[this.selected].api_endpoint.concat(this.page)
        ).then(res => res.json());
      }
    );

    this.$watch(
      vm => [vm.selected],
      async () => {
        this.$fetch();
        this.page = 1;
        this.movies = await fetch(
          this.category[this.selected].api_endpoint.concat(this.page)
        ).then(res => res.json());
      }
    );
  },
  async fetch() {
    this.movies = await fetch(
      "https://api.themoviedb.org/3/movie/top_rated?api_key=" +
        process.env.API_KEY +
        "&language=en-US&page=1"
    ).then(res => res.json());
  }
};
</script>
