<template>
  <div>
    <div v-if="$fetchState.pending">
      <v-sheet class="pa-3">
        <v-skeleton-loader
          class="mx-auto"
          max-width="300"
          type="card"
        ></v-skeleton-loader>
      </v-sheet>
    </div>
    <div v-else-if="$fetchState.error">Error while fetching mountains</div>
    <v-card
      v-else
      :loading="loading"
      class="mx-auto my-6 tv-card"
      max-width="300"
      elevation="0"
      :to="'/tv/' + tvObject.id"
    >
      <template slot="progress">
        <v-progress-linear
          color="deep-purple"
          height="10"
          indeterminate
        ></v-progress-linear>
      </template>
      <v-img
        id="poster"
        :src="'https://image.tmdb.org/t/p/w500' + tvObject.poster_path"
      ></v-img>
      <v-card-title class="text-left title">{{ tvObject.name }}</v-card-title>
      <v-card-text>
        <v-row align="center" class="mx-0">
          <v-rating
            :value="tvObject.vote_average / 2"
            color="white"
            dense
            half-increments
            readonly
            size="14"
          ></v-rating>
          <div class="grey--text ml-4">
            <span
              >{{ tvObject.vote_average / 2 }} ({{
                tvObject.vote_count
              }}
              voted)</span
            >
          </div>
        </v-row>
        <div class="mt-4 subtitle-1">
          <v-chip-group active-class="primary--text" column>
            <div v-for="genre in tvObject.genre_ids" :key="genre">
              <v-chip draggable small>
                {{ getGenreName(genre) }}
              </v-chip>
            </div>
          </v-chip-group>
        </div>
      </v-card-text>
    </v-card>
  </div>
</template>

<script>
export default {
  props: {
    tvObject: Object
  },
  data: () => ({
    loading: false,
    selection: 1,
    genres: {}
  }),
  computed: {
    getRating: function(tvRating) {
      return tvRating / 2;
    }
  },
  mounted() {},
  async fetch() {
    this.genres = await fetch(
      "https://api.themoviedb.org/3/genre/tv/list?api_key=" +
        process.env.API_KEY +
        "&language=en-US"
    ).then(res => res.json());
  },
  methods: {
    reserve() {
      this.loading = true;

      setTimeout(() => (this.loading = false), 2000);
    },
    getGenreName(id) {
      for (var i = 0; i < this.genres.genres.length; i++) {
        if (id == this.genres.genres[i].id) {
          return this.genres.genres[i].name;
        }
      }
    }
  }
};
</script>

<style scoped>
.tv-card:hover {
  cursor: pointer;
  background-color: #2e2e2e;
  transition: all 0.5s;
  -webkit-transition: all 0.5s;
}
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
  opacity: 1;
}
.title {
  -ms-word-break: break-all;
  word-break: break-all;
  word-break: break-word;
}
</style>