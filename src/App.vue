<template>
  <players-component-backup />
</template>

<script>
import PlayersComponentBackup from "@/views/players/PlayersComponentBackup.vue"

export default {
  name: 'App',
  components: {
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
