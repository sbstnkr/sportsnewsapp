<template>
    <v-app>
<!--             Alert section -->
        <v-overlay :value="overlay">
        <v-alert 
            prominent 
            type="error" 
            color="pink" 
            dark border="top"
            class="animated bounceIn">
            <v-row align="center">
            <v-col class="grow mb-0">No matching results for this particular country and sports category, try again with different values!</v-col>
            <v-col class="shrink">
                <v-btn 
                icon 
                @click="loadedOnce = false; overlay = false; secondModel = null; thirdModel = null; category = ''; team = ''; categories = []; teams = []; teamPlayers = null, selectedPlayer = null, teamEvents = null; animated = true; readMoreTeamDescActivated = false; image = require('@/assets/Soccer.jpeg'); placeholder = 'e.g. Lech Poznan'; icon = 'mdi-soccer'; selectedTeam = null">X</v-btn>
            </v-col>
            </v-row>
        </v-alert>
        </v-overlay>
        <v-card color="deep-purple lighten-1" dark class="d-flex flex-column">
                <v-card-title class="headline deep-purple lighten-1">
                    <span 
                    class="animated flipInX"
                    style="animation-delay: 500ms">
                    Search for your favourite team
                    </span>
                    <v-spacer></v-spacer>
                    <span 
                    class="caption font-italic font-weight-light animated bounceInRight"
                    style="animation-delay: 750ms">
                    SPORTSNEWSAPP</span>
                    <v-icon right>mdi-newspaper-variant-multiple-outline</v-icon>
                </v-card-title>
                <v-img 
                    :src="image" 
                    :key="image" 
                    height="600" 
                    gradient="to bottom right, rgba(100,115,201,.75), rgba(25,32,72,.5)"
                    class="animated fadeIn">
                <v-card-text 
                class="overline animated"
                :class="{flipInX : loadedOnce}"
                style="animation-delay: 500ms">
                Explore hundreds of sports clubs worldwide!
                </v-card-text>
                <v-card-text class="mb-10">
                    <v-card-text 
                    class="animated"
                    :class="{flipInX : loadedOnce}"
                    style="animation-delay: 500ms">
                    Choose:
                    </v-card-text>
                    <v-autocomplete
                    class="d-inline-flex elevation-22 mr-5 mb-5 mt-3 pl-5 pr-2 animated"
                    :class="{flipInX : loadedOnce}"
                    style="animation-delay: 750ms;"
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
                    @change="loadedOnce = false; readMoreTeamDescActivated = false; thirdModel = null; team = ''; teams = []; selectedTeam = null; teamPlayers = null; selectedPlayer = null; teamEvents = null; animated = true">
                </v-autocomplete>
                    <v-autocomplete
            class="d-inline-flex elevation-23 mr-5 mb-5 pl-5 pr-2 animated"
            :class="{flipInX : loadedOnce}"
            style="animation-delay: 1250ms"
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
            @change="loadedOnce = false; readMoreTeamDescActivated = false; thirdModel = null; team = ''; teams = []; selectedTeam = null; teamPlayers = null; selectedPlayer = null; teamEvents = null; animated = true">
          </v-autocomplete>
          <v-autocomplete
            class="d-inline-flex elevation-24 pl-5 pr-2 animated"
            :class="{flipInX : loadedOnce}"
            style="animation-delay: 1750ms"
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
            @input="handleThirdInput"
            @change="loadedOnce = false; readMoreTeamDescActivated = false; selectedTeam = null; teamPlayers = null; selectedPlayer = null; teamEvents = null; animated = true">
          </v-autocomplete>
                </v-card-text>
                <v-card-actions class="ma-5">
          <v-btn
            :disabled="!thirdModel"
            color="grey darken-1"
            @click="fetchApi"
            class="animated"
            :class="{pulse: thirdModel}"
            style="position: absolute; left: 25px; bottom: 20px;">
            <v-icon right>mdi-magnify</v-icon>
            Search
          </v-btn>
          <v-spacer></v-spacer>
          <v-btn
            :disabled="!(firstModel || secondModel || thirdModel)"
            color="grey darken-1"
            @click="loadedOnce = false; readMoreTeamDescActivated = false; firstModel = ''; secondModel = null; thirdModel = null; category = ''; team = ''; region = ''; categories = []; teams = []; countries = []; selectedTeam = null; teamPlayers = null; selectedPlayer = null; teamEvents = null; animated = true; image = require('@/assets/Soccer.jpeg'); placeholder = 'e.g. Lech Poznan'; icon = 'mdi-soccer';"
            style="position: absolute; right: 25px; bottom: 20px;">
            Clear
            <v-icon right>mdi-close-circle</v-icon>
          </v-btn>
        </v-card-actions>
            </v-img>
        </v-card>

      <div class="indigo darken-1 text-center">
          <v-card :elevation="24" class=" indigo darken-4 white--text col-md-8 col-sm-10 offset-md-2 offset-sm-1 my-md-10 my-5">

              <div class="Team_Info_container" v-if="firstLoadOfPage!=true">
              <img :src="selectedTeam.strTeamBanner" class="v-picker--full-width mb-5" >

