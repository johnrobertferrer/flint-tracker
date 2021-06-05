<template>
  <v-app-bar
      app
      color="#009688"
      flat
      dark
    >
      <v-tabs
        centered
        class="ml-n9"
        color="white"
      >
        <v-tab
          v-for="(tab, index) in tabs"
          :key="'tab-' + index"
          :to="tab.url"
          v-html="tab.label"
          dark
        />
      </v-tabs>
    </v-app-bar>
</template>

<script>
  export default {
    name: 'Navigation',

    props: [
      'tabs'
    ]
  }
</script>

<style scoped>

</style>