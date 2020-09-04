<template>
	<view>
		write...
	</view>
</template>

<script>
	var iflogin;
	export default {
		data() {
			return {
	
			}
		},
		onLoad() {
			iflogin=this.checklogin('../write/write',2);
			if(!iflogin){
				return  false;
			}
		},
		methods: {
	
		}
	}
</script>

<style>
</style>
