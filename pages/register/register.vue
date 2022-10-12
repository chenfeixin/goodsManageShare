<template>
	<view>
		<image src="/static/hzg_logo.png" class="logo"></image>
		<view class="title">欢迎注册好掌柜共享科技</view>
		<view class="box">
			<view class="box_l">账户昵称：</view>
			<input type="text" maxlength="12" placeholder="请输入您的昵称" class="box_input" @input="inputNickName">
		</view>
		<view class="box">
			<view class="box_l">登录密码：</view>
			<input type="password" maxlength="6" placeholder="请输入6位数字登录密码" class="box_input" @input="inputLogin">
		</view>
		<view class="box">
			<view class="box_l">支付密码：</view>
			<input type="password" maxlength="6" placeholder="请输入6位数字支付密码" class="box_input" @input="inputPay">
		</view>
		<view class="box">
			<view class="box_l">手机号码：</view>
			<!-- <input type="number" maxlength="11" class="box_input"> -->
			<view class="box_r">
				<input type="number" maxlength="11" placeholder="请输入您的手机号" class="box_r_input" @input="inputPhone">
				<view class="box_r_r" @click="clickSend" v-if="mobile.length==11">{{timeText}}</view>
				<view class="box_r_r" @click="sendHint">{{timeText}}</view>
			</view>	
		</view>
		<view class="box">
			<view class="box_l">验证码：</view>
			<input type="number" maxlength="8" class="box_input" placeholder="请输入验证码" @input="inputVerification">
		</view>
		<!-- #ifdef APP -->
		<view class="box">
			<view class="box_l">邀请码：</view>
			<input type="text" class="box_input" placeholder="请输入邀请码" @input="inputInvite">
		</view>
		<!-- #endif -->
		
		<!-- #ifdef MP-WEIXIN -->
		<view class="box">
			<view class="box_l">邀请码：</view>
			<input type="text" class="box_input" placeholder="请输入邀请码" disabled :value="cc" v-if="noCc">
			<input type="text" class="box_input" placeholder="请输入邀请码" :value="cc" @input="inputInvite" v-else>
		</view>
		<!-- #endif -->
		
		<!-- #ifdef H5 -->
		<view class="box">
			<view class="box_l">邀请码：</view>
			<input type="text" disabled class="box_input" :value="cc">
		</view>
		<!-- #endif -->
		
		<view class="button" @click="register">注册</view>
		<!-- #ifdef H5 -->
		<view class="download" @click="download">已有账号，点击下载App</view>
		<!-- #endif -->
		<!-- #ifdef APP -->
		<view class="download" @click="login">已有账号，去登录</view>
		<!-- #endif -->
		<!-- #ifdef MP-WEIXIN -->
		<view class="download" @click="login">已有账号，去登录</view>
		<!-- #endif -->
	</view>
</template>

