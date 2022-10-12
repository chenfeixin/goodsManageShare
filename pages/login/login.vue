<template>
<!-- 登录 -->
	<view>
		<customHead mode='deNav' title='登录' :isReturn='false'></customHead>
		<!-- <view class="box_title">登录账号</view> -->
		<view class="box" style="margin-top: 80rpx;">
			<view class="box_l">手机号：</view>
			<input type="number" maxlength="11" class="box_input" placeholder="请输入登录手机号" @input="inputPhone" />
		</view>
		<view class="box" style="margin-bottom: 30rpx;">
			<view class="box_l">密码：</view>
			<input type="password" maxlength="6" class="box_input" placeholder="请输入登录密码" @input="inputPassword" />
		</view>
		<view class="box" style="height: 36rpx;">
			<view class="box_set" @click="setPassword">忘记密码</view>
		</view>
		<view class="button" @click="login">登 录</view>
		<!-- #ifdef H5 -->
		<view class="wxLogin" @click="wxLogin">微信登录</view>
		<!-- #endif -->
		<!-- #ifdef APP -->
		<view class="text" @click="register">还没有账号？去注册></view>
		<!-- #endif -->
		<!-- #ifdef MP-WEIXIN -->
		<view class="text" @click="register">还没有账号？去注册></view>
		<!-- #endif -->
	</view>
</template>

<script>
	export default {
		data() {
			return {
				phone: '',
				password: '',
			};
		},
		onLoad() {
			let that = this
			getApp().globalData.login = false
			
			// #ifdef H5
			// let code = that.getQueryVariable('code')
			// uni.request({
			// 	url: getApp().globalData.url + 'api.login/isLong',
			// 	method: 'POST',
			// 	success(res) {
			// 		if(res.data.code!=1){
			// 			if(!code){
			// 				let loc_href = window.location.href;
			// 				// 对当前页面的url进行微信要求的urlEncode 处理 
			// 				loc_href = encodeURIComponent(loc_href);
			// 				let appId = 'wx6857a351a4b284cb';
			// 				// let appId = 'wx9adeae32306c225d';
			// 				let wxUrl = `https://open.weixin.qq.com/connect/oauth2/authorize?appid=${appId}&redirect\_uri=${loc_href}&response_type=code&scope=snsapi_base&state=STATE#wechat_redirect`;
							
			// 				location.href = wxUrl;
			// 				// window.location.href = 'https://open.weixin.qq.com/connect/oauth2/authorize?appid=&redirect_uri=https://fx.mouthwash.top/h5/index.html&response_type=code&scope=snsapi_base&state=123#wechat_redirect'
			// 			}
			// 		}
			// 	}
			// })
			// #endif
		},
		methods: {
			// 输入手机号
			inputPhone(e){
				this.phone = e.detail.value
			},
			
			// 输入 密码
			inputPassword(e){
				this.password = e.detail.value
			},
			
			// 登录
			login(){
				let that = this
				
				if(!that.phone){
					uni.showToast({
						title: '请确认登录账号',
						icon: 'none'
					})
					return false
				}
				
				if(!that.password){
					uni.showToast({
						title: '请确认登录密码',
						icon: 'none'
					})
					return false
				}
				uni.request({
					url: getApp().globalData.url + 'api.login/login',
					method: 'POST',
					data: {
						account: that.phone,
						password: that.password
					},
					complete(res) {
						console.log(res)
					},
					success(res) {
						console.log(that.password,res)
						if(res.data.code==1){
							uni.setStorageSync('user',res.data.data.user)
							uni.setStorageSync('token',res.data.data.token)
							uni.setStorageSync('phone',res.data.data.user.mobile)
							// console.log(uni.getStorageSync('user'))
							// #ifdef APP
							uni.switchTab({
								url: '/pages/index/index'
							})
							// #endif
							
							// #ifdef MP-WEIXIN
							uni.switchTab({
								url: '/pages/index/index'
							})
							// #endif
							
							// #ifdef H5
							// window.location.href = 'https://gch.gargle.top/h5/index.html#/pages/index/index'
							window.location.href = 'https://fx.mouthwash.top/h5/index.html#/pages/index/index'
							// #endif
						}else{
							uni.showToast({
								title: res.data.msg,
								icon: 'none'
							})
						}
					}
					
				})
			},
			
			// 忘记密码
			setPassword(){
				uni.navigateTo({
					url: '/pages/my/setPassword?title=登录密码&type=1'
				})
			},
			
			// 微信登录
			wxLogin(){
				let that = this
				
				let code = that.getQueryVariable('code')
				if(code){
					uni.request({
						url: getApp().globalData.url + 'api.login/wxh5',
						method: 'POST',
						data: {
							code: code,
							cc: uni.getStorageSync('cc')
						},
						success(res) {
							if(res.data.code==1){
								uni.setStorageSync('user',res.data.data.user)
								uni.setStorageSync('phone',res.data.data.user.mobile)
								uni.switchTab({
									url: '/pages/index/index'
								})
							}
						}
					})
				}
			},
			
			// 获取url参数
			getQueryVariable(variable) {
			      var query = window.location.search.substring(1);
			      var vars = query.split("&");
			      for (var i = 0; i < vars.length; i++) {
			        var pair = vars[i].split("=");
			        if (pair[0] == variable) { return pair[1]; }
			      }
				  // console.log(code)
			      return (false);
			},
			
			// 注册
			register(){
				uni.redirectTo({
					url: '/pages/register/register'
				})
			}
		}
	}
