<template>
  <div>
    <v-row>
      <v-col>
        <v-slide-group
          v-if="$fetchState.pending"
          v-model="model"
          class="pa-4"
          active-class="success"
          show-arrows="always"
        >
          <v-slide-item v-for="n in 20" :key="n">
            <v-skeleton-loader
              class="ma-2"
              type="card"
              height="150"
              width="100"
            ></v-skeleton-loader>
          </v-slide-item>
        </v-slide-group>
        <v-slide-group
          v-else
          v-model="model"
          class="pa-4"
          active-class="success"
          show-arrows="always"
        >
          <v-slide-item v-for="item in slideItems.results" :key="item.id">
            <v-card class="ma-2" width="100" :to="`/tv/${item.id}`">
              <v-row align="center" justify="center">
                <v-col>
                  <v-img
                    id="poster"
                    height="100%"
                    :src="`https://image.tmdb.org/t/p/w154${item.poster_path}`"
                    :lazy-src="
                      `https://image.tmdb.org/t/p/w92${item.poster_path}`
                    "
                  />
                </v-col>
              </v-row>
            </v-card>
          </v-slide-item>
        </v-slide-group>
      </v-col>
    </v-row>
    <v-row class="my-2">
      <v-col cols="2">
        <span class="text-h4"><strong>Popular</strong></span>
      </v-col>
      <v-col>
        <v-btn-toggle
          v-model="toggleList"
          rounded
          mandatory
          color="primary"
          class="ml-6"
        >
          <v-btn>
            <span>Movie</span>
          </v-btn>

          <v-btn>
            <span>TV Shows</span>
          </v-btn>
        </v-btn-toggle>
      </v-col>
    </v-row>
    <v-row>
      <v-col>
        <div v-if="$fetchState.pending" class="text-center">
          <v-progress-circular
            indeterminate
            color="primary"
          ></v-progress-circular>
        </div>
        <div v-else>
          <masonry
            :cols="{ default: 4, 1000: 3, 700: 2, 400: 1 }"
            :gutter="{ default: '30px', 700: '15px' }"
          >
            <div v-for="(item, index) in items.results" :key="index.id">
              <LazyMovieThumbnail v-if="toggleList == 0" :movieObject="item" />
              <LazyTvThumbnail v-if="toggleList == 1" :tvObject="item" />
            </div>
          </masonry>
        </div>
      </v-col>
    </v-row>
  </div>
</template>

<script>
export default {
  data() {
    return {
      model: null,
      slideItems: [],
      toggleList: 0,
      isLoading: false,
      items: {}
    };
  },
  async fetch() {
    this.slideItems = await fetch(
      "https://api.themoviedb.org/3/tv/popular?api_key=" +
        process.env.API_KEY +
        "&language=en-US&page=1"
    ).then(res => res.json());

    // this.items = await fetch(
    //   "https://api.themoviedb.org/3/movie/popular?api_key=" +
    //     process.env.API_KEY +
    //     "&language=en-US&page=1"
    // ).then(res => res.json());
  },
  mounted() {
    this.isLoading = true;
    setTimeout(() => (this.isLoading = false), 1000);
    this.firstLoadList();

    this.$watch(
      vm => [vm.toggleList],
      async () => {
        if (this.toggleList == 0) {
          this.$fetch();
          this.items = await fetch(
            "https://api.themoviedb.org/3/movie/popular?api_key=" +
              process.env.API_KEY +
              "&language=en-US&page=1"
          ).then(res => res.json());
        } else if (this.toggleList == 1) {
          this.$fetch();
          this.items = await fetch(
            "https://api.themoviedb.org/3/tv/popular?api_key=" +
              process.env.API_KEY +
              "&language=en-US&page=1"
          ).then(res => res.json());
        }
      }
    );
  },

  methods: {
    async firstLoadList() {
      this.items = await fetch(
        "https://api.themoviedb.org/3/movie/popular?api_key=" +
          process.env.API_KEY +
          "&language=en-US&page=1"
      ).then(res => res.json());
    }
  }
};
</script>

<style scoped>
#poster:after {
  content: "See Detail";
  color: #fff;
  font-weight: bolder;
  display: flex;
  justify-content: center;
  align-items: center;
  position: absolute;
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
  background: rgba(0, 0, 0, 0.6);
  opacity: 0;
  transition: all 0.5s;
  -webkit-transition: all 0.5s;
}
#poster:hover:after {
  cursor: pointer;
  opacity: 1;
}
</style>
