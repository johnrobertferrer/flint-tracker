<template>
  <v-app>
    <navigation
      :tabs="tabs"
    />

    <v-main class="grey lighten-3">
      <router-view/>
    </v-main>
  </v-app>
</template>

<script>
  import Navigation from './components/Navigation.vue';

  export default {
    name: 'App',

    components: {
      'navigation': Navigation
    },

    data: () => ({
      tabs: [
        {   url: '/', label: 'Dashboard'   },
        {   url: '/transactions', label: 'Transactions'   },
        {   url: '/reports', label: 'Reports'   },
        {   url: '/settings', label: 'Settings'   }
      ]
    }),
  };
</script>
