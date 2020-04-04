<template>
    <v-app>
        <v-card color="teal accent-3" dark class="d-flex flex-column">
            <v-img :src="image" :key="image" height="600"
                   gradient="to bottom right, rgba(100,115,201,.75), rgba(25,32,72,.5)">
                <v-card-title class="headline deep-purple lighten-1">
                    Search for your favourite team
                    <v-spacer></v-spacer>
                    <span class="caption font-italic font-weight-light">SPORTSNEWSAPP</span>
                    <v-icon right>mdi-newspaper-variant-multiple-outline</v-icon>
                </v-card-title>
                <v-card-text class="mt-5 overline">
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
                            :loading="firstIsLoading"
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
                    ></v-autocomplete>
                    <v-autocomplete
                            class="d-inline-flex elevation-23 mr-5 mb-5 pl-5 pr-2"
                            v-model="secondModel"
                            :items="secondItems"
                            :loading="secondIsLoading"
                            :search-input.sync="secondSearch"
                            color="white"
                            hide-no-data
                            hide-selected
                            rounded
                            item-text="strSport"
                            item-value="idSport"
                            label="sports category"
                            placeholder="e.g. Soccer"
                            prepend-icon="mdi-soccer"
                            return-object
                            @input="handleSecondInput"
                    ></v-autocomplete>
                    <v-autocomplete
                            class="d-inline-flex elevation-24 pl-5 pr-2"
                            v-model="thirdModel"
                            :items="thirdItems"
                            :loading="thirdIsLoading"
                            :search-input.sync="thirdSearch"
                            color="white"
                            hide-no-data
                            hide-selected
                            rounded
                            item-text="strTeam"
                            item-value="idTeam"
                            label="team"
                            placeholder="e.g. Lech Poznan"
                            prepend-icon="mdi-account-group-outline"
                            return-object
                            :disabled="!(firstModel && secondModel)"
                            @input="handleThirdInput"
                    ></v-autocomplete>
                </v-card-text>
                <v-card-actions class="ma-5">
                    <v-btn
                            :disabled="!thirdModel"
                            color="grey darken-3"
                            @click="fetchApi"
                            style="position: absolute;
left:    25px;
bottom:   20px;"
                    >
                        <v-icon right>mdi-magnify</v-icon>
                        Search
                    </v-btn>
                    <v-spacer></v-spacer>
                    <v-btn
                            :disabled="!(firstModel || secondModel || thirdModel)"
                            color="grey darken-3"
                            @click="firstModel = ''; secondModel = null; thirdModel = null"
                            style="position: absolute;
right:    25px;
bottom:   20px;"
                    >
                        Clear
                        <v-icon right>mdi-close-circle</v-icon>
                    </v-btn>
                </v-card-actions>
            </v-img>
        </v-card>
        <div v-if="news">
            <v-carousel light continuous height hide-delimiters>
                <v-carousel-item class="ma-5" v-for="item in news.data.articles" :key="item.urlToImage">
                    <v-card class="mx-auto" max-width="800">
                        <v-img :src="item.urlToImage" height="200px"></v-img>
                        <v-card-title>{{ item.title }}</v-card-title>
                        <v-card-subtitle>{{ item.description }}</v-card-subtitle>
                        <v-card-actions>
                            <v-btn icon @click="show = !show">
                                <v-icon>{{ show ? 'mdi-chevron-up' : 'mdi-chevron-down' }}</v-icon>
                            </v-btn>
                        </v-card-actions>
                        <v-expand-transition>
                            <div v-show="show">
                                <v-divider></v-divider>
                                <v-card-text>
                                    {{ item.content | regExp }}
                                    <a :href="item.url" target="_blank">Click here to read full article </a>
                                </v-card-text>
                            </div>
                        </v-expand-transition>
                    </v-card>
                </v-carousel-item>
            </v-carousel>
        </div>
