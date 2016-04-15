<template>
  <div id="app">
    <weather></weather>
    <toilet-status></toilet-status>
    <bottom-message></bottom-message>
    <bus-table></bus-table>
  </div>
</template>

<script>
import busTable from './components/busTable.vue'
import weather from './components/weather.vue'
import bottomMessage from './components/bottomMessage.vue'
import toiletStatus from './components/toiletStatus.vue'

export default {
  components: {
    busTable,
    weather,
    bottomMessage,
    toiletStatus,
  }
}
</script>

<style>
body {
	/*cursor: none;*/
  background-color: black;
  color: white;
  font-family: Helvetica, sans-serif;
}
</style>
