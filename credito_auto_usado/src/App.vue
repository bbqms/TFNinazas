<template>
  <v-app>
    <v-toolbar app color="#1E88E5">
      <v-toolbar-title class="headline text-uppercase">
        <span>Plan de Pagos</span>
        <span class="font-weight-light"> - BCP</span>
      </v-toolbar-title>
      <v-spacer></v-spacer>
    </v-toolbar>

    <v-content>
      <router-view/>
    </v-content>
  </v-app>
</template>

<script>

export default {
  name: 'App',
  data () {
    return {
      //
    }
  }
}
</script>