</script>

<style lang="scss">
	.box_title{
		margin: 100rpx 0rpx;
		height: 48rpx;
		font-size: 34rpx;
		font-family: PingFangSC-Regular, PingFang SC;
		font-weight: 400;
		color: #333333;
		line-height: 48rpx;
		text-align: center;
	}
	.box{
		margin-bottom: 52rpx;
		height: 76rpx;
		overflow: hidden;
		
		&_l{
			float: left;
			margin-top: 18rpx;
			margin-left: 46rpx;
			width: 120rpx;
			height: 40rpx;
			font-size: 28rpx;
			font-family: PingFangSC-Regular, PingFang SC;
			font-weight: 400;
			color: #333333;
			line-height: 40rpx;
		}
		&_input{
			float: left;
			padding-left: 66rpx;
			margin-left: 32rpx;
			width: 446rpx;
			height: 76rpx;
			border-radius: 38rpx;
			border: 2rpx solid #EEC873;
			font-size: 28rpx;
			font-family: PingFangSC-Regular, PingFang SC;
			font-weight: 400;
			color: #000000;
			line-height: 76rpx;
			box-sizing: border-box;
		}
		&_set{
			float: right;
			margin-right: 138rpx;
			height: 36rpx;
			font-size: 24rpx;
			font-family: PingFangSC-Regular, PingFang SC;
			font-weight: 400;
			color: #6236FF;
			line-height: 36rpx;
		}
	}
	.button{
		margin: 100rpx 33rpx 0rpx;
		width: 684rpx;
		height: 92rpx;
		background: linear-gradient(90deg, #F6E1B0 0%, #EDC66E 100%);
		border-radius: 50rpx;
		text-align: center;
		font-size: 28rpx;
		font-family: PingFangSC-Medium, PingFang SC;
		font-weight: 500;
		color: #FFFFFF;
		line-height: 92rpx;
	}
	.wxLogin{
		margin-top: 32rpx;
		height: 40rpx;
		font-size: 28rpx;
		font-family: PingFangSC-Regular, PingFang SC;
		font-weight: 400;
		color: #6236FF;
		line-height: 40rpx;
		text-align: center;
	}
	.text{
		margin-top: 60rpx;
		height: 40rpx;
		font-size: 28rpx;
		font-family: PingFangSC-Regular, PingFang SC;
		font-weight: 400;
		color: #6236FF;
		line-height: 40rpx;
		text-align: center;
		text-decoration: underline;
	}
</style>
