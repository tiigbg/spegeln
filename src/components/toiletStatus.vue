<template>

	<h2 id="toiletMessage">Toaletten är {{toiletFree}} </h2>

</template>

<script>

export default {
  data () {
    return {
    	toiletFree: 'okänd status',
		}
	},
	methods: {
		getToiletStatus: function(){
			this.$http.get('http://tiigbg.se/robots/esp8266/toilet/status.php').then(
				function(response){
					// console.log('fick svar från toaletten: ');
					// console.log(response);
					if(response.data===1)
						this.toiletFree = 'ledig';
					else
						this.toiletFree = 'upptagen';
				},
				function(response){
					console.log('bad response when requesting toilet: ');
					console.log(response);
				}
			);

			setTimeout(this.getToiletStatus, 4000);
		}
	},
	ready: function(){
		this.getToiletStatus();
	}
}


</script>

<style>

#toiletMessage {
	font-size: 3em;
  font-family: 'Covered By Your Grace', cursive;
}

</style>