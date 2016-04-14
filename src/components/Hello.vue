<template>
  <div class="hello">
    <div id="weather"></div>
    <h1 v-on:click="updateLocation">{{ msg }}</h1>
    <h2>{{accessToken}}</h2>
    <pre>{{locationData | json}}</pre>
    <!-- <pre>{{response | json}}</pre> -->
  </div>
</template>

<script>
// var $ = require('jquery');
export default {
  data () {
    return {
      // Note: modifying `msg` below will not cause changes to occur with
      // hot-reload. As reloaded components preserve their initial state,
      // modifying these values will have no effect.
      msg: 'Hellooo World!',
      accessToken: '',
      locationData: 'bläää',
      response: ''
    }
  },
  methods: {
    updateLocation(){
      this.$http.get('https://api.vasttrafik.se/bin/rest.exe/v2/location.name', 
        null,
        {
          params: {
            format: 'json',
            input: 'lindholmen',
          },
          headers: {
            Authorization: this.accessToken,
          }
        }).then(
          function(response){
            this.locationData = response.data;
          },
          function(response){
            this.msg = 'dålig response när jag försökte använda token';
          }
        );
      setTimeout(this.updateLocation, 10000);
    },
    getWeather(){
      $(document).ready(function() {
        $.simpleWeather({
          location: 'Gothenburg',
          woeid: '',
          unit: 'c',
          success: function(weather) {
            html = '<h2><i class="icon-'+weather.code+'"></i> '+weather.temp+'&deg;'+weather.units.temp+'</h2>';
            //html += '<ul><li>'+weather.city+', '+weather.region+'</li>';
            html += '<li class="currently">'+weather.currently+'</li>';
            html += '<li>'+weather.wind.direction+' '+weather.wind.speed+' '+weather.units.speed+'</li></ul>';
        
            $("#weather").html(html);
          },
          error: function(error) {
            $("#weather").html('<p>'+error+'</p>');
          }
        });
      });
    }
  },
  ready: function(){
    this.getWeather();
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

          this.updateLocation();
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


.icon-0:before { content: ":"; }
.icon-1:before { content: "p"; }
.icon-2:before { content: "S"; }
.icon-3:before { content: "Q"; }
.icon-4:before { content: "S"; }
.icon-5:before { content: "W"; }
.icon-6:before { content: "W"; }
.icon-7:before { content: "W"; }
.icon-8:before { content: "W"; }
.icon-9:before { content: "I"; }
.icon-10:before { content: "W"; }
.icon-11:before { content: "I"; }
.icon-12:before { content: "I"; }
.icon-13:before { content: "I"; }
.icon-14:before { content: "I"; }
.icon-15:before { content: "W"; }
.icon-16:before { content: "I"; }
.icon-17:before { content: "W"; }
.icon-18:before { content: "U"; }
.icon-19:before { content: "Z"; }
.icon-20:before { content: "Z"; }
.icon-21:before { content: "Z"; }
.icon-22:before { content: "Z"; }
.icon-23:before { content: "Z"; }
.icon-24:before { content: "E"; }
.icon-25:before { content: "E"; }
.icon-26:before { content: "3"; }
.icon-27:before { content: "a"; }
.icon-28:before { content: "A"; }
.icon-29:before { content: "a"; }
.icon-30:before { content: "A"; }
.icon-31:before { content: "6"; }
.icon-32:before { content: "1"; }
.icon-33:before { content: "6"; }
.icon-34:before { content: "1"; }
.icon-35:before { content: "W"; }
.icon-36:before { content: "1"; }
.icon-37:before { content: "S"; }
.icon-38:before { content: "S"; }
.icon-39:before { content: "S"; }
.icon-40:before { content: "M"; }
.icon-41:before { content: "W"; }
.icon-42:before { content: "I"; }
.icon-43:before { content: "W"; }
.icon-44:before { content: "a"; }
.icon-45:before { content: "S"; }
.icon-46:before { content: "U"; }
.icon-47:before { content: "S"; }

#weather {
  width: 500px;
  margin: 0px auto;
  text-align: center;
  text-transform: uppercase;
}

#weather h2 {
  margin: 0 0 8px;
  /*color: #fff;*/
  font-size: 100px;
  font-weight: 300;
  text-align: center;
  text-shadow: 0px 1px 3px rgba(0, 0, 0, 0.15);
}

#weather ul {
  margin: 0;
  padding: 0;
}

#weather li {
  background: #fff;
  background: rgba(255,255,255,0.90);
  padding: 20px;
  display: inline-block;
  border-radius: 5px;
}

#weather .currently {
  margin: 0 20px;
}

</style>
