<template>

	<h2>Toaletten är {{toiletFree}} </h2>

</template>

<script>

export default {
  data () {
    return {
    	toiletFree: 'okänd status',
		}
	},
	ready: function(){
		this.$http.get('http://tiigbg.se/robots/esp8266/toilet/status.php').then(
			function(response){
				// console.log(response.data);
				if(response.data==1)
					this.toiletFree = 'ledig';
				else if(response.data == 1)
					this.toiletFree = 'upptagen';
			},
			function(response){

			}
		);
	}
}


</script>

<style>

</style>