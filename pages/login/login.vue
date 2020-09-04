<template>
	<view class="contain">
		<view class="app">
			
		</view>
		<view class="wx">
			
		</view>
	</view>
</template>

<script>
	var that;
	export default{
		
		data(){
			return {
				
			}
		},
		onLoad(option) {
			//#ifdef APP-PLUS
				that=this; 
				uni.login({ 
					success:function(res){
						uni.getUserInfo({
							success:function(info){
								uni.request({
									url:that.apiServer+'member&m=login',
									method:'POST',
									header: {'content-type' : "application/x-www-form-urlencoded"},
									data:{
										openid:info.userInfo.openId,
										name:info.userInfo.nickName,
										face:info.userInfo.avatarUrl
									},
									success:function(ress){ 
										console.log(ress);
									},
									fail:function(){
										console.log(that.apiServer+'member&m=login')
									},
									complete:function(){ 
										
									}
								})
							},
							fail:function(){
								uni.showToast({
									title:'微信登录授权失败',
									icon:"none"
								})
							}
						})
					},
					fail:function(){
						uni.showToast({
							title:'微信登录授权失败',
							icon:"none"
						}) 
					}
				})
			//#endif
		}
	}
</script>

<style>
</style>
