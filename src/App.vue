
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
<!--             Search section -->
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
                    @change="loadedOnce = false; readMoreTeamDescActivated = false; secondModel = null; category = ''; categories = []; thirdModel = null; team = ''; teams = []; selectedTeam = null; teamPlayers = null; selectedPlayer = null; teamEvents = null; animated = true">
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
               <v-img src="./assets/background.png" gradient="to top left, rgba(100,115,201,.75), rgba(70,70,70,.85)">
          <v-card :elevation="24" color="rgba(26, 35, 126, 0.5)"  class=" white--text col-md-8 col-sm-10 offset-md-2 offset-sm-1 my-md-10 my-5 ">

              <div class="Team_Info_container" v-if="firstLoadOfPage!=true && selectedTeam">
<!--             tooltip section-->
             <div
             class="d-flex justify-center animated pulse mb-5"
             :class="{infinite: animated}"
             >
              <v-tooltip top max-width='66.67%'>
              <template v-slot:activator="{ on }">
              <v-icon size=85 color="white" @mouseover="animated = false" v-on="on">{{ icon }}</v-icon>
              </template>
              <span v-if="width < 450" >{{description.slice(0, description.indexOf('.', 450) + 1)}}</span>
              <span v-else-if="description.length < 1000" >{{ description }}</span>
              <span v-else >{{description.slice(0, description.indexOf('.', 1000) + 1)}}</span>
              </v-tooltip>
              </div>


              <img v-if="selectedTeam.strTeamBanner" :src="selectedTeam.strTeamBanner" id="banner" class="v-picker--full-width mb-5 animated fadeIn">

<!--             team description section-->
              <div class="py-8 px-8"  v-if="selectedTeam.strDescriptionEN!=null" data-aos="fade-up">
                  <p v-if="!readMoreTeamDescActivated && isTeamDescToggleButton()" > {{selectedTeam.strDescriptionEN.slice(0, 435)}}...</p>
                  <p v-else > {{selectedTeam.strDescriptionEN}}</p>
                  <v-btn small color="indigo lighten-4" v-if="!readMoreTeamDescActivated  && isTeamDescToggleButton()" @click="activateMoreText">Read More</v-btn>
                  <v-btn small color="indigo lighten-4" v-else-if="readMoreTeamDescActivated && isTeamDescToggleButton()" @click="deactivateMoreText">Read Less</v-btn>
              </div>

