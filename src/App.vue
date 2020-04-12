<template>
  <v-app>
<!--             Alert section -->
    <v-overlay :value="overlay">
      <v-alert 
        prominent 
        type="error" 
        color="pink" 
        dark border="top">
        <v-row align="center">
          <v-col class="grow mb-0">No matching results for this particular country and sports category, try again with different values!</v-col>
          <v-col class="shrink">
            <v-btn 
            icon 
            @click="overlay = false; secondModel = null; thirdModel = null; category = ''; team = ''; categories = []; teams = []; image = require('@/assets/Soccer.jpeg'); placeholder = 'e.g. Lech Poznan'; icon = 'mdi-soccer'; clubs = null">X</v-btn>
          </v-col>
        </v-row>
      </v-alert>
    </v-overlay>
<!--             Search section -->
    <v-card 
      color="deep-purple lighten-1" 
      dark 
      class="d-flex flex-column">      
        <v-card-title class="headline deep-purple lighten-1">
          Search for your favourite team
          <v-spacer></v-spacer>
          <span class="caption font-italic font-weight-light delay-1s">SPORTSNEWSAPP</span>
          <v-icon right>mdi-newspaper-variant-multiple-outline</v-icon> 
        </v-card-title>
        <v-img 
          :src="image" 
          :key="image" 
          height="600" 
          gradient="to bottom right, rgba(100,115,201,.75), rgba(25,32,72,.5)">
        <v-card-text class="overline">
          Explore hundreds of sports clubs worldwide!
        </v-card-text>
        <v-card-text class="mb-10">
          <v-card-text>
            Choose:
          </v-card-text>
          <v-autocomplete
            class="d-inline-flex elevation-22 mr-5 mb-5 mt-3 pl-5 pr-2"
            v-model="firstModel"
            :items="firstItems"
            :search-input.sync="firstSearch"
            color="white"
            hide-no-data
            hide-selected
            rounded
            item-text="country"
            item-value="code"
            label="country"
            placeholder="e.g. Poland"
            prepend-icon="mdi-flag-variant-outline"
            return-object
            @input="handleFirstInput"
            @change="thirdModel = null; team = ''; teams = []">
          </v-autocomplete>
          <v-autocomplete
            class="d-inline-flex elevation-23 mr-5 mb-5 pl-5 pr-2"
            v-model="secondModel"
            :items="secondItems"
            :search-input.sync="secondSearch"
            color="white"
            hide-no-data
            hide-selected
            rounded
            item-text="strSport"
            item-value="idSport"
            label="sports category"
            placeholder="e.g. Soccer"
            :prepend-icon="icon"
            return-object
            @input="handleSecondInput"
            @change="thirdModel = null; team = ''; teams = []">
          </v-autocomplete>
          <v-autocomplete
            class="d-inline-flex elevation-24 pl-5 pr-2"
            v-model="thirdModel"
            :items="thirdItems"
            :search-input.sync="thirdSearch"
            color="white"
            hide-no-data
            hide-selected
            rounded
            item-text="strTeam"
            item-value="idTeam"
            label="team"
            :placeholder="placeholder"
            prepend-icon="mdi-account-group-outline"
            return-object
            :disabled="!(firstModel && secondModel)"
            @input="handleThirdInput">
          </v-autocomplete>
        </v-card-text>
        <v-card-actions class="ma-5">
          <v-btn
            :disabled="!thirdModel"
            color="grey darken-1"
            @click="fetchApi"
            style="position: absolute; left: 25px; bottom: 20px;">
            <v-icon right>mdi-magnify</v-icon>
            Search
          </v-btn>
          <v-spacer></v-spacer>
          <v-btn
            :disabled="!(firstModel || secondModel || thirdModel)"
            color="grey darken-1"
            @click="firstModel = ''; secondModel = null; thirdModel = null; category = ''; team = ''; region = ''; categories = []; teams = []; countries = []; image = require('@/assets/Soccer.jpeg'); placeholder = 'e.g. Lech Poznan'; icon = 'mdi-soccer'; clubs = null"
            style="position: absolute; right: 25px; bottom: 20px;">
            Clear
            <v-icon right>mdi-close-circle</v-icon>
          </v-btn>
        </v-card-actions>
      </v-img>
    </v-card>
<!--             Footer section -->
    <v-footer
      dark
      padless>
      <v-card
        flat
        tile
        class="indigo lighten-1 white--text text-center"
        width="100%">
        <v-card-text>
          powered by
        </v-card-text>
        <v-row class="pt-0 d-flex justify-center">
          <v-img class="mr-5 shadow" max-width="7%" max-height="100%" src="https://images-apilist-fun.sfo2.cdn.digitaloceanspaces.com/the_sports_db_api.png"></v-img>
          <v-img class="mx-5 shadow2" max-width="10%" height="100%" src="./assets/newsapi.png"></v-img>
          <v-img class="ml-5 shadow" max-width="7%" max-height="100%" src="https://www.api-football.com/public/img/home1/hero-banner1.png"></v-img>
        </v-row>
        <v-divider class="mx-10 mt-5"></v-divider>
        <v-card-text class="white--text">
          <strong>{{ new Date().getFullYear() }} ©</strong>
        </v-card-text>
      </v-card>
    </v-footer>
  </v-app>
</template>

<script>

