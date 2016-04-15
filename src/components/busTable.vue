<template>
    <!-- <div>{{locationObjects |json}}</div> -->
    <h2 v-on:click="getDepartureBoard(locationObjects.lindholmen)">locationObjects.lindholmen.stop.name</h2>
    <!-- <pre>{{departureBoard}}</pre> -->
    <pre>{{boards |json}}</pre>
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
      // locationNames: ['lindholmen', 'lindholmspiren'],
      boards: {
        lindholmspiren: {
          stop: {},
          to:{
            stenpiren:{
              stop:{},
              departures:{},
            },
          }
        },
        lindholmsplatsen:{
          stop: {},
          to:{
            regnbågsgatan:{
              stop:{},
              departures:{},
            },
          }
        },
        lindholmen: {
          stop: {},
          to:{
            regnbågsgatan:{
              stop: {},
              departures: {}
            },
          }
        },
      },
      departureBoard: {},
    }
  },
  methods: {
    // getLocationObject(name){
    //   this.$http.get('https://api.vasttrafik.se/bin/rest.exe/v2/location.name', 
    //     null,
    //     {
    //       params: {
    //         format: 'json',
    //         input: name,
    //       },
    //       headers: {
    //         Authorization: this.accessToken,
    //       }
    //     }).then(
    //       function(response){
    //         this.$set('boards.'+name+'.stop', response.data.LocationList.StopLocation[0]);
    //       },
    //       function(response){
    //         this.$set('boards.'+name+'.stop', 'dålig response när jag försökte hämta hållplats'+name);
    //       }
    //     );
    //   // setTimeout(this.updateLocation, 10000);
    // },
    setLocationData(){
      for(stopName in this.boards){
        if (this.boards.hasOwnProperty(stopName)) {
          // console.log(stopName + " -> " + this.boards[stopName]);
          this.$http.get('https://api.vasttrafik.se/bin/rest.exe/v2/location.name', 
            null,
            {
              params: {
                format: 'json',
                input: stopName,
              },
              headers: {
                Authorization: this.accessToken,
              }
            }).then(
              function(response){
                //we use the reference to the stop name in the request inside the response since the stopName variable might be overwritten when this callback gets fired
                this.boards[response.request.params.input].stop = response.data.LocationList.StopLocation[0];
              },
              function(response){
                this.boards[response.request.params.input].stop = "fuuuuck";
              }
            );

          for(destName in this.boards[stopName].to){
            if (this.boards[stopName].to.hasOwnProperty(destName)) {
              this.$http.get('https://api.vasttrafik.se/bin/rest.exe/v2/location.name', 
                null,
                {
                  params: {
                    format: 'json',
                    input: destName,
                    parent: stopName,
                  },
                  headers: {
                    Authorization: this.accessToken,
                  }
                }).then(
                  function(response){
                    //we use the reference to the stop names in the request inside the response since the stopName and destName variables might be overwritten when this callback gets fired
                    var stopObj = this.boards[response.request.params.parent];
                    var destObj = stopObj.to[response.request.params.input];
                    destObj.stop = response.data.LocationList.StopLocation[0];
                    this.getDepartureBoard(stopObj, response.request.params.parent, destObj, response.request.params.input);
                  },
                  function(response){
                    // this.boards[response.request.params.parent].to[response.request.params.input].stop = "fuuuuck";
                  }
                );
            }
          }
        }
      }
          
      // setTimeout(this.updateLocation, 10000);
    },
    getDepartureBoard(fromObj, fromName, toObj, toName){
      // console.log(locationObject);
      // this.setNeighbourLocations(locationObject);
      // console.log(this.locationObjects.lindholmen.neighbours);
      
      var date = new Date();

      this.$http.get('https://api.vasttrafik.se/bin/rest.exe/v2/departureBoard', 
        null,
        {
          params: {
            format: 'json',
            id: fromObj.stop.id,
            date: date.toLocaleDateString(),
            time: date.getHours()+'.'+date.getMinutes(),
            direction: toObj.stop.id,
            from: fromName,
            to: toName,
          },
          headers: {
            Authorization: this.accessToken,
          }
        }).then(
          function(response){
            // this.$set('departureBoard.'+name, response.data.DepartureBoard);
            this.boards[response.request.params.from].to[response.request.params.to].departures = response.data.DepartureBoard;
          },
          function(response){
            // this.boards[response.request.params.from].to[response.request.params.to].departures = 'dålig response när jag försökte hämta avgångar för hållplats '+fromObj.stop.name;
          }
        );
    },
    // setNeighbourLocations(locationObject){
    //   this.$http.get('https://api.vasttrafik.se/bin/rest.exe/v2/location.nearbystops', 
    //     null,
    //     {
    //       params: {
    //         format: 'json',
    //         originCoordLat: locationObject.stop.lat,
    //         originCoordLong: locationObject.stop.lon,
    //       },
    //       headers: {
    //         Authorization: this.accessToken,
    //       }
    //     }).then(
    //       function(response){
    //         locationObject.neighbours =  response.data.LocationList.StopLocation;
    //       },
    //       function(response){
    //         locationObject.neighbours =  "skit också!";
    //       }
    //     );
    // }
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

          // this.getLocationObject(this.locationNames[0]);
          this.setLocationData();
          // this.getLocationObject(this.locationNames[0]);
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
