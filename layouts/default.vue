<template>
  <v-app dark>
    <v-navigation-drawer app v-model="drawer" temporary>
      <v-list nav dense>
        <v-list-item-group active-class="deep-purple--text text--accent-4">
          <v-list-item to="/">
            <v-list-item-content>
              <v-list-item-title class="title">
                MovieDB
              </v-list-item-title>
              <v-list-item-subtitle>
                Your movie information
              </v-list-item-subtitle>
            </v-list-item-content>
          </v-list-item>
          <v-list-item to="/movie">
            <v-list-item-icon>
              <v-icon>mdi-filmstrip</v-icon>
            </v-list-item-icon>
            <v-list-item-title>Movie</v-list-item-title>
          </v-list-item>

          <v-list-item to="/tv">
            <v-list-item-icon>
              <v-icon>mdi-television-play</v-icon>
            </v-list-item-icon>
            <v-list-item-title>TV Show</v-list-item-title>
          </v-list-item>
        </v-list-item-group>
      </v-list>
    </v-navigation-drawer>

    <v-app-bar app>
      <v-app-bar-nav-icon @click="drawer = true"></v-app-bar-nav-icon>
      <v-row class="my-auto mx-5">
        <v-col cols="12" sm="6">
          <v-row>
            <v-col>
              <v-text-field
                label="Search movie, tv show, or person ..."
                prepend-inner-icon="mdi-magnify"
                hide-details="false"
                v-model="searchQuery"
                dense
                solo-inverted
              />
            </v-col>
          </v-row>
          <v-row v-if="searchQuery != ''">
            <v-col id="searchResult" class="pt-0">
              <v-card class="pa-3" v-if="items">
                <v-list-item-group color="primary">
                  <v-list-item
                    v-for="item in items.slice(0, 3)"
                    :key="item.id"
                    class="mb-3 pa-2"
                    :to="'/' + item.media_type + '/' + item.id"
                    @click="clickedSearch"
                  >
                    <v-img
                      max-width="100px"
                      height="100px"
                      contain
                      style="border-radius: 5px;"
                      :src="'https://image.tmdb.org/t/p/w92' + item.poster_path"
                    ></v-img>
                    <v-list-item-content>
                      <div v-if="item.media_type == 'movie'">
                        <v-list-item-title
                          v-text="item.title"
                          class="font-weight-bold"
                        ></v-list-item-title>
                        <v-list-item-subtitle>Movie</v-list-item-subtitle>
                      </div>
                      <div v-if="item.media_type == 'tv'">
                        <v-list-item-title
                          v-text="item.name"
                          class="font-weight-bold"
                        ></v-list-item-title>
                        <v-list-item-subtitle>TV Show</v-list-item-subtitle>
                      </div>
                      <div v-if="item.media_type == 'person'">
                        <v-list-item-title
                          v-text="item.name"
                          class="font-weight-bold"
                        ></v-list-item-title>
                        <v-list-item-subtitle>Person</v-list-item-subtitle>
                      </div>
                    </v-list-item-content>
                  </v-list-item>
                </v-list-item-group>
              </v-card>
              <v-card v-if="items.length == 0">
                <v-card-text>
                  <div>Sorry, i cant get what you want :(</div>
                </v-card-text>
              </v-card>
            </v-col>
          </v-row>
        </v-col>
      </v-row>
    </v-app-bar>
    <v-main>
      <v-container>
        <nuxt />
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
export default {
  data() {
    return {
      drawer: false,
      searchQuery: "",
      items: []
    };
  },
  watch: {
    searchQuery: function(query) {
      this.seen = true;
      if (this.searchQuery != "") {
        this.getSearch(query);
      } else {
        this.items = [];
      }
    }
  },
  methods: {
    async getSearch(query) {
      const response = await this.$axios.get(
        "https://api.themoviedb.org/3/search/multi?api_key=" +
          process.env.API_KEY +
          "&language=en-US&query=" +
          query +
          "&page=1&include_adult=false"
      );
      this.items = response.data.results;
    },
    clickedSearch: function() {
      this.seen = false;
      this.searchQuery = "";
    }
  }
};
</script>