<!--             team description section-->
              <div class="py-8 px-8">
                  <p v-if="!readMoreTeamDescActivated" > {{selectedTeam.strDescriptionEN.slice(0, 435)}}...</p>
                  <p v-if="readMoreTeamDescActivated" > {{selectedTeam.strDescriptionEN}}</p>
                  <v-btn small color="indigo lighten-4" v-if="!readMoreTeamDescActivated  && selectedTeam.strDescriptionEN.length > 435" @click="activateMoreText">Read More</v-btn>
                  <v-btn small color="indigo lighten-4" v-if="readMoreTeamDescActivated" @click="deactivateMoreText">Read Less</v-btn>
              </div>

<!--              team info section-->
              <v-card color="indigo darken-2 white--text" :elevation="10" class="mx-sm-8">
                  <v-row>
                      <div class="mx-md-4 col-4 offset-md-0 offset-4">
                          <v-img :src="selectedTeam.strTeamBadge"></v-img>
                      </div>
                      <div class="col-md-7 ">
                          <v-row v-for="(infoValue, infoKey) in teamInfo[0]" :key="infoKey" >
                              <div class="col-md-4 col-6  text-right py-0">
                                  <p class="font-weight-black"> {{infoKey}} : </p>
                              </div>
                              <div class="col-md-8 col-6 text-left py-0">
                                  <p> {{selectedTeam[infoValue]}} </p>
                              </div>
                          </v-row>
                      </div>
                  </v-row>
              </v-card>

<!--              team players section-->
              <v-card class="text-left mx-sm-8 my-10" :elevation="10">
                  <v-card-title class="indigo darken-2 white--text headline">
                      Players
                  </v-card-title>
                  <v-row class="pa-4" justify="space-between">
                      <div class="col-sm-12 col-md-6 col-lg-5 pr-0">
                          <v-list class="overflow-y-auto" max-height="500" three-line>
                              <template v-for="player in teamPlayers" >
                                  <v-list-item @click="setSelectedPlayer(player)" :key="player.strTeam">
                                      <v-list-item-avatar>
                                          <v-img :src="player.strCutout"></v-img>
                                      </v-list-item-avatar>
                                      <v-list-item-title v-html="player.strPlayer"></v-list-item-title>
                                  </v-list-item>
                                  <v-divider :key="player.id"></v-divider>
                              </template>
                          </v-list>
                      </div>

                      <div class="col-lg-6 col-md-5 col-12 text-center pl-0">
                              <div v-if="!selectedPlayer" class="title grey--text text--lighten-1 font-weight-light" style="align-self: center;">
                                  Select a User
                              </div>
                              <v-card v-else :key="selectedPlayer.id" class="my-10" flat>
                                  <v-card-text>
                                      <v-avatar size="100">
                                          <v-img :src="selectedPlayer.strCutout" class="mb-6"></v-img>
                                      </v-avatar>
                                      <h3 class="headline mb-2">
                                          {{ selectedPlayer.strPlayer }}
                                      </h3>
                                  </v-card-text>
                                  <v-divider></v-divider>
                                  <div class="my-5">
                                      <v-row class="pa-0">
                                          <div class="offset-1 offset-md-0 col-5  pa-0 text-right font-regular mr-4 mb-2" tag="strong" cols="6">Date Born:</div>
                                          <div class="col-5 pa-0 text-left font-weight-light">{{selectedPlayer.dateBorn}}</div>
                                      </v-row>
                                      <v-row class="pa-0">
                                          <div class="offset-1 offset-md-0 col-5  pa-0 text-right  font-regular mr-4 mb-2" tag="strong" cols="6">Nationality:</div>
                                          <div class="col-5 pa-0 text-left font-weight-light">{{ selectedPlayer.strNationality }}</div>
                                      </v-row>
                                      <v-row class="pa-0">
                                          <div class="offset-1  offset-md-0 col-5 pa-0 text-right font-regular mr-4 mb-2" tag="strong" cols="6">Number:</div>
                                          <div class="col-5  pa-0 text-left font-weight-light">{{ selectedPlayer.strNumber }}</div>
                                      </v-row>
                                      <v-row class="pa-0">
                                          <div class="offset-1 offset-md-0 col-5 pa-0 text-right font-regular mr-4 mb-2" tag="strong" cols="6">Position:</div>
                                          <div class="col-5 pa-0 text-left font-weight-light">{{ selectedPlayer.strPosition }}</div>
                                      </v-row>
                                      <v-row class="col-md-10 offset-md-1 mx-3">
                                          <a v-if="selectedPlayer.strFacebook" target="_blank" v-bind:href="'http://' + selectedPlayer.strFacebook"><i class="fab fa-facebook-square fa-2x social-icons" style="color: #153bd4"></i></a>
                                          <a v-if="selectedPlayer.strTwitter" target="_blank" v-bind:href="'http://' + selectedPlayer.strTwitter"><i class="fab fa-twitter-square fa-2x social-icons" style="color: #3ca5cf"></i></a>
                                          <a v-if="selectedPlayer.strInstagram" target="_blank" v-bind:href="'http://' + selectedPlayer.strInstagram"><i class="fab fa-instagram-square fa-2x social-icons" style="color: #bd31d6"></i></a>
                                          <a v-if="selectedPlayer.strYoutube" target="_blank" v-bind:href="'http://' + selectedPlayer.strYoutube"><i class="fab fa-youtube fa-2x social-icons" style="color: red"></i></a>
                                      </v-row>
                                  </div>
                              </v-card>
                      </div>
                  </v-row>
              </v-card>

