<template>
  <v-hover v-slot="{ hover }">
    <v-card class="mx-auto">
      <v-img
        :src="`https://image.tmdb.org/t/p/original${episodeObject.still_path}`"
        :lazy-src="`https://image.tmdb.org/t/p/w185${episodeObject.still_path}`"
        class="grey lighten-2"
      >
        <template v-slot:placeholder>
          <v-row class="fill-height ma-0" align="center" justify="center">
            <v-progress-circular
              indeterminate
              color="primary lighten-5"
            ></v-progress-circular>
          </v-row>
        </template>
        <v-expand-transition>
          <div
            v-if="hover"
            class="text-body-2 pa-2 text-justify d-flex transition-fast-in-fast-out primary darken-2 v-card--reveal display-3 white--text"
            style="height: 100%;"
          >
            <v-row>
              <v-col>
                <span>{{ episodeObject.overview }}</span>
              </v-col>
            </v-row>
          </div>
        </v-expand-transition>
      </v-img>
      <v-card-title class="text-subtitle-1 title">
        <span
          ><strong
            >S{{ episodeObject.season_number }}E{{
              episodeObject.episode_number
            }}
            : </strong
          >{{ episodeObject.name }}
        </span>
      </v-card-title>
    </v-card>
  </v-hover>
</template>

<script>
export default {
  props: {
    episodeObject: Object
  }
};
</script>

<style scoped>
.v-card--reveal {
  align-items: center;
  bottom: 0;
  justify-content: center;
  opacity: 0.8;
  position: absolute;
  width: 100%;
}
.title {
  overflow-x: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
  display: block;
}
</style>