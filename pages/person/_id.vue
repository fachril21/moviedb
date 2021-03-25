<template>
  <div class="mt-5">
    <v-row>
      <v-col cols="3">
        <v-img
          class="person"
          :src="'https://image.tmdb.org/t/p/original' + people.profile_path"
        />
        <div class="mt-5">
          <v-row>
            <v-col v-if="externalId.facebook_id" cols="3">
              <v-btn
                class="ma-2"
                text
                icon
                color="white lighten-2"
                :href="'https://www.facebook.com/' + externalId.facebook_id"
                target="_blank"
              >
                <v-icon>mdi-facebook</v-icon>
              </v-btn>
            </v-col>
            <v-col v-if="externalId.instagram_id" cols="3">
              <v-btn
                class="ma-2"
                text
                icon
                color="white lighten-2"
                :href="'https://www.instagram.com/' + externalId.instagram_id"
                target="_blank"
              >
                <v-icon>mdi-instagram</v-icon>
              </v-btn>
            </v-col>
            <v-col v-if="externalId.twitter_id" cols="3">
              <v-btn
                class="ma-2"
                text
                icon
                color="white lighten-2"
                :href="'https://www.twitter.com/' + externalId.twitter_id"
                target="_blank"
              >
                <v-icon>mdi-twitter</v-icon>
              </v-btn>
            </v-col>
          </v-row>
        </div>
        <div>
          <v-row>
            <v-col>
              <span class="text-h6"><strong>Personal Info</strong></span>
            </v-col>
          </v-row>
          <v-row>
            <v-col>
              <div class="mb-4">
                <div>
                  <span><strong>Known For</strong></span>
                </div>
                <div>
                  <span class="text-body">{{
                    people.known_for_department
                  }}</span>
                </div>
              </div>
              <div class="my-4">
                <div>
                  <span><strong>Known Credits</strong></span>
                </div>
                <div>
                  <span>{{ totalCredits }} Movies and TVs</span>
                </div>
              </div>
              <div class="my-4">
                <div>
                  <span><strong>Gender</strong></span>
                </div>
                <div>
                  <span v-if="people.gender == 1">Female</span>
                  <span v-if="people.gender == 2">Male</span>
                </div>
              </div>
              <div class="my-4">
                <div>
                  <span><strong>Birthday</strong></span>
                </div>
                <div>
                  <span
                    >{{ people.birthday | formatDate() }} ({{
                      people.birthday | calculateAge()
                    }}
                    years old)</span
                  >
                </div>
              </div>
              <div class="my-4">
                <div>
                  <span><strong>Place of Birth</strong></span>
                </div>
                <div>
                  <span>{{ people.place_of_birth }}</span>
                </div>
              </div>
            </v-col>
          </v-row>
        </div>
      </v-col>
      <v-col cols="9">
        <div>
          <div>
            <span class="text-h4"
              ><strong>{{ people.name }}</strong></span
            >
          </div>
          <div class="mt-5"><span class="text-h6">Biography</span></div>
          <div>
            <p class="text-body-2">
              {{ people.biography }}
            </p>
          </div>
        </div>
        <div>
          <div class="mt-5 mb-2"><span class="text-h6">Acting</span></div>

          <v-card class="pa-5">
            <v-timeline align-top :dense="$vuetify.breakpoint.smAndDown">
              <v-timeline-item
                v-for="(item, i) in getCreditList"
                :key="i"
                :color="item.color"
                :icon="item.icon"
                fill-dot
              >
                <v-tooltip top>
                  <template v-slot:activator="{ on, attrs }">
                    <v-card
                      :color="item.color"
                      dark
                      v-bind="attrs"
                      v-on="on"
                      class="timeline-card"
                      :to="item.path"
                    >
                      <v-card-title class="text-subtitle-1 timeline-title">
                        <span
                          ><strong
                            >{{ item.title }} ({{
                              item.date | getYear()
                            }})</strong
                          >
                          <span v-if="item.data.character"
                            >as {{ item.data.character }}</span
                          ></span
                        >
                      </v-card-title>
                    </v-card>
                  </template>
                  <div>
                    <v-img
                      class="mb-2"
                      v-if="item.data.poster_path"
                      :src="
                        'https://image.tmdb.org/t/p/w185' +
                          item.data.poster_path
                      "
                      style="border-radius: 5px;"
                    />
                    <div class="text-center">
                      <span
                        ><strong>{{ item.title }}</strong></span
                      >
                    </div>
                  </div>
                </v-tooltip>
              </v-timeline-item>
            </v-timeline>
          </v-card>
        </div>
      </v-col>
    </v-row>
  </div>