<!--              team info section-->
              <v-card color="indigo darken-2 white--text" :elevation="10" class="mx-sm-8" data-aos="fade-up">
                  <v-row>
                      <div class="mx-sm-4 col-4 offset-sm-0 offset-4">
                          <v-img id="logo" class="verticallyCenter" :src="selectedTeam.strTeamBadge"></v-img>
                      </div>
                      <div class="col-sm-7" style="margin-top: auto; margin-bottom: auto">
                          <div v-for="(infoValue, infoKey) in teamInfo[0]" :key="infoKey">
                              <v-row v-if="selectedTeam[infoValue] != null && selectedTeam[infoValue].length > 0 && selectedTeam[infoValue] !== '0' && !selectedTeam[infoValue].startsWith('_')">
                                  <div class="col-md-4 col-sm-5 col-6 text-right py-0">
                                      <p class="font-weight-black"> {{infoKey}} : </p>
                                  </div>
                                  <div class="col-md-8 col-sm-7 col-6 text-left py-0">
                                      <p> {{selectedTeam[infoValue]}} </p>
                                  </div>
                              </v-row>
                          </div>
                      </div>
                  </v-row>
              </v-card>

              <!--              team players section-->
              <v-card v-if="teamPlayers" class="text-left mx-sm-8 pa-0 my-10" :elevation="10" data-aos="fade-up">
                  <v-card-title class="indigo darken-2 white--text headline">
                      Players
                  </v-card-title>
                  <v-row class="pa-4" justify="space-between">

                      <div class="col-12 col-md-6 text-center pl-0" style="margin-top: auto; margin-bottom: auto">
                          <div v-if="!selectedPlayer" class="title grey--text text--lighten-1 font-weight-light" style="align-self: center">
                              Select a Player
                          </div>
                          <v-card v-else flat class="animated flipInX">
                              <v-card-text>
                                  <v-avatar size="100">
                                      <v-img v-if="selectedPlayer.strCutout" :src="selectedPlayer.strCutout" class="mb-6"></v-img>
                                      <v-icon size="100" color="black" v-else>mdi-account</v-icon>
                                  </v-avatar>
                                  <h3 class="headline mb-2">
                                      {{ selectedPlayer.strPlayer }}
                                  </h3>
                              </v-card-text>
                              <v-divider></v-divider>
                              <div class="my-5">
                                  <v-row v-if="selectedPlayer.dateBorn" class="pa-0">
                                      <div class="offset-1 offset-md-0 col-5  pa-0 text-right font-regular mr-4 mb-2" tag="strong" cols="6">Date Born:</div>
                                      <div class="col-5 pa-0 text-left font-weight-light">{{selectedPlayer.dateBorn}}</div>
                                  </v-row>
                                  <v-row v-if="selectedPlayer.strNationality" class="pa-0">
                                      <div class="offset-1 offset-md-0 col-5  pa-0 text-right  font-regular mr-4 mb-2" tag="strong" cols="6">Nationality:</div>
                                      <div class="col-5 pa-0 text-left font-weight-light">{{ selectedPlayer.strNationality }}</div>
                                  </v-row>
                                  <v-row v-if="selectedPlayer.strNumber" class="pa-0">
                                      <div class="offset-1  offset-md-0 col-5 pa-0 text-right font-regular mr-4 mb-2" tag="strong" cols="6">Number:</div>
                                      <div class="col-5  pa-0 text-left font-weight-light">{{ selectedPlayer.strNumber }}</div>
                                  </v-row>
                                  <v-row v-if="selectedPlayer.strPosition" class="pa-0">
                                      <div class="offset-1 offset-md-0 col-5 pa-0 text-right font-regular mr-4 mb-2" tag="strong" cols="6">Position:</div>
                                      <div class="col-5 pa-0 text-left font-weight-light">{{ selectedPlayer.strPosition }}</div>
                                  </v-row>
                                  <v-row class="justify-center">
                                      <a v-if="selectedPlayer.strFacebook" target="_blank" v-bind:href="'http://' + selectedPlayer.strFacebook"><i class="fab fa-facebook-square fa-2x social-icons" style="color: #153bd4"></i></a>
                                      <a v-if="selectedPlayer.strTwitter" target="_blank" v-bind:href="'http://' + selectedPlayer.strTwitter"><i class="fab fa-twitter-square fa-2x social-icons" style="color: #3ca5cf"></i></a>
                                      <a v-if="selectedPlayer.strInstagram" target="_blank" v-bind:href="'http://' + selectedPlayer.strInstagram"><i class="fab fa-instagram-square fa-2x social-icons" style="color: #bd31d6"></i></a>
                                      <a v-if="selectedPlayer.strYoutube" target="_blank" v-bind:href="'http://' + selectedPlayer.strYoutube"><i class="fab fa-youtube fa-2x social-icons" style="color: red"></i></a>
                                  </v-row>
                              </div>
                          </v-card>
                      </div>
                      <div class="col-12 col-md-5 pr-0">
                          <v-list class="overflow-y-auto" max-height="400" three-line>
                              <template v-for="player in teamPlayers">
                                  <v-list-item @click="setSelectedPlayer(player)" :key="player.id">
                                      <v-list-item-avatar style="margin-top: auto; margin-bottom: auto">
                                          <v-img v-if="player.strCutout" :src="player.strCutout"></v-img>
                                          <v-icon color="black" v-else large>mdi-account</v-icon>
                                      </v-list-item-avatar>
                                      <v-list-item-title v-html="player.strPlayer"></v-list-item-title>
                                  </v-list-item>
                                  <v-divider v-if="teamPlayers.length> 1" :key="player.id"></v-divider>
                              </template>
                          </v-list>
                      </div>
                  </v-row>
              </v-card>

              <!--              team events section-->
              <v-card v-if="teamEvents" class="col-sm-8 offset-sm-2 pa-0 my-10" :elevation="10" data-aos="fade-up">
                  <v-toolbar class="indigo darken-2" dark>
                      <v-toolbar-title>Coming Events</v-toolbar-title>
                  </v-toolbar>
                  <v-list two-line>
                      <v-list-item-group>
                              <template v-for="(teamEvent, eventIndex) in teamEvents">
                                      <v-list-item v-on="on" :key="eventIndex" class="tooltip">
                                          <v-list-item-content class="text-left">
                                              <v-list-item-title class="font-weight-black" v-text="teamEvent.strEvent"> </v-list-item-title>
                                              <v-list-item-subtitle v-if="teamEvent.strHomeTeam" class="text--primary" ><b>Home: </b> {{teamEvent.strHomeTeam}}</v-list-item-subtitle>
                                              <v-list-item-subtitle v-if="teamEvent.strAwayTeam" class="text--primary" ><b>Away: </b> {{teamEvent.strAwayTeam}}</v-list-item-subtitle>
                                              <v-list-item-subtitle class="text--primary" ><b>League: </b> {{teamEvent.strLeague}}</v-list-item-subtitle>
                                          </v-list-item-content>
                                          <v-list-item-action>
                                              <v-list-item-action-text v-text="teamEvent.dateEvent" ></v-list-item-action-text>
                                              <v-list-item-action-text v-if="teamEvent.strTimeLocal!=null" v-text="teamEvent.strTimeLocal.slice(0,-3)" ></v-list-item-action-text>
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
              data-aos="fade-up"
            >
              <v-card-title class="indigo darken-2 white--text headline">Sport News</v-card-title>
                <v-tooltip top color="indigo accent-3" :disabled=isNewsClicked>
                  <template v-slot:activator="{ on }" >
              <v-carousel
                class="animated fadeIn slower"
                hide-delimiters
                @change="newsToShow = news[$event]"
              >

                <v-carousel-item
                  class="carousel__item clickable tooltip "
                  @click="newsClicked(item)"
                  v-for="(item,index) in news"
                  :key="item.urlToImage"
                  :class="{ active: index == 0 }"
                  reverse-transition="fade"
                  transition="fade"
                  v-on="on"
                >
                  <v-card>
                    <v-img v-if="item.urlToImage!=null"  :src="item.urlToImage" height="400px"></v-img>
                    <v-img v-else :src="require('@/assets/noNews.jpg')" height="400px"></v-img>
                    <v-card-title class="mx-0 my-0">{{ item.title.slice(0,lengthOfNewsTitle) }}...</v-card-title>
                  </v-card>
                </v-carousel-item>
              </v-carousel>
                </template>
                  <span>Click to find out more</span>
              </v-tooltip>
            </v-card>