<!--              team events section-->
              <v-card class="col-md-6 offset-md-3 pa-0 " :elevation="10">
                  <v-toolbar class="indigo darken-2" dark>
                      <v-toolbar-title>Coming Events</v-toolbar-title>
                  </v-toolbar>
                  <v-list two-line>
                      <v-list-item-group>
                          <template v-for="(teamEvent, eventIndex) in teamEvents">
                              <v-list-item :key="eventIndex">
                                  <v-list-item-content class="text-left">
                                      <v-list-item-title class="font-weight-black" v-text="teamEvent.strEvent"> </v-list-item-title>
                                      <v-list-item-subtitle class="text--primary" v-text="teamEvent.strLeague"></v-list-item-subtitle>
                                  </v-list-item-content>
                                  <v-list-item-action>
                                      <v-list-item-action-text v-text="teamEvent.dateEvent" ></v-list-item-action-text>
                                      <v-list-item-action-text v-text="teamEvent.strTimeLocal" ></v-list-item-action-text>
                                  </v-list-item-action>
                              </v-list-item>
                              <v-divider :key="eventIndex"></v-divider>
                          </template>
                      </v-list-item-group>
                  </v-list>
              </v-card>
            </div>

<!--             Carousel section -->
            <v-card
              color="rgba(26, 35, 126, 0.5)"
              class="white--text text-left mx-sm-8 my-10"
              :elevation="10"
            >
              <v-card-title class="indigo darken-2 white--text headline">Sport News</v-card-title>
              <v-carousel
                class="animated fadeIn slower"
                hide-delimiters
                @change="newsToShow = news[$event]"
              >
                <v-carousel-item
                  class="carousel__item"
                  @click="newsClicked(item)"
                  v-for="(item,index) in news"
                  :key="item.urlToImage"
                  :class="{ active: index == 0 }"
                  reverse-transition="fade"
                  transition="fade"
                >
                  <v-card>
                    <v-img :src="item.urlToImage" height="400px"></v-img>
                    <v-card-title class="mx-0 my-0">{{ item.title.slice(0,lengthOfNewsTitle) }}...</v-card-title>
                  </v-card>
                </v-carousel-item>
              </v-carousel>
            </v-card>