export default {
  name: 'App',
  data () {
    return {
      count: [],
      countries: [],
      categories: [], 
      teams: [],
      firstModel: null,
      secondModel: null,
      thirdModel: null,
      firstSearch: null,
      secondSearch: null,
      thirdSearch: null,
      region: '',
      category: '',
      team: '',
      image: require('@/assets/Soccer.jpeg'),
      icon: 'mdi-soccer',
      icons: ['mdi-soccer', 
      'mdi-car-sports', 
      'mdi-boxing-glove', 
      'mdi-baseball', 
      'mdi-basketball',
      'mdi-football',
      'mdi-hockey-puck',
      'mdi-rugby',
      'mdi-cricket',
      'mdi-football-australian',
      'mdi-gamepad-square-outline',
      'mdi-volleyball',
      'mdi-pokeball',
      'mdi-handball',
      'mdi-hockey-sticks',
      ],
      placeholder: 'e.g. Lech Poznan',
      placeholders: ['e.g. Lech Poznan',
      'e.g. Orlen X-Raid Team',
      'e.g. ACB MMA',
      'e.g. Boston Red Sox',
      'e.g. Basket Zielona Góra',
      'e.g. New York Giants',
      'e.g. Toronto Maple Leafs',
      'e.g. Gloucester',
      'e.g. Birmingham Bears',
      'e.g. Collingwood Football Club',
      'e.g. Ascension Gaming',
      'e.g. A.E. Komotini',
      'e.g. Melbourne Vixens',
      'e.g. Paris Saint-Germain Handball',
      'e.g. Rot-Weiss Köln'
      ],
      overlay: false,
      alert: true
    }
  },
  methods: {
    handleFirstInput (value) {
      this.region = value.country;
      },
    handleSecondInput (value) {
      this.category = value.strSport;
      var i;
      for (i = 0; i < this.categories.length; i++) {
        if (this.category == `${this.categories[i].strSport}`) {
          this.image = require(`@/assets/${this.categories[i].strSport}.jpeg`);
          this.icon = this.icons[i]
          this.placeholder = this.placeholders[i]
          }}},
    handleThirdInput (value) {
      this.team = value.strTeam;
      },
  },
  computed: {
    firstFields () {
        if (!this.firstModel) return []

        return Object.keys(this.firstModel).map(key => {
          return {
            key,
            value: this.firstModel[key] || 'n/a',
          }
        })
      },
      secondFields () {
        if (!this.secondModel) return []

        return Object.keys(this.secondModel).map(key => {
          return {
            key,
            value: this.secondModel[key] || 'n/a',
          }
        })
      },
      thirdFields () {
        if (!this.thirdModel) return []
      
        return Object.keys(this.thirdModel).map(key => {
          return {
            key,
            value: this.thirdModel[key] || 'n/a',
          }
        })
      },
      firstItems () {
        return this.countries.map(region => {
          const country = region.country.replace('-', ' ').replace(' DR', '')

          return Object.assign({}, region, { country })
        })
      },
      secondItems () {
        return this.categories.map(category => {
          const strSport = category.strSport

          return Object.assign({}, category, { strSport })
        })
      },
      thirdItems () {
        return this.teams.map(team => {
          const strTeam = team.strTeam

          return Object.assign({}, team, { strTeam })
        })
      }
  },
  watch: {
      firstSearch () {
        if (this.firstItems.length > 0) return

        fetch("https://api-football-v1.p.rapidapi.com/v2/countries", {
              "method": "GET",
              "headers": {
                "x-rapidapi-host": "api-football-v1.p.rapidapi.com",
                "x-rapidapi-key": "54aa1af517msh325e94573cd5588p194beejsn32fe5fe630c3"
              }
            })
          .then(res => res.json())
          .then(res => {
            const { results, countries } = res.api
            this.count = results
            this.countries = countries
            let keys = Object.keys(this.countries)
            let indexes = [6, 9, 12, 16, 17, 18, 21, 24, 42, 44, 50, 52, 64, 76, 83, 92, 99, 126]
            for (var index of indexes) {
            delete this.countries[keys[index]]}
            this.countries = this.countries.filter(function() { return true; })
          })
          .catch(err => {
            console.log(err)
          })
      },
      secondSearch () {
        if (this.secondItems.length > 0) return

        fetch('https://www.thesportsdb.com/api/v1/json/1/all_sports.php')
          .then(res => res.json())
          .then(res => {
            const { sports } = res
            this.categories = sports
            let keys = Object.keys(this.categories)
            let indexes = [7, 9, 11, 17, 19]
            for (var index of indexes) {
            delete this.categories[keys[index]]}
            this.categories = this.categories.filter(function() { return true; })
              
          })
          .catch(err => {
            console.log(err)
          })
      },
      thirdSearch () {
        if (this.thirdItems.length > 0) return

        if (this.category != '' && this.region != '') {
          fetch(`https://www.thesportsdb.com/api/v1/json/1/search_all_teams.php?s=${this.category}&c=${this.region}`)
            .then(res => res.json())
            .then(res => {
              if (res.teams != null) {
              const { teams } = res
              this.teams = teams}
              else {this.overlay = true
              this.thirdSearch = null
              }
            })
            .catch(err => {
              console.log(err)
            });
            }
      },
    },
};
</script>

<style scoped>
.shadow {
  -webkit-filter: drop-shadow( 3px 3px 2px rgba(0, 0, 0, .7));
  filter: drop-shadow( 3px 3px 2px rgba(0, 0, 0, .7));
  /* Similar syntax to box-shadow */
}

.shadow2 {
  -webkit-filter: drop-shadow( 3px 3px 2px rgba(0, 0, 0, .3));
  filter: drop-shadow( 3px 3px 2px rgba(0, 0, 0, .3));
  /* Similar syntax to box-shadow */

}

.fade-enter-active, .fade-leave-active {
  transition: opacity .5s;
}
.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
  opacity: 0;
}
</style>