<!--            News section-->
          <v-card color="indigo darken-2 white--text" :elevation="10" class="mx-sm-8">
            <div id="newsContainer" class="pa-5" v-if="isShowNews === true && newsToShow.publishedAt!=null">
                <p>{{ newsToShow.publishedAt.replace("T", " ").replace("Z", "") }}</p>
                <h3 class="text-center">{{ newsToShow.title }}</h3>
                    <p class="my-5" v-if="newsToShow.description!=null">
                        <b>{{ newsToShow.description }}</b>
                    </p>
                    <p class="mb-5" v-if="newsToShow.content!=null">
                        {{ newsToShow.content | regExp() }}
                    <a :href="newsToShow.url" target="_blank" >Click here to read full article</a>
                    </p>
            </div>
            </v-card>
          </v-card>
               </v-img>
      </div>

<!--             footer -->
  <CreditFooter/>
    </v-app>
</template>


<script>
    import axios from "axios";
    import CreditFooter from './components/CreditFooter.vue'
    export default {
        name: 'App',
        components: {
          CreditFooter
        },
        data() {
            return {
              apiKey1: '15de284d0e884be19dcf6c92c4a88205',
              apiKey2: '4013017',
              urlBase: 'https://www.thesportsdb.com/api/v1/json/',
              news: null,
              show: false,
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
              selectedTeam: null,
              teamInfo: [{Team:'strTeam', Formed:'intFormedYear', League:'strLeague', Stadium: 'strStadium', Country: 'strCountry'}],
              teamDescToggleButton: false,
              teamPlayers: null,
              selectedPlayer: null,
              teamEvents: null,
              readMoreTeamDescActivated: false,
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
              isShowNews: false,
              newsToShow: null,
              isNewsClicked: false,
              firstLoadOfPage: true,
              lengthOfNewsTitle: 56,
              overlay: false,
              alert: true,
              animated: true,
              description: '',
              loadedOnce: true,
              width: screen.width,
            }
        },
  methods: {
    fetchApi() {
      axios.all([
        axios.get(`${this.urlBase}${this.apiKey2}/searchteams.php?t=${this.team}`),
        axios.get(`${this.urlBase}${this.apiKey2}/searchplayers.php?t=${this.team}`)
                ])
          .then(response => (
            this.selectedTeam = response[0].data.teams[0],
            console.log(this.selectedTeam),
            this.teamPlayers = response[1].data.player,
                console.log(this.teamPlayers),
            axios.get(`${this.urlBase}${this.apiKey2}/eventsnext.php?id=${this.selectedTeam.idTeam}`)
                .then(response => {
                    this.teamEvents = response.data.events;
                })
));
this.firstLoadOfPage=false;
      },
    handleFirstInput (value) {
      this.region = value.country;
      },
    handleSecondInput (value) {
      this.category = value.strSport;
      this.description = value.strSportDescription
      var i;
      for (i = 0; i < this.categories.length; i++) {
        if (this.category == `${this.categories[i].strSport}`) {
          this.image = require(`@/assets/${this.categories[i].strSport}.jpeg`);
          this.icon = this.icons[i]
          this.placeholder = this.placeholders[i]
          }}},
    handleThirdInput (value) {
      this.team = value.strTeam;
      console.log(this.team)
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
    },
    isTeamDescToggleButton()
            {
                return this.selectedTeam.strDescriptionEN.length > 435 ? true : false;
            }
  },
  filters: {
    regExp(string) {
        return string.replace(/\[.*?\]/g, '').replace(/<\/?[^>]+(>|$)/g, '');
    }
},
created() {
  axios.get(`https://newsapi.org/v2/top-headlines?country=gb&category=sports&apiKey=${this.apiKey1}`)
                .then(response => {
                    this.news = response.data.articles;
                    for (var i = 0; i < this.news.length; i++)
                if (this.news[i].urlToImage == null) {
                this.news.splice(i,1);
                }});
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
      },
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
            console.log(this.countries)
            let keys = Object.keys(this.countries)
            let indexes = [6, 9, 12, 16, 17, 18, 21, 24, 25, 31, 44, 53, 55, 78, 82, 84, 92, 109, 122, 127, 129, 132, 141]
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
        fetch(`${this.urlBase}${this.apiKey2}/all_sports.php`)
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
          fetch(`${this.urlBase}${this.apiKey2}/search_all_teams.php?s=${this.category}&c=${this.region}`)
            .then(res => res.json())
            .then(res => {
              if (res.teams != null) {
              const { teams } = res
              this.teams = teams
              for (var i = 0; i < this.teams.length; i++)
                if (this.teams[i].strTeam.startsWith('_')) {
                this.teams.splice(i,1);
                if (this.teams.length == 0) {
                  this.overlay = true
                  this.thirdSearch = null
                }
                }
              }
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
    .clickable {
      cursor: pointer;
    }
    .verticallyCenter {
        margin: 0;
        top: 50%;
        transform: translateY(-50%);
    }
    .carousel__item {
      filter: brightness(70%)
    }
    #logo {
      -webkit-filter: drop-shadow( 3px 3px 7.5px rgba(0, 0, 0, .7));
      filter: drop-shadow( 3px 3px 7.5px rgba(0, 0, 0, .7));
    }
    #banner {
      filter: brightness(90%)
}
</style>