<!--            News section-->
          <v-card color="indigo darken-2 white--text" :elevation="10" class="mx-sm-8">
            <div id="newsContainer" v-if="isShowNews === true">
                <p>{{ newsToShow.publishedAt.replace("T", " ").replace("Z", "") }}</p>
                <h3 class="text-center">{{ newsToShow.title }}</h3>
                    <p class="my-5">
                        <b>{{ newsToShow.description }}</b>
                    </p>
                    <p class="mb-5">
                        {{ newsToShow.content | regExp() }}
                    <a :href="newsToShow.url" target="_blank" >Click here to read full article</a>
                    </p>
            </div>
            </v-card>
          </v-card>
      </div>


        <v-footer
                dark
                padless
        >
            <v-card
                    flat
                    tile
                    class="indigo lighten-1 white--text text-center"
                    width="100%"
            >
                <v-card-text>
                    powered by
                </v-card-text>

                <v-row class="pt-0 d-flex justify-center">
                    <v-img class="mr-5 shadow" max-width="7%" max-height="100%"
                           src="https://images-apilist-fun.sfo2.cdn.digitaloceanspaces.com/the_sports_db_api.png">
                    </v-img>
                    <v-img class="mx-5 shadow2" max-width="10%" height="100%" src="./assets/newsapi.png">
                    </v-img>
                    <v-img class="ml-5 shadow" max-width="7%" max-height="100%"
                           src="https://www.api-football.com/public/img/home1/hero-banner1.png">
                    </v-img>

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
    import axios from "axios";


    export default {
        name: 'App',
        data() {
            return {
        apiKey1: '15de284d0e884be19dcf6c92c4a88205',
      apiKey2: '4013017',
      urlBase: 'https://www.thesportsdb.com/api/v1/json/',
      selectedTeam: null,
      news: null,
      show: false,
      loading: true,
      loaded: false,
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
      alert: true,
      teamInfo: [{Team:'strTeam', Formed:'intFormedYear', League:'strLeague', Stadium: 'strStadium', Country: 'strCountry'}],
      teamPlayers: null,
      selectedPlayer: null,
      teamEvents: null,
      readMoreTeamDescActivated: false,
      isShowNews: false,
      newsToShow: null,
      isNewsClicked: false,
      animated: true,
      description: '',
      loadedOnce: true,
      firstLoadOfPage: true,
      lengthOfNewsTitle: 56,
            }
        },
        methods: {
            fetchApi() {
                axios.all([
                    axios.get(`${this.urlBase2}${this.apiKey2}/searchteams.php?t=${this.team}`)
                ])
                    .then(response => (
                        this.clubs = response[1]
                    ));
                console.log(this.news),
                console.log(this.clubs),
                this.firstLoadOfPage=false;
            },
            handleFirstInput(value) {
                this.region = value.country;
            },
            handleSecondInput(value) {
                this.category = value.strSport;
            },
            handleThirdInput(value) {
                this.team = value.strTeam;
            },
            activateMoreText() {
                this.readMoreTeamDescActivated = true;
            },
            deactivateMoreText() {
                this.readMoreTeamDescActivated = false;
            },
            setSelectedPlayer(player) {
                this.selectedPlayer = player;
            },
            newsClicked(news) {
                this.isShowNews = true;
                this.newsToShow = news;
                this.isNewsClicked = true;
            }
        },
        filters: {
            regExp(string) {
                return string.replace(/\[.*?\]/g, '').replace(/<\/?[^>]+(>|$)/g, '');

            },
            slice(string) {
                let strings = string.split("T")

                return strings[0].concat(" ", strings[1].slice(0, -1))

            }
        },
        created() {
            const readyHandler = () => {
                if (document.readyState == 'complete') {
                    this.loading = false;
                    this.loaded = true;
                    document.removeEventListener('readystatechange', readyHandler);
                }
            };

            axios.get(`https://www.thesportsdb.com/api/v1/json/1/searchteams.php?t=Arsenal`)
                .then(response => {
                    // JSON responses are automatically parsed.
                    this.selectedTeam = response.data.teams[0];
                    console.log(this.selectedTeam);
                });

            axios.get(`https://www.thesportsdb.com/api/v1/json/4013017/searchplayers.php?t=Arsenal`)
                .then(response => {
                    // JSON responses are automatically parsed.
                    this.teamPlayers = response.data.player;
                    console.log(this.teamPlayers);
                });

            axios.get(`https://www.thesportsdb.com/api/v1/json/1/eventsnext.php?id=133604`)
                .then(response => {
                    // JSON responses are automatically parsed.
                    this.teamEvents = response.data.events;
                    console.log(this.teamEvents);
                });

            axios.get(`http://newsapi.org/v2/top-headlines?country=pl&category=sports&apiKey=${this.apiKey1}`)
                .then(response => {
                    // JSON responses are automatically parsed.
                    this.news = response.data.articles;
                    console.log(this.news);
                });

            document.addEventListener('readystatechange', readyHandler);

            readyHandler(); // in case the component has been instantiated lately after loading

        },
        computed: {
            firstFields() {
                if (!this.firstModel) return []

                return Object.keys(this.firstModel).map(key => {
                    return {
                        key,
                        value: this.firstModel[key] || 'n/a',
                    }
                })
            },
            secondFields() {
                if (!this.secondModel) return []

                return Object.keys(this.secondModel).map(key => {
                    return {
                        key,
                        value: this.secondModel[key] || 'n/a',
                    }
                })
            },
            thirdFields() {
                if (!this.thirdModel) return []

                return Object.keys(this.thirdModel).map(key => {
                    return {
                        key,
                        value: this.thirdModel[key] || 'n/a',
                    }
                })
            },
            firstItems() {
                return this.countries.map(region => {
                    const country = region.country

                    return Object.assign({}, region, {country})
                })
            },
            secondItems() {
                return this.categories.map(category => {
                    const strSport = category.strSport

                    return Object.assign({}, category, {strSport})
                })
            },
            thirdItems() {
                return this.teams.map(team => {
                    const strTeam = team.strTeam

                    return Object.assign({}, team, {strTeam})
                })
            },
        },
        watch: {
            firstSearch() {
                // Items have already been loaded
                if (this.firstItems.length > 0) return

                // Items have already been requested
                if (this.firstIsLoading) return

                this.firstIsLoading = true

                // Lazily load input items
                fetch("https://api-football-v1.p.rapidapi.com/v2/countries", {
                    "method": "GET",
                    "headers": {
                        "x-rapidapi-host": "api-football-v1.p.rapidapi.com",
                        "x-rapidapi-key": "54aa1af517msh325e94573cd5588p194beejsn32fe5fe630c3"
                    }
                })
                    .then(res => res.json())
                    .then(res => {
                        const {results, countries} = res.api
                        this.count = results
                        this.countries = countries
                    })
                    .catch(err => {
                        console.log(err)
                    })
                    .finally(() => (this.firstIsLoading = false))
            },
            secondSearch() {
                // Items have already been loaded
                if (this.secondItems.length > 0) return

                // Items have already been requested
                if (this.secondIsLoading) return

                this.secondIsLoading = true

                // Lazily load input items
                fetch('https://www.thesportsdb.com/api/v1/json/1/all_sports.php')
                    .then(res => res.json())
                    .then(res => {
                        const {sports} = res
                        this.categories = sports
                    })
                    .catch(err => {
                        console.log(err)
                    })
                    .finally(() => (this.secondIsLoading = false))
            },
            thirdSearch() {
                // Items have already been loaded
                if (this.thirdItems.length > 0) return

                // Items have already been requested
                if (this.thirdIsLoading) return

                this.thirdIsLoading = true

                if (this.category != '' && this.region != '') {
                    fetch(`https://www.thesportsdb.com/api/v1/json/1/search_all_teams.php?s=${this.category}&c=${this.region}`)
                        .then(res => res.json())
                        .then(res => {
                            const {teams} = res
                            this.teams = teams
                        })
                        .catch(err => {
                            console.log(err)
                        })
                        .finally(() => (this.thirdIsLoading = false))
                }

            },
        },
    };
</script>

<style scoped>
    .shadow {
        -webkit-filter: drop-shadow(3px 3px 2px rgba(0, 0, 0, .7));
        filter: drop-shadow(3px 3px 2px rgba(0, 0, 0, .7));
        /* Similar syntax to box-shadow */
    }

    .shadow2 {
        -webkit-filter: drop-shadow(3px 3px 2px rgba(0, 0, 0, .3));
        filter: drop-shadow(3px 3px 2px rgba(0, 0, 0, .3));
        /* Similar syntax to box-shadow */

    }

    .social-icons {
        margin: 15px;
    }

    .fade-enter-active, .fade-leave-active {
        transition: opacity .5s;
    }

    .fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */
    {
        opacity: 0;
    }
</style>