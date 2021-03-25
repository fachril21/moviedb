<template>
  <div>
    <div v-if="$fetchState.pending">
      <v-overlay :z-index="zIndex" :value="overlay">
        <v-progress-circular indeterminate color="primary">
        </v-progress-circular>
      </v-overlay>
    </div>
    <p v-else-if="$fetchState.error">Error while fetching mountains</p>
    <v-row>
      <v-col v-if="video" cols="12" class="text-center">
        <iframe
          width="100%"
          height="315"
          :src="'https://www.youtube.com/embed/' + video.key"
          frameborder="0"
          allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
          allowfullscreen
        ></iframe>
      </v-col>
    </v-row>
    <div>
      <v-row>
        <v-col cols="2">
          <div>
            <img
              width="100%"
              :src="'https://image.tmdb.org/t/p/w185' + tv.poster_path"
            />
          </div>
          <div>
            <strong>{{ tv.vote_average }}</strong> /
            <span class="text-caption">{{ tv.vote_count }} voted</span>
          </div>
          <div>
            <v-rating
              :value="tv.vote_average / 2"
              color="amber"
              dense
              half-increments
              readonly
              size="16"
            ></v-rating>
          </div>
        </v-col>
        <v-col cols="6">
          <v-row>
            <v-col>
              <span class="text-h5 font-weight-bold">{{ tv.name }}</span>
            </v-col>
          </v-row>
          <v-row>
            <v-col>
              <p class="text-justify">{{ tv.overview }}</p>
            </v-col>
          </v-row>
          <v-row>
            <v-col>
              <span><strong>Released :</strong> {{ tv.first_air_date }}</span
              ><br />
              <span
                ><strong>Genre :</strong>
                <span
                  v-for="(genre, index) in tv.genres"
                  :key="genre.id"
                  :index="index"
                >
                  <span v-if="index != tv.genres.length - 1">
                    {{ genre.name }},</span
                  >
                  <span v-else> {{ genre.name }}.</span>
                </span>
              </span>
              <br />
              <span v-if="tv.episode_run_time"
                ><strong>Duration :</strong>
                {{ tv.episode_run_time[0] }} min</span
              >
            </v-col>
            <v-col>
              <span
                ><strong>Production :</strong>
                <span
                  v-for="(production, index) in tv.production_companies"
                  :key="production.id"
                  :index="index"
                >
                  <span v-if="index != tv.production_companies.length - 1">
                    {{ production.name }},</span
                  >
                  <span v-else> {{ production.name }}.</span>
                </span>
              </span>
              <br />
              <span
                ><strong>Country :</strong>
                <span
                  v-for="(country, index) in tv.production_countries"
                  :key="index"
                >
                  <span v-if="index != tv.production_countries.length - 1">
                    {{ country.name }},</span
                  >
                  <span v-else> {{ country.name }}.</span>
                </span>
              </span>
            </v-col>
          </v-row>
        </v-col>
        <v-col cols="4">
          <v-card class="pa-5" height="100%" width="100%">
            <v-row>
              <v-col>
                <span><strong>Watch Provider</strong></span>
              </v-col>
            </v-row>
            <v-row>
              <v-col>
                <v-autocomplete
                  label="Select your country"
                  solo-inverted
                  :items="countryListName"
                  item-value="alpha2Code"
                  item-text="name"
                  v-model="selectedCountry"
                  dense
                ></v-autocomplete>
              </v-col>
            </v-row>
            <v-row>
              <v-col>
                <div v-if="watchProviderSelected">
                  <v-expansion-panels>
                    <v-expansion-panel v-if="watchProviderSelected.buy">
                      <v-expansion-panel-header>
                        <strong>Buy</strong>
                      </v-expansion-panel-header>
                      <v-expansion-panel-content>
                        <v-row>
                          <v-col
                            cols="3"
                            v-for="buy in watchProviderSelected.buy"
                            :key="buy.id"
                          >
                            <v-tooltip bottom color="primary">
                              <template v-slot:activator="{ on, attrs }">
                                <v-img
                                  height="50"
                                  width="50"
                                  :src="
                                    'https://image.tmdb.org/t/p/w92' +
                                      buy.logo_path
                                  "
                                  style="border-radius: 10px"
                                  v-bind="attrs"
                                  v-on="on"
                                />
                              </template>
                              <span>{{ buy.provider_name }}</span>
                            </v-tooltip>
                          </v-col>
                        </v-row>
                      </v-expansion-panel-content>
                    </v-expansion-panel>
                    <v-expansion-panel v-if="watchProviderSelected.rent">
                      <v-expansion-panel-header>
                        <strong>Rent</strong>
                      </v-expansion-panel-header>
                      <v-expansion-panel-content>
                        <v-row>
                          <v-col
                            cols="3"
                            v-for="rent in watchProviderSelected.rent"
                            :key="rent.id"
                          >
                            <v-tooltip bottom color="primary">
                              <template v-slot:activator="{ on, attrs }">
                                <v-img
                                  height="50"
                                  width="50"
                                  :src="
                                    'https://image.tmdb.org/t/p/w92' +
                                      rent.logo_path
                                  "
                                  style="border-radius: 10px"
                                  v-bind="attrs"
                                  v-on="on"
                                />
                              </template>
                              <span>{{ rent.provider_name }}</span>
                            </v-tooltip>
                          </v-col>
                        </v-row>
                      </v-expansion-panel-content>
                    </v-expansion-panel>
                    <v-expansion-panel v-if="watchProviderSelected.flatrate">
                      <v-expansion-panel-header>
                        <strong>Flatrate</strong>
                      </v-expansion-panel-header>
                      <v-expansion-panel-content>
                        <v-row>
                          <v-col
                            cols="3"
                            v-for="flatrate in watchProviderSelected.flatrate"
                            :key="flatrate.id"
                          >
                            <v-tooltip bottom color="primary">
                              <template v-slot:activator="{ on, attrs }">
                                <v-img
                                  height="50"
                                  width="50"
                                  :src="
                                    'https://image.tmdb.org/t/p/w92' +
                                      flatrate.logo_path
                                  "
                                  style="border-radius: 10px"
                                  v-bind="attrs"
                                  v-on="on"
                                />
                              </template>
                              <span>{{ flatrate.provider_name }}</span>
                            </v-tooltip>
                          </v-col>
                        </v-row>
                      </v-expansion-panel-content>
                    </v-expansion-panel>
                  </v-expansion-panels>
                </div>
                <div v-else class="text-center">
                  <span>Sorry, it's not available in your country yet</span>
                </div>
              </v-col>
            </v-row>
          </v-card>
        </v-col>
      </v-row>
    </div>
    <div>
      <v-row>
        <v-col>
          <v-tabs v-model="tab" background-color="primary" dark>
            <v-tab v-for="item in tabItems" :key="item.id">
              {{ item.tab }}
            </v-tab>
          </v-tabs>

          <v-tabs-items v-model="tab">
            <v-tab-item
              v-for="item in tabItems"
              :key="item.id"
              style="background-color: #121212;"
            >
              <div v-if="item.id == 1">
                <div v-if="$fetchState.pending">
                  <v-progress-circular
                    indeterminate
                    color="primary"
                  ></v-progress-circular>
                </div>
                <p v-else-if="$fetchState.error">
                  Sorry, i cant reach tv database :(
                </p>
                <v-row>
                  <v-col
                    v-if="similar.length == 0 && $fetchState.pending == false"
                  >
                    <span>
                      Sorry, Similiar Movies Not Found :(
                    </span>
                  </v-col>
                  <v-col v-else>
                    <masonry
                      :cols="{ default: 4, 1000: 3, 700: 2, 400: 1 }"
                      :gutter="{ default: '15px', 700: '7px' }"
                    >
                      <div v-for="tv in similar" :key="tv.id">
                        <LazyTvThumbnail :tvObject="tv" />
                      </div>
                    </masonry>
                  </v-col>
                </v-row>
              </div>
              <div v-if="item.id == 2">
                <v-row class="pt-6">
                  <masonry
                    :cols="{ default: 5, 1000: 3, 700: 2, 400: 1 }"
                    :gutter="{ default: '15px', 700: '7px' }"
                  >
                    <v-col v-for="cast in casts" :key="cast.id">
                      <LazyCastThumbnail :castObject="cast" />
                    </v-col>
                  </masonry>
                </v-row>
              </div>
              <div v-if="item.id == 3">
                <v-row class="pt-6">
                  <v-col cols="4">
                    <v-select
                      :items="getSeasonList"
                      label="test"
                      :item-text="'name'"
                      :item-value="'id'"
                      dense
                      solo
                      v-model="selectedSeason"
                    ></v-select>
                  </v-col>
                </v-row>
                <v-row>
                  <v-col
                    cols="6"
                    v-for="(episode, index) in episodes.episodes"
                    :key="index"
                  >
                    <LazyEpisodeThumbnail :episodeObject="episode" />
                  </v-col>
                </v-row>
              </div>
            </v-tab-item>
          </v-tabs-items>
        </v-col>
      </v-row>
    </div>
    <!-- <div>
      {{ seasonList }}
    </div> -->
  </div>
</template>

<script>
export default {
  props: {
    tvId: String
  },
  data() {
    return {
      overlay: true,
      zIndex: 0,
      tv: {},
      api_key: process.env.API_KEY,
      video: {},
      countries: {},
      countryListName: [],
      selectedCountry: "",
      selectFirst: true,
      watchProvider: {},
      watchProviderSelected: {},
      similar: [],
      casts: [],
      selectedSeason: 1,
      episodes: [],
      tab: null,
      tabItems: [
        {
          id: 1,
          tab: "Similar TV Shows"
        },
        {
          id: 2,
          tab: "Cast"
        },
        {
          id: 3,
          tab: "Season and Episode"
        }
      ]
    };
  },
  mounted() {
    this.getCountryList();
  },
  watch: {
    selectedCountry: function(selectedCountry) {
      this.$axios
        .get(
          "https://api.themoviedb.org/3/tv/" +
            this.$route.params.id +
            "/watch/providers?api_key=" +
            this.api_key
        )
        .then(res => {
          this.watchProviderSelected = res.data.results[selectedCountry];
        });
    },
    selectedSeason: async function(selectedSeason) {
      let seasonResponse = await fetch(
        "https://api.themoviedb.org/3/tv/" +
          this.$route.params.id +
          "/season/" +
          selectedSeason +
          "?api_key=" +
          this.api_key +
          "&language=en-US"
      );
      seasonResponse = await seasonResponse.json();
      this.episodes = seasonResponse;
    }
  },
  async fetch() {
    this.tv = await fetch(
      "https://api.themoviedb.org/3/tv/" +
        this.$route.params.id +
        "?api_key=" +
        this.api_key +
        "&language=en-US"
    ).then(res => res.json());

    let videoResponse = await fetch(
      "https://api.themoviedb.org/3/tv/" +
        this.$route.params.id +
        "/videos?api_key=" +
        this.api_key +
        "&language=en-US"
    );

    let similiarResponse = await fetch(
      "https://api.themoviedb.org/3/tv/" +
        this.$route.params.id +
        "/similar?api_key=" +
        this.api_key +
        "&language=en-US&page=1"
    );

    let castResponse = await fetch(
      "https://api.themoviedb.org/3/tv/" +
        this.$route.params.id +
        "/credits?api_key=" +
        this.api_key +
        "&language=en-US"
    );

    let seasonResponse = await fetch(
      "https://api.themoviedb.org/3/tv/" +
        this.$route.params.id +
        "/season/" +
        1 +
        "?api_key=" +
        this.api_key +
        "&language=en-US"
    );
    seasonResponse = await seasonResponse.json();
    this.episodes = seasonResponse;

    videoResponse = await videoResponse.json();
    similiarResponse = await similiarResponse.json();
    castResponse = await castResponse.json();
    this.video = videoResponse.results[0];
    this.similar = similiarResponse.results;
    this.casts = castResponse.cast;
  },
  computed: {
    getSeasonList: function() {
      let objectParsed = JSON.parse(JSON.stringify(this.tv));
      let seasonLength = objectParsed.number_of_seasons;
      let seasonList = [];
      for (var i = 1; i <= seasonLength; i++) {
        let obj = {
          id: i,
          name: "Season " + i
        };
        seasonList.push(obj);
      }
      return seasonList;
    }
  },
  methods: {
    getCountryList() {
      this.$axios.get("https://restcountries.eu/rest/v2/all").then(res => {
        var parsedobj = JSON.parse(JSON.stringify(res.data));
        parsedobj.forEach(element => {
          this.countryListName.push(element);
        });
      });
    }
  }
};
</script>