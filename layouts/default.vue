<template>
  <v-app dark>
    <v-navigation-drawer app v-model="drawer" temporary>
      <v-list nav dense>
        <v-list-item-group active-class="deep-purple--text text--accent-4">
          <v-list-item disabled>
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
          <v-text-field
            class="mx-4"
            flat
            hide-details
            label="Search Movie or TV Show"
            prepend-inner-icon="mdi-magnify"
            solo-inverted
            dense
          ></v-text-field>
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
      drawer: false
    };
  }
};
</script>
