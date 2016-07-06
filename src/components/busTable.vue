<template>
    <!-- <div>{{locationObjects |json}}</div> -->
    <!-- <template v-for="board in boards">
      <h2>{{board.stop.name}}</h2>
      <template v-for="dest in board.to">
        <p>MOT {{dest.stop.name}}</p>
        <template v-if="dest.departures" >
          <p v-for="line in dest.departures | limitBy 5"> {{line.name}},{{line.direction}},{{line.time}}</p>
        </template>
      </template>
    </template> -->
    <h1>Mot Stan om {{Math.floor(untilNext)+1}} {{Math.floor(untilNext)+1<2?'minut':'minuter'}}</h1>
    <table id="bus-table" class="table">
      <tr v-for="line in timeSortedList | limitBy 5">
        <td>{{line.name}}</td>
        <td>{{line.direction}}</td>
        <td v-bind:class="{'blink-me': line.blink}">{{line.time}}</td>
      </tr>
    <!-- <pre>{{departureBoard}}</pre> -->
    <!-- <pre>{{boards |json}}</pre> -->
    <!-- <div>{{locationData.LocationList.StopLocation[0].name | json}}</div> -->
    <!-- <pre>{{locationData | json}}</pre> -->

</template>

<script>
// var $ = require('jquery');
export default {
  data: function () {
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
              departures: [],
            },
          }
        },
        lindholmsplatsen:{
          stop: {},
          to:{
            regnbågsgatan:{
              stop:{},
              departures: [],
            },
          }
        },
        lindholmen: {
          stop: {},
          to:{
            nordstan:{
              stop: {},
              departures: [],
            },
          }
        },
      },
      // departureBoard: {},
      now: new Date(),
      // timeSortedList: [],
    }
  },
  computed: {
    // blinkTopOne: function(){
    //   // return false;
    //   if(!this.timeSortedList[0]){
    //     return false;
    //   }
    //   // var departTime = new Date(this.timeSortedList[0].date+' '+this.timeSortedList[0].time);
    //   var diff = (new Date(this.timeSortedList[0].date+' '+this.timeSortedList[0].time) - this.now) /1000 / 60;
    //   // console.log(diff);
    //   if(diff < 4){
    //     return true;
    //   }else{
    //     return false;
    //   }
    // },
    untilNext: function(){
      if(!this.timeSortedList[0]){
        return '';
      }
      // var nextDep = new Date(this.timeSortedList[0].date+' '+this.timeSortedList[0].time);
      // var result = (nextDep - this.now) /1000 / 60;
      // return result;

      return this.timeSortedList[0].untilDeparture;
    },
    timeSortedList: function(){
      list = [];
      for(stopName in this.boards){
        if(this.boards.hasOwnProperty(stopName)){
          for(destName in this.boards[stopName].to){
            if(this.boards[stopName].to.hasOwnProperty(destName)){
              if(this.boards[stopName].to[destName].departures){
                list = list.concat(this.boards[stopName].to[destName].departures);
              }
            }
          }
        }
      }
      if(!list){
        console.log("empty timeSortedList!");
        return '';
      }

      var i = list.length;
      while(i--){
        // list[i].departTime = new Date(list[i].date+' '+list[i].time);
        // list[i].departTime = list[i].date.substr(0,4) + '_' + list[i].date.substr(5,2) + '_' + list[i].date.substr(8,2) + '_' + list[i].time.substr(0,2) + '_' + list[i].time.substr(3,2);
        //let's keep this safe and convert directly to milliseconds, bypassing risks with locale date formats
        var depTime = (new Date(list[i].date.substr(0,4), list[i].date.substr(5, 2)-1, list[i].date.substr(8, 2), list[i].time.substr(0,2), list[i].time.substr(3,2)));

        list[i].departTime = depTime;

        //Date(year, month[, day[, hour[, minutes[, seconds[, milliseconds]]]]]);
        console.log('Now: ' + this.now);
        console.log('Depart: ' + depTime);
        console.log('Difference: ' + (depTime - this.now));
        var diff = (list[i].departTime - this.now) /1000 / 60;
        list[i].untilDeparture = diff;
        list[i].blink = diff < 4;
        
        //If passed delete
        if(diff< 0){
          list.splice(i, 1);
        }
      }

      list.sort(function(a, b){
        // a = new Date(a.date+' '+a.time);
        // b = new Date(b.date+' '+b.time);
        return a.departTime<b.departTime?-1:a.departTime>b.departTime?1:0;
      });
      return list;
    },
  },
  methods: {
    // updateTimeSortedList(){
    //   console.log("updating timeSortedList");
    //   var list = [];
    //   for(stopName in this.boards){
    //     if(this.boards.hasOwnProperty(stopName)){
    //       for(destName in this.boards[stopName].to){
    //         if(this.boards[stopName].to.hasOwnProperty(destName)){
    //           if(this.boards[stopName].to[destName].departures){
    //             list = list.concat(this.boards[stopName].to[destName].departures);
    //           }
    //         }
    //       }
    //     }
    //   }
    //   if(!list){
    //     console.log("empty timeSortedList!");
    //     return '';
    //   }
    //   list.sort(function(a, b){
    //     a = new Date(a.date+' '+a.time);
    //     b = new Date(b.date+' '+b.time);
    //     return a<b?-1:a>b?1:0;
    //   });
    //   // var i = list.length;
    //   // while(i--){
    //   //   var diff = (new Date(list[i].date+' '+list[i].time) - this.now) /1000 / 60;
    //   //   list[i].blink = diff < 4;
    //   //   if(diff< 0){
    //   //     list.splice(i, 1);
    //   //   }
    //   // }
    //   this.timeSortedList = list;
    // },
    getLocationObject(name, successCallback, param2){
      this.$http.get('https://api.vasttrafik.se/bin/rest.exe/v2/location.name', 
        null,
        {
          params: {
            format: 'json',
            input: name,
            param2: param2
          },
          headers: {
            Authorization: this.accessToken,
          }
        }).then(
          function(response){
            successCallback(response);
          }, 
          // function(response){
          //   this.$set('boards.'+name+'.stop', response.data.LocationList.StopLocation[0]);
          // },
          function(response){
            // this.$set('boards.'+name+'.stop', 'dålig response när jag försökte hämta hållplats'+name);
            console.log("Faaaaan! Fick inte stoppet.");
          }
        );
      // setTimeout(this.updateLocation, 10000);
    },
    setLocationData(){
      for(stopName in this.boards){
        //Exclude prototype properties!
        if (this.boards.hasOwnProperty(stopName)) {
          // console.log(stopName + " -> " + this.boards[stopName]);
          this.getLocationObject(stopName,
              function (response){
                //we use the reference to the stop name in the request inside the response since the stopName variable might already be altered when this callback gets fired
                this.boards[response.request.params.input].stop = response.data.LocationList.StopLocation[0];
                console.log("ajax stop request for " + response.request.params.input + " returned:");
                console.log(response.data);

                for(destName in this.boards[response.request.params.input].to){
                  //exclude ptototype properties
                  if (this.boards[response.request.params.input].to.hasOwnProperty(destName)) {
                    this.getLocationObject(destName, 
                      function (response){
                        //we use the reference to the stop names in the request inside the response since the stopName and destName variables might be overwritten when this callback gets fired
                        var stopObj = this.boards[response.request.params.param2];
                        var destObj = stopObj.to[response.request.params.input];
                        destObj.stop = response.data.LocationList.StopLocation[0];
                        console.log("ajax to request for " + response.request.params.input + " returned:");
                        console.log(response.data);
                        this.getDepartureBoard(stopObj, response.request.params.param2, destObj, response.request.params.input);
                      }.bind(this),
                      response.request.params.input);
                  }
                }
              }.bind(this)
              // ,function(response){
              //   console.log("ajax stop request failed: ");
              //   console.log(response.data);
              //   // this.boards[response.request.params.input].stop = "fuuuuck. Didn't get the stop!";
              // }
            );

          // for(destName in this.boards[stopName].to){
          //   //exclude ptototype properties
          //   if (this.boards[stopName].to.hasOwnProperty(destName)) {
          //     this.$http.get('https://api.vasttrafik.se/bin/rest.exe/v2/location.name', 
          //       null,
          //       {
          //         params: {
          //           format: 'json',
          //           input: destName,
          //           parent: stopName,
          //         },
          //         headers: {
          //           Authorization: this.accessToken,
          //         }
          //       }).then(
          //         function(response){
          //           //we use the reference to the stop names in the request inside the response since the stopName and destName variables might be overwritten when this callback gets fired
          //           var stopObj = this.boards[response.request.params.parent];
          //           var destObj = stopObj.to[response.request.params.input];
          //           destObj.stop = response.data.LocationList.StopLocation[0];
          //           console.log("ajax to request for " + response.request.params.input + " returned:");
          //           console.log(response.data);
          //           // this.getDepartureBoard(stopObj, response.request.params.parent, destObj, response.request.params.input);
          //         },
          //         function(response){
          //           console.log("ajax to request failed: ");
          //           console.log(response.data);
          //           // this.boards[response.request.params.parent].to[response.request.params.input].stop = "fuuuuck";
          //         }
          //       );
          //   }
          // }
        }
      }
      // setTimeout(this.setLocationData, 4000);
      // setTimeout(this.updateLocation, 10000);
    },
    getDepartureBoard(fromObj, fromName, toObj, toName){
      // console.log(locationObject);
      // this.setNeighbourLocations(locationObject);
      // console.log(this.locationObjects.lindholmen.neighbours);
      
      // var date = new Date();

      this.$http.get('https://api.vasttrafik.se/bin/rest.exe/v2/departureBoard', 
        null,
        {
          params: {
            format: 'json',
            id: fromObj.stop.id,
            date: moment().format('YYYY-MM-DD'),
            time: moment().format('HH.mm'),
            timeSpan: 360,
            maxDeparturesPerLine: 3,
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
            this.boards[response.request.params.from].to[response.request.params.to].departures = response.data.DepartureBoard.Departure;
          },
          function(response){
            // this.boards[response.request.params.from].to[response.request.params.to].departures = 'dålig response när jag försökte hämta avgångar för hållplats '+fromObj.stop.name;
          }
        );
    },
    getNewKey(){
      this.$http.post('https://api.vasttrafik.se/token',
      {
        grant_type: 'client_credentials',
        scope: 'device_555'
      },
      {
        headers: {
          Authorization: 'Basic TEJaemp3cFM3OTVIdzJKMm1SMWxIcVcxOWFrYTo0QWxRZUZsZjlQTTlGUjA1Q3ZSaVlfNlQzNW9h', //This is our client secret.
        },
        emulateJSON: true,
      }).then(
        function(response){
          this.msg = 'ja! fick svar';
          this.accessToken = response.data.token_type
            + ' ' 
            + response.data.access_token;
          this.response = response;
        },
        function(response){
          this.msg = 'Inget svar!';
          this.response = response.data;
        }
      );
    },
  },
  ready: function(){
    //Set up authorization with västtrafik API
    this.$http.post('https://api.vasttrafik.se/token',
      {
        grant_type: 'client_credentials',
        scope: 'device_555'
      },
      {
        headers: {
          Authorization: 'Basic TEJaemp3cFM3OTVIdzJKMm1SMWxIcVcxOWFrYTo0QWxRZUZsZjlQTTlGUjA1Q3ZSaVlfNlQzNW9h', //This is our client secret.
        },
        emulateJSON: true,
      }).then(
        function(response){
          this.msg = 'ja! fick svar';
          this.accessToken = response.data.token_type
            + ' ' 
            + response.data.access_token;
          this.response = response;

          //Ok. let's fetch our stops according to the structure in the data-object
          this.setLocationData();
          // setInterval(this.updateTimeSortedList, 10000);
          setInterval(this.setLocationData, 30000);
        },
        function(response){
          this.msg = 'Inget svar!';
          this.response = response.data;
        }
      );

    setInterval(this.getNewKey, 3300000);

    setInterval(function(){
      this.now = new Date();
    }.bind(this), 10000);
  }
}
</script>

<style>

.table{
  font-size: 1.5em;
}

.table>tbody>tr>td{
  border-top: 0;
}

.blink-me {
  animation: blinker 0.8s linear infinite;
}

@keyframes blinker {  
  50% { opacity: 0.0; }
}

</style>
