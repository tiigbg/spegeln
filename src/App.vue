<template>
  <div id="app" class="container-fluid vertically-stretched-container">

    <div class="row vertical-stretch top-row">
      <div class="col-md-6">
        <date-time></date-time>
      </div>
      <div class="col-md-6">
        <weather></weather>
      </div>
      
    </div>

    <div class="row vertical-stretch">
      <div class="col-md-12 text-center">
        <toilet-status></toilet-status>
      </div>
    </div>

    <div class="row vertical-stretch">
      <div class="col-md-12">
        <bus-table></bus-table>
      </div>
    </div>

    <div class="row bottom-message">
      <div id="message-column" class="col-md-12 text-center">
        <bottom-message></bottom-message>
      </div>
    </div>

  </div>
</template>

<script>
import busTable from './components/busTable.vue'
import weather from './components/weather.vue'
import bottomMessage from './components/bottomMessage.vue'
import toiletStatus from './components/toiletStatus.vue'
import dateTime from './components/dateTime.vue'

export default {
  components: {
    dateTime,
    busTable,
    weather,
    bottomMessage,
    toiletStatus,
  }
}
</script>

<style>

@import url(https://fonts.googleapis.com/css?family=Covered+By+Your+Grace);

html, body{
  height: 100%;
}

body {
  overflow:hidden;
	cursor: none;
  background-color: black;
  color: white;
  font-family: Helvetica, sans-serif;
}

/*.row{
  border: 2px white;
}*/

.vertically-stretched-container {
  display: flex;
  flex-direction: column;
  height: 100%;
}

.vertical-stretch {
  /*border: 2px solid deeppink;*/
  flex: 2;
}

.top-row {
  margin-top: 100px;
}

.bottom-message {
  /*border: 2px solid deeppink;*/
  margin-bottom: 100px;
  flex: 1;
  /*background-color: deeppink;*/
  position: relative;
}

#message-column {
  /*background-color: yellow;*/
  position: absolute;
  bottom: 10px;
}

</style>