<!--        <div class="container" v-if="clubs">-->
<!--            <div class="col" v-for="item in clubs.data.teams" :key="item.strTeam">-->
<!--                <h1>{{ item.strTeam }}</h1>-->
<!--                <img :src="item.strTeamBadge" alt="">-->
<!--                <h6>Established: {{ item.intFormedYear }}</h6>-->
<!--                <h6>Sport: {{ item.strSport }}</h6>-->
<!--                <h6>League: {{ item.strLeague }}</h6>-->
<!--                <h6>Stadium: {{ item.strStadium }}</h6>-->
<!--            </div>-->
<!--            <div class="col">-->
<!--            </div>-->
<!--        </div>-->

      <div class="indigo darken-1 text-center">
          <v-card :elevation="24" class=" indigo darken-4 white--text col-md-8 col-10 offset-md-2 offset-1 my-md-10 my-5">

              <img :src="team.strTeamBanner" class="v-picker--full-width mb-5" >

              <div class="py-8 px-8">
                  <p v-if="!readMoreTeamDescActivated" > {{team.strDescriptionEN.slice(0, 435)}}...</p>
                  <p v-if="readMoreTeamDescActivated" > {{team.strDescriptionEN}}</p>
                  <v-btn small color="indigo lighten-4" v-if="!readMoreTeamDescActivated" @click="activateMoreText">Read More</v-btn>
                  <v-btn small color="indigo lighten-4" v-if="readMoreTeamDescActivated" @click="deactivateMoreText">Read Less</v-btn>
              </div>

              <v-card color="indigo darken-2 white--text" :elevation="10" class="mx-8">
                  <v-row>
                      <div class="mx-4 col-md-4">
                          <v-img :src="team.strTeamBadge"></v-img>
                      </div>
                      <div class="col-md-7">
                          <v-row v-for="(infoValue, infoKey) in teamInfo[0]" :key="infoKey" >
                              <div class="col-4 text-right py-0">
                                  <p class="font-weight-black"> {{infoKey}} </p>
                              </div>
                              <div class="col-8 text-left py-0">
                                  <p> {{team[infoValue]}} </p>
                              </div>
                          </v-row>
                      </div>
                  </v-row>
              </v-card>

              <v-card class="text-left mx-8 my-10" :elevation="10">
                  <v-card-title class="indigo darken-2 white--text headline">
                      Players
                  </v-card-title>
                  <v-row class="pa-4" justify="space-between">
                      <v-col cols="5">
                          <v-list class="overflow-y-auto" max-height="500" three-line>
                              <template v-for="player in teamPlayers" >
                                  <v-list-item @click="setSelectedPlayer(player)" :key="player.strTeam">
                                      <v-list-item-avatar>
                                          <v-img :src="player.strCutout"></v-img>
                                      </v-list-item-avatar>
                                      <v-list-item-title v-html="player.strPlayer"></v-list-item-title>
                                  </v-list-item>
                              </template>
                          </v-list>
                      </v-col>

                      <v-divider vertical></v-divider>

                      <v-col
                              class="d-flex text-center"
                      >
                          <v-scroll-y-transition mode="out-in">
                              <div v-if="!selectedPlayer" class="title grey--text text--lighten-1 font-weight-light" style="align-self: center;">
                                  Select a User
                              </div>
                              <v-card v-else :key="selectedPlayer.id" class="pt-6 mx-auto" flat max-width="400">
                                  <v-card-text>
                                      <v-avatar size="100">
                                          <v-img :src="selectedPlayer.strCutout" class="mb-6"></v-img>
                                      </v-avatar>
                                      <h3 class="headline mb-2">
                                          {{ selectedPlayer.strPlayer }}
                                      </h3>
                                  </v-card-text>
                                  <v-divider></v-divider>
                                  <v-row class="text-left" tag="v-card-text">
                                      <v-col class="text-right mr-4 mb-2" tag="strong" cols="5">Date Born:</v-col>
                                      <v-col>{{selectedPlayer.dateBorn}}</v-col>
                                      <v-col class="text-right mr-4 mb-2" tag="strong" cols="5">Nationality:</v-col>
                                      <v-col>{{ selectedPlayer.strNationality }}</v-col>
                                      <v-col class="text-right mr-4 mb-2" tag="strong" cols="5">Number:</v-col>
                                      <v-col>{{ selectedPlayer.strNumber }}</v-col>
                                      <v-col class="text-right mr-4 mb-2" tag="strong" cols="5">Position:</v-col>
                                      <v-col>{{ selectedPlayer.strPosition }}</v-col>
                                  </v-row>
                                  <v-row class="col-md-10 offset-md-1 mx-3">
                                      <i class="fab fa-facebook"></i>
                                  </v-row>
                              </v-card>
                          </v-scroll-y-transition>
                      </v-col>
                  </v-row>
              </v-card>

              <v-card class="col-md-6 offset-md-3 pa-0 " :elevation="10">
                  <v-toolbar class="indigo darken-2" dark>
                      <v-toolbar-title>Inbox</v-toolbar-title>
                  </v-toolbar>
                  <v-list two-line>
                      <v-list-item-group>
