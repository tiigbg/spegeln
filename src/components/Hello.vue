<template>
  <div class="hello">
    <h1 v-on:click="updateLocation">{{ msg }}</h1>
    <h2>{{accessToken}}</h2>
    <pre>{{locationData | json}}</pre>
    <!-- <pre>{{response | json}}</pre> -->
  </div>
</template>

<script>
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
            input: 'järntorget',
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
        )
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
        },
        function(response){
          this.msg = 'Inget svar!';
          this.response = response.data;
        }
      );
  }
}
</script>
