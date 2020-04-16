<template>
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
          <v-img class="mx-5 shadow2" max-width="10%" height="100%" src="../assets/newsapi.png"></v-img>
          <v-img class="ml-5 shadow" max-width="7%" max-height="100%" src="https://www.api-football.com/public/img/home1/hero-banner1.png"></v-img>
        </v-row>
        <v-divider class="mx-10 mt-5"></v-divider>
        <v-card-text class="white--text">
          <strong>{{ new Date().getFullYear() }} ©</strong>
        </v-card-text>
      </v-card>
    </v-footer>
</template>

<script>
export default {
  name: 'HelloWorld',
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
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
</style>