</template>

<script>
export default {
  data() {
    return {
      people: {},
      externalId: {},
      movieCredits: [],
      tvCredits: [],
      totalCredits: "",
      list: []
    };
  },
  async fetch() {
    let peopleResponse = await fetch(
      "https://api.themoviedb.org/3/person/" +
        this.$route.params.id +
        "?api_key=" +
        process.env.API_KEY +
        "&language=en-US"
    );

    let externalResponse = await fetch(
      "https://api.themoviedb.org/3/person/" +
        this.$route.params.id +
        "/external_ids?api_key=" +
        process.env.API_KEY +
        "&language=en-US"
    );

    peopleResponse = await peopleResponse.json();
    externalResponse = await externalResponse.json();

    this.people = peopleResponse;
    this.externalId = externalResponse;
  },
  mounted() {
    this.getCredits();
  },
  filters: {
    formatDate(date) {
      var dateObj = new Date(date);
      var monthNames = [
        "Jan",
        "Feb",
        "Mar",
        "Apr",
        "May",
        "Jun",
        "Jul",
        "Aug",
        "Sep",
        "Oct",
        "Nov",
        "Dec"
      ];

      var day = dateObj.getDate();
      var monthIndex = dateObj.getMonth();
      var year = dateObj.getFullYear();

      return monthNames[monthIndex] + " " + day + " " + year;
    },
    calculateAge(birthday) {
      var dateObj = new Date(birthday);
      var ageDifMs = Date.now() - dateObj.getTime();
      var ageDate = new Date(ageDifMs); // miliseconds from epoch
      return Math.abs(ageDate.getUTCFullYear() - 1970);
    },
    getYear: function(string) {
      return string.substring(0, 4);
    }
  },
  computed: {
    getTotalCredits: function() {
      this.totalCredits = this.movieCredits.length + this.tvCredits.length;
    },
    getCreditList: function() {
      var creditList = [];
      for (var i = 0; i < this.movieCredits.length; i++) {
        creditList.push({
          media_type: "movie",
          data: this.movieCredits[i],
          date: this.movieCredits[i].release_date,
          icon: "mdi-filmstrip",
          title: this.movieCredits[i].title,
          color: "indigo",
          path: "/movie/" + this.movieCredits[i].id
        });
      }

      for (var i = 0; i < this.tvCredits.length; i++) {
        creditList.push({
          media_type: "tv",
          data: this.tvCredits[i],
          date: this.tvCredits[i].first_air_date,
          icon: "mdi-television-play",
          title: this.tvCredits[i].name,
          color: "pink",
          path: "/tv/" + this.tvCredits[i].id
        });
      }

      var sortedList = creditList.sort(function(a, b) {
        var keyA = new Date(a.date),
          keyB = new Date(b.date);
        // Compare the 2 dates
        if (keyA > keyB) return -1;
        if (keyA < keyB) return 1;
        return 0;
      });
      return sortedList;
    }
  },
  methods: {
    getCredits() {
      this.$axios
        .get(
          "https://api.themoviedb.org/3/person/" +
            this.$route.params.id +
            "/movie_credits?api_key=" +
            process.env.API_KEY +
            "&language=en-US"
        )
        .then(res => {
          this.movieCredits = res.data.cast;
        });

      this.$axios
        .get(
          "https://api.themoviedb.org/3/person/" +
            this.$route.params.id +
            "/tv_credits?api_key=" +
            process.env.API_KEY +
            "&language=en-US"
        )
        .then(res => {
          this.tvCredits = res.data.cast;
        });
    }
  }
};
</script>

<style scoped>
.person {
  border-radius: 10px;
}
p {
  text-align: justify;
}
.timeline-card:hover {
  cursor: pointer;
}
.timeline-title {
  -ms-word-break: break-all;
  word-break: break-all;
  word-break: break-word;
}
</style>