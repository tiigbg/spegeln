<template>
  <div id="date-time">
    <h2>{{date}}</h2>
    <h2>{{time}}</h2>
  </div>
</template>

<script>
export default {
  data: function(){
    return{
      date: "no date",
      time: "no time",
    }
  },
  methods:{
    updateDateTime: function(){
      // now = moment();
      this.date = moment().format('dddd, MMMM Do');
      this.time = moment().format('HH:mm');
      setTimeout(this.updateDateTime, 5000);
    },
  },
  ready: function(){
    this.updateDateTime();
  }
}
</script>

<style>
  #date-time {
    /*margin: 50px;*/
  }
  #date-time h2 {
    font-size: 70px;
    font-weight: 300;
  }
</style>