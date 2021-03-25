<template>
  <v-card
    :loading="loading"
    class="mx-auto"
    max-width="250"
    :to="'/person/' + castObject.id"
  >
    <template slot="progress">
      <v-progress-linear
        color="deep-purple"
        height="10"
        indeterminate
      ></v-progress-linear>
    </template>

    <v-img
      v-if="castObject.profile_path"
      height="250"
      :src="'https://image.tmdb.org/t/p/w185' + castObject.profile_path"
    ></v-img>
    <v-img
      v-else
      class="dummy"
      src="https://i.ibb.co/TB4jhDM/janko-ferlic-Y0-QG7-GBHBLE-unsplash.jpg"
    >
    </v-img>
    <v-card-title class="title">{{ castObject.name }}</v-card-title>
    <v-card-text>
      <v-row>
        <v-col>
          {{ castObject.character }}
        </v-col>
      </v-row>
    </v-card-text>
  </v-card>
</template>

<script>
export default {
  props: {
    castObject: Object
  },
  data() {
    return {
      loading: false
    };
  }
};
</script>

<style scoped>
.v-card:hover {
  cursor: pointer;
  background-color: #2e2e2e;
  transition: all 0.5s;
  -webkit-transition: all 0.5s;
}
.dummy {
  height: 250px;
  width: 193.55px;
}
.title {
  -ms-word-break: break-all;
  word-break: break-all;
  word-break: break-word;
}
</style>