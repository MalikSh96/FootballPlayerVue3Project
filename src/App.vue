<template>
  <!-- <ThePlayersComponent /> -->
  <!-- <the-players-component-copy /> -->
  <players-component-backup />
</template>

<script>
// import ThePlayersComponent from '@/views/players/ThePlayersComponent.vue'
// import ThePlayersComponentCopy from '@/views/players/ThePlayersComponentCopy.vue'
import PlayersComponentBackup from "@/views/players/PlayersComponentBackup.vue"

export default {
  name: 'App',
  components: {
    // ThePlayersComponent,
    // ThePlayersComponentCopy
    PlayersComponentBackup
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
