<template>
	<view class="contain">
		<view class="wx">
			<!-- #ifdef MP-WEIXIN -->
			<button type="primary" open-type="getUserInfo" @getuserinfo="getUserInfo">使用微信登录</button>
			<!-- #endif -->
		</view>
	</view>
</template>

<script>
	var signfunction=require('../../static/plug_doc/sign.js')
	var that, pageOptions, session_key, openid;
	export default {

		data() {
			return {

			}
		},
		methods: {
			//#ifdef MP-WEIXIN
			getUserInfo(info) {
				
				info = info.mp.detail.userInfo;
				var sign = uni.getStorageSync('sign');
				//将数据提交到服务器进行登录
				uni.request({
					url: that.apiServer + 'member&m=login',
					method: "POST",
					header: {
						'content-type': "application/x-www-form-urlencoded"
					},
					sslVerify: false,
					data: {
						openid: openid,
						name: info.nickName,
						face: info.avatarUrl,
						sign   : sign
					},
					success: function(loginbackinfo) {
						//登录成功记录登录的信息

						console.log(loginbackinfo);
						if (loginbackinfo.data.status == 'ok') {
							uni.showToast({
								title: "登录成功"
							});
							uni.setStorageSync('SUID', loginbackinfo.data.data.u_id + '');
							uni.setStorageSync('SRAND', loginbackinfo.data.data.u_random + '');
							uni.setStorageSync('SNAME', loginbackinfo.data.data.u_name + '');
							uni.setStorageSync('SFACE', loginbackinfo.data.data.u_face + '');
							// 跳转
							if (pageOptions.backtype == 1) {
								uni.redirectTo({
									url: pageOptions.backpage
								});
							} else {
								uni.switchTab({
									url: pageOptions.backpage
								});
							}
						}
					},
					fail: function(e) {
						console.log(JSON.stringify(e));
					},
					complete: function() {

					}
				})
			}
			//#endif
		},
		onLoad(options) {
			pageOptions = options;
			signfunction.sign(this.apiServer);
			that = this;
			//#ifdef APP-PLUS		
			uni.login({
				provider: 'weixin',
				success: function(ress) {
					uni.getUserInfo({
						success: function(info) {
							var sign = uni.getStorageSync('sign');
							uni.request({
								url: that.apiServer + 'member&m=login',
								method: "POST",
								header: {
									'content-type': "application/x-www-form-urlencoded"
								},
								sslVerify: false,
								data: {
									openid: info.userInfo.openId,
									name: info.userInfo.nickName,
									face: info.userInfo.avatarUrl,
									sign:sign
								},
								success: function(res) {
									res = res.data;
									if (res.status == 'ok') {
										uni.showToast({
											title: "登录成功"
										});
										
										uni.setStorageSync('SUID', res.data.u_id + '');
										uni.setStorageSync('SRAND', res.data.u_random + '');
										uni.setStorageSync('SNAME', res.data.u_name + '');
										uni.setStorageSync('SFACE', res.data.u_face + '');
										// 跳转
										if (options.backtype == 1) {
											uni.redirectTo({
												url: options.backpage
											});
										} else {
											uni.switchTab({
												url: options.backpage
											});
										}
									}

								},
								fail: function(error) {
									console.log(JSON.stringify(error));
								},
								complete: function() {
									uni.showToast({
										title: '微信登录授权失败',
										icon: "none"
									})
								}
							})
						},
						fail: function() {
							uni.showToast({
								title: '微信登录授权失败',
								icon: "none"
							})
						}
					})
				},
				fail: function() {
					uni.showToast({
						title: '微信登录授权失败',
						icon: "none"
					})
				} 
			}) 
			//#endif
			//#ifdef MP-WEIXIN
			uni.login({
				success: function(res) {
					uni.request({
						url: that.apiServer + 'member&m=codeToSession&code=' + res.code,
						success: (sessions) => {
							session_key = sessions.data.session_key;
							openid = sessions.data.openid;
						},

					})
				}
			})
			//#endif
		}
	}
</script>

<style>
</style>