<!--                          <template v-for="()">-->

<!--                          </template>-->
                      </v-list-item-group>
                  </v-list>
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
                urlBase1: 'http://newsapi.org/v2/everything?q=',
                urlBase2: 'https://www.thesportsdb.com/api/v1/json/',
                clubs: null,
                news: null,
                show: false,
                loading: true,
                loaded: false,
                count: [],
                countries: [],
                categories: [],
                teams: [],
                firstIsLoading: false,
                secondIsLoading: false,
                thirdIsLoading: false,
                firstModel: null,
                secondModel: null,
                thirdModel: null,
                firstSearch: null,
                secondSearch: null,
                thirdSearch: null,
                region: '',
                category: '',
                team: '',
                teamInfo: [{Team:'strTeam', Formed:'intFormedYear', League:'strLeague', Stadium: 'strStadium', Country: 'strCountry'}],
                teamPlayers: null,
                selectedPlayer: null,
                teamEvents: null,
                readMoreTeamDescActivated: false,
                image: require('@/assets/soccer.jpg')
            }
        },
        methods: {
            fetchApi() {
                axios.all([
                    axios.get(`${this.urlBase1}${this.category}&language=en&sortBy=publishedAt&apiKey=${this.apiKey1}`),
                    axios.get(`${this.urlBase2}${this.apiKey2}/searchteams.php?t=${this.team}`)
                ])
                    .then(response => (
                        this.news = response[0],
                            this.clubs = response[1]
                    ));
                console.log(this.news),
                    console.log(this.clubs)
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

            axios.get(`https://www.thesportsdb.com/api/v1/json/1/searchteams.php?t=Chelsea`)
                .then(response => {
                    // JSON responses are automatically parsed.
                    this.team = response.data.teams[0];
                    console.log(this.team);
                });

            axios.get(`https://www.thesportsdb.com/api/v1/json/4013017/searchplayers.php?t=Chelsea`)
                .then(response => {
                    // JSON responses are automatically parsed.
                    this.teamPlayers = response.data.player;
                    console.log(this.teamPlayers);
                });

            axios.get(`https://www.thesportsdb.com/api/v1/json/1/eventsnext.php?id=133738`)
                .then(response => {
                    // JSON responses are automatically parsed.
                    this.teamEvents = response.data.events;
                    console.log(this.teamEvents);
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

            // selected () {
            //     if (!this.active.length) return undefined
            //
            //     const id = this.active[0]
            //
            //     return this.users.find(user => user.id === id)
            // },
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

    .fade-enter-active, .fade-leave-active {
        transition: opacity .5s;
    }

    .fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */
    {
        opacity: 0;
    }
</style>