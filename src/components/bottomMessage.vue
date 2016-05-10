<template>
    <h2 id="message">{{bottomMessage}}</h2>
</template>

<script>
// var $ = require('jquery');
export default {
  data () {
    return {
      bottomMessage: 'So static message. Much not dynamic. Such WOW!',
    }
  },
  methods: {
    getMessage(){
      this.$http.get('http://www.tiigbg.se/api/getmirrormessage.php').then(
        function(response){
          this.bottomMessage = response.data;
        },
        function(response){
          // this.bottomMessage = 'So static message. Much not dynamic. Such WOW!';
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

#message {
  font-size: 3em;
  font-family: 'Covered By Your Grace', cursive;
  word-wrap: break-word;
  /*margin-bottom: 10px;*/
  /*position: absolute;*/
  /*bottom: 10px;*/
}


</style>
