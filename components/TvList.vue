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
              :length="tvs.total_pages"
              :total-visible="7"
            ></v-pagination>
          </div>
        </div>
        <masonry
          :cols="{ default: 4, 1000: 3, 700: 2, 400: 1 }"
          :gutter="{ default: '30px', 700: '15px' }"
        >
          <div v-for="(tv, index) in tvs.results" :key="index.id">
            <LazyTvThumbnail :tvObject="tv" />
          </div>
        </masonry>
        <div>
          <div class="text-center">
            <v-pagination
              v-model="page"
              :length="tvs.total_pages"
              :total-visible="7"
            ></v-pagination>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      overlay: true,
      zIndex: 0,
      tvs: [],
      selected: "popular",
      page: 1,
      category: {
        list: [
          { value: "popular", text: "Popular" },
          { value: "top_rated", text: "Top Rated" }
        ],
        popular: {
          id: 1,
          value: "Popular",
          api_endpoint:
            "https://api.themoviedb.org/3/tv/popular?api_key=" +
            process.env.API_KEY +
            "&language=en-US&page="
        },
        top_rated: {
          id: 2,
          value: "Top Rated",
          api_endpoint:
            "https://api.themoviedb.org/3/tv/top_rated?api_key=" +
            process.env.API_KEY +
            "&language=en-US&page="
        }
      }
    };
  },
  components: {},
  mounted() {
    if (typeof this.$redrawVueMasonry === "function") {
      this.$redrawVueMasonry();
    }

    this.$watch(
      vm => [vm.page],
      async () => {
        this.$fetch();
        this.tvs = await fetch(
          this.category[this.selected].api_endpoint.concat(this.page)
        ).then(res => res.json());
      }
    );

    this.$watch(
      vm => [vm.selected],
      async () => {
        this.$fetch();
        this.page = 1;
        this.tvs = await fetch(
          this.category[this.selected].api_endpoint.concat(this.page)
        ).then(res => res.json());
      }
    );
  },
  async fetch() {
    this.tvs = await fetch(
      "https://api.themoviedb.org/3/tv/popular?api_key=" +
        process.env.API_KEY +
        "&language=en-US&page=1"
    ).then(res => res.json());
  }
};
</script>
