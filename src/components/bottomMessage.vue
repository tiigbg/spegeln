<template>
    <h2>bottomMessage: {{bottomMessage}}</h2>
</template>

<script>
// var $ = require('jquery');
export default {
  data () {
    return {
      bottomMessage: 'Lorem Ipsum',
    }
  },
  methods: {
    getMessage(){
      this.$http.get('http://www.tiigbg.se/api/getmirrormessage.php').then(
        function(response){
          this.bottomMessage = response.data;
        },
        function(response){
          this.bottomMessage = 'Lyckades inte hämta meddelande :-(';
        }
      );
      setTimeout(this.getMessage, 4000);
    }
  },
  ready: function(){
    this.getMessage();
  }
}
</script>

<style>

</style>
