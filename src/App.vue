<template>
  <v-app>
    <v-app-bar
      app
    >
      <v-toolbar-title>Générateur</v-toolbar-title>

      <v-spacer></v-spacer>

      <v-btn
        href="https://media.interieur.gouv.fr/deplacement-covid-19/"
        target="_blank"
        text
      >
        <span class="mr-2">Site officiel</span>
        <v-icon>mdi-open-in-new</v-icon>
      </v-btn>
    </v-app-bar>

    <v-content>
      <HelloWorld/>
    </v-content>
  </v-app>
</template>

<script>
import HelloWorld from './components/HelloWorld';

export default {
  name: 'App',

  components: {
    HelloWorld,
  },

  data: () => ({
    //
  }),
};
</script>