<script>
	export default {
		data() {
			return {
				nickName: '', //用户名
				mobile: '', //手机号码
				timeText: '获取验证码',
				loginPassword: '', //登录密码
				payPassword: '', //支付密码
				verification: '', //验证码
				showSend: true, //是否可以发送
				time: 60,
				cc: '', //邀请码
				noCc: false, //是否可以填写邀请码
			};
		},
		onLoad(options) {
			let that = this
			
			// console.log(options)
			if(options.cc){
				that.cc = options.cc
				that.noCc = true
			}
		},
		methods: {
			//输入昵称
			inputNickName(e){
				this.nickName = e.detail.value
			},
			// 输入手机号
			inputPhone(e){
				this.mobile = e.detail.value
			},
			// 发送验证码
			clickSend(){
				let that = this
				
				if(that.showSend){
					uni.request({
						url: getApp().globalData.url + 'api.login/sendAliDaYuAuthCode',
						method: 'POST',
						data: {
							phone: that.mobile
						},
						success(res) {
							if(res.data.code==1){
								that.showSend = false
								let time = that.time
								let countDown = setInterval(()=>{
									if(time>1){
										time = time - 1
										that.time = time
										that.timeText = '重新发送('+time+')'
									}else{
										// 结束倒计时，可以重新发送验证码
										that.showSend = true
										that.timeText = '发送验证码'
										that.time = 60
										clearInterval(countDown)
									}
								},1000)
							}else{
								console.log(res)
								uni.showToast({
									title: res.data.msg,
									icon: 'none'
								})
							}
						}
					})
					// that.showSend = false
					// let time = that.time
					// let countDown = setInterval(()=>{
					// 	if(time>1){
					// 		time = time - 1
					// 		that.time = time
					// 		that.timeText = '重新发送('+time+')'
					// 	}else{
					// 		// 结束倒计时，可以重新发送验证码
					// 		that.showSend = true
					// 		that.timeText = '获取验证码'
					// 		that.time = 60
					// 		clearInterval(countDown)
					// 	}
					// },1000)
				}
			},
			// 发送验证码提示
			sendHint(){
				uni.showToast({
					title: '请输入手机号',
					icon: 'none'
				})
			},
			// 输入验证码
			inputVerification(e){
				let that = this
				
				that.verification = e.detail.value
			},
			// 输入登录密码
			inputLogin(e){
				let that = this
				that.loginPassword = e.detail.value
			},
			// 输入支付密码
			inputPay(e){
				let that = this
				that.payPassword = e.detail.value
			},
			// 输入邀请码
			inputInvite(e){
				let that = this
				
				that.cc = e.detail.value
			},
			// 注册
			register(){
				let that = this
				if(that.nickName){
					if(that.loginPassword.length==6){
						if(that.payPassword.length==6){
							if(that.mobile.length==11){
								if(that.verification){
									if(that.cc){
										uni.request({
											url: getApp().globalData.url + 'api.login/register',
											method: 'POST',
											data: {
												password: that.loginPassword,
												payment: that.payPassword,
												mobile: that.mobile,
												code: that.verification,
												cc: that.cc,
												nickname: that.nickName
											},
											success(res) {
												console.log(res)
												if(res.data.code==1){
													uni.setStorageSync('user',res.data.data.user)
													uni.setStorageSync('phone',res.data.data.user.mobile)
													uni.setStorageSync('token',res.data.data.token)
													// #ifdef APP
													uni.showToast({
														title: '注册成功!',
														success() {
															setTimeout(()=>{
																uni.switchTab({
																	url: '/pages/index/index'
																})
															},1500)
														}
													})
													// #endif
													
													// #ifdef MP-WEIXIN
													uni.showToast({
														title: '注册成功!',
														success() {
															setTimeout(()=>{
																uni.switchTab({
																	url: '/pages/index/index'
																})
															},1500)
														}
													})
													// #endif
													
													// #ifdef H5
													uni.showModal({
														title: '注册成功！',
														content: '是否前往下载APP？',
														success: function (res) {
															if (res.confirm) {
																that.download()
															} else if (res.cancel) {
																console.log('用户点击取消');
															}
														}
													});
													// #endif
												}else{
													console.log(res)
													uni.showToast({
														title: res.data.msg,
														icon: 'none'
													})
												}
											}
										})
									}else{
										uni.showToast({
											title: '请输入邀请码',
											icon: 'none'
										})
									}
								}else{
									uni.showToast({
										title: '请输入验证码',
										icon: 'none'
									})
								}
							}else{
								uni.showToast({
									title: '请输入正确的手机号',
									icon: 'none'
								})
							}
						}else{
							uni.showToast({
								title: '请确认支付密码',
								icon: 'none'
							})
						}
					}else{
						uni.showToast({
							title: '请确认登录密码',
							icon:'none'
						})
					}
				}else{
					uni.showToast({
						title: '请输入昵称',
						icon:'none'
					})
				}
				// console.log('loginPassword:'+that.loginPassword + 'payPassword:'+that.payPassword+'mobile:'+that.mobile+'verification:'+that.verification)
			},
			
			// 下载
			download(){
				uni.getSystemInfo({
					success(res) {
						if(res.osName== 'android'){
							location.href = 'https://www.pgyer.com/Khk7'
						}else if(res.osName== 'ios'){
							location.href = 'https://apps.apple.com/cn/app/id1635920146?mt=8'
						}
					}
				})
			},
			
			// 去登录
			login(){
				uni.redirectTo({
					url: '/pages/login/login'
				})
			},
		}
	}
</script>

<style lang="scss">
	.logo{
		margin-left: 50%;
		transform: translate(-50%);
		width: 200rpx;
		height: 200rpx;
	}
	.title{
		margin-bottom: 60rpx;
		height: 48rpx;
		font-size: 34rpx;
		font-family: PingFangSC-Regular, PingFang SC;
		font-weight: 400;
		color: #333333;
		line-height: 48rpx;
		text-align: center;
	}
	.box{
		margin: 32rpx 52rpx 0rpx 40rpx;
		overflow: hidden;
		
		&_l{
			float: left;
			width: 140rpx;
			height: 76rpx;
			font-size: 26rpx;
			font-family: PingFangSC-Regular, PingFang SC;
			font-weight: 400;
			color: #333333;
			line-height: 76rpx;
		}
		&_input{
			float: right;
			padding-left: 40rpx;
			width: 500rpx;
			height: 76rpx;
			border-radius: 38rpx;
			border: 2rpx solid #EEC873;
			box-sizing: border-box;
			font-size: 28rpx;
		}
		&_r{
			float: right;
			padding-left: 40rpx;
			width: 500rpx;
			height: 76rpx;
			border-radius: 38rpx;
			border: 2rpx solid #EEC873;
			box-sizing: border-box;
			
			&_input{
				float: left;
				width: 250rpx;
				height: 72rpx;
				font-size: 26rpx;
			}
			&_r{
				float: right;
				margin-top: 18rpx;
				width: 180rpx;
				height: 40rpx;
				font-size: 26rpx;
				font-family: PingFangSC-Regular, PingFang SC;
				font-weight: 400;
				color: #CBA55F;
				line-height: 40rpx;
				text-align: center;
				border-left: 2rpx solid #979797;
			}
		}
	}
	.button{
		margin: 80rpx 33rpx 0rpx;
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
	.download{
		margin-top: 40rpx;
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
