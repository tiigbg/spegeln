<template>
    <!-- <div>{{locationObjects |json}}</div> -->
    <h2 v-on:click="getDepartureBoard(locationObjects.lindholmen)">locationObjects.lindholmen.stop.name</h2>
    <pre>{{departureBoard}}</pre>
    <pre>{{locationObjects |json}}</pre>
    <!-- <div>{{locationData.LocationList.StopLocation[0].name | json}}</div> -->
    <!-- <pre>{{locationData | json}}</pre> -->

</template>

<script>
// var $ = require('jquery');
export default {
  data () {
    return {
      // Note: modifying `msg` below will not cause changes to occur with
      // hot-reload. As reloaded components preserve their initial state,
      // modifying these values will have no effect.
      accessToken: '',
      locationNames: ['lindholmen', 'lindholmspiren'],
      locationObjects: {
        lindholmen: {
          stop: {},
          neighbours:{}
        },
        lindholmspiren: {
          stop: {},
          neighbours:{}
        },
      },
      departureBoard: {},
    }
  },
  methods: {
    getLocationObject(name){
      this.$http.get('https://api.vasttrafik.se/bin/rest.exe/v2/location.name', 
        null,
        {
          params: {
            format: 'json',
            input: name,
          },
          headers: {
            Authorization: this.accessToken,
          }
        }).then(
          function(response){
            this.$set('locationObjects.'+name+'.stop', response.data.LocationList.StopLocation[0]);
          },
          function(response){
            this.$set('locationObjects.'+name+'.stop', 'dålig response när jag försökte hämta hållplats'+name);
          }
        );
      // setTimeout(this.updateLocation, 10000);
    },
    getDepartureBoard(locationObject){

      console.log(locationObject);

      this.setNeighbourLocations(locationObject);

      console.log(this.locationObjects.lindholmen.neighbours);
      var date = new Date();

      this.$http.get('https://api.vasttrafik.se/bin/rest.exe/v2/departureBoard', 
        null,
        {
          params: {
            format: 'json',
            id: locationObject.stop.id,
            date: date.toLocaleDateString(),
            time: date.getHours()+'.'+date.getMinutes(),
            direction: locationObject.stop.id
          },
          headers: {
            Authorization: this.accessToken,
          }
        }).then(
          function(response){
            this.$set('departureBoard.'+name, response.data.DepartureBoard);
          },
          function(response){
            this.$set('departureBoard.'+name, 'dålig response när jag försökte hämta hållplats'+name);
          }
        );
    },
    setNeighbourLocations(locationObject){
      this.$http.get('https://api.vasttrafik.se/bin/rest.exe/v2/location.nearbystops', 
        null,
        {
          params: {
            format: 'json',
            originCoordLat: locationObject.stop.lat,
            originCoordLong: locationObject.stop.lon,
          },
          headers: {
            Authorization: this.accessToken,
          }
        }).then(
          function(response){
            locationObject.neighbours =  response.data.LocationList.StopLocation;
          },
          function(response){
            locationObject.neighbours =  "skit också!";
          }
        );
    }
  },
  ready: function(){
    this.$http.post('https://api.vasttrafik.se/token',
      {
        grant_type: 'client_credentials',
        scope: 'device_555'
      },
      {
        headers: {
          Authorization: 'Basic TEJaemp3cFM3OTVIdzJKMm1SMWxIcVcxOWFrYTo0QWxRZUZsZjlQTTlGUjA1Q3ZSaVlfNlQzNW9h',
        },
        emulateJSON: true,
      }).then(
        function(response){
          this.msg = 'ja! fick svar';
          this.accessToken = response.data.token_type
            + ' ' 
            + response.data.access_token;
          this.response = response;

          this.getLocationObject(this.locationNames[0]);
          this.getLocationObject(this.locationNames[0]);
        },
        function(response){
          this.msg = 'Inget svar!';
          this.response = response.data;
        }
      );
  }
}
</script>

<style>

</style>
