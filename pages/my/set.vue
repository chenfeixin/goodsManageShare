<template>
<!-- 设置 -->
	<view>
		<view class="boxes" @click="selectAvatar">
			<view class="boxes_l">修改头像：</view>
			<view class="boxes_imgr">></view>
			<image :src="avatar" class="boxes_img"></image>
		</view>
		<view class="boxes">
			<view class="boxes_l"><text style="color: red;">*</text>修改昵称：</view>
			<input type="text" placeholder="请输入昵称" class="boxes_r" maxlength="8" :value="name" @input="inputName">
		</view>
		<view class="boxes" v-if="phone">
			<view class="boxes_l">当前绑定手机号：</view>
			<view class="boxes_r">{{phone}}</view>
		</view>
		<view class="boxes" @click="setPhone" v-else>
			<view class="boxes_l"><text style="color: red;">*</text>绑定手机号：</view>
			<view class="boxes_r">设置></view>
		</view>
		<!-- <view class="boxes">
			<input type="number" placeholder="请输入验证码" class="boxes_code_l" maxlength="10" @input="inputVerification">
			<view class="boxes_code_r" @click="clickSend">{{timeText}}</view>
		</view> -->
		<view class="boxes" @click="setPassword('登录密码')">
			<view class="boxes_l"><text style="color: red;">*</text>修改登录密码</view>
			<view class="boxes_r">设置></view>
		</view>
		<view class="boxes" @click="setPassword('设置平台支付密码')">
			<view class="boxes_l"><text style="color: red;">*</text>修改平台支付密码</view>
			<view class="boxes_r">设置></view>
		</view>
		<view class="boxes" @click="call">
			<view class="boxes_l">客服热线（4001360376）</view>
			<view class="boxes_r">呼叫</view>
		</view>
		<view class="boxes" @click="logout">
			<view class="boxes_l">退出登录</view>
			<view class="boxes_r">退出</view>
		</view>
		<!-- #ifdef APP -->
		<view class="boxes" @click="userdel">
			<view class="boxes_l"></view>
			<view class="boxes_r">注销账号</view>
		</view>
		<!-- #endif -->
		<view class="button" @click="confirm">保存</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				name: '',
				avatar: '',
				phone: uni.getStorageSync('phone'),
				timeText: '发送验证码',
				verification: '',//验证码
				showSend: true, //是否可以发送
				time: 60,
			};
		},
		onLoad(options) {
			let that = this
			
			if(options.name){
				that.name = options.name
				that.avatar = options.avatar
			}
		},
		methods: {
			// 拨打电话
			call(){
				uni.makePhoneCall({
					phoneNumber: '4001360376'
				})
			},
			// 选择头像
			selectAvatar(){
				let that = this
				uni.chooseImage({
				    success: (chooseImageRes) => {
				        const tempFilePaths = chooseImageRes.tempFilePaths;
						console.log(tempFilePaths)
				        uni.uploadFile({
							url: 'https://www.gargle.top/api/common/upload',
				            // url: 'https://fx.mouthwash.top/api/common/upload',
				            filePath: tempFilePaths[0],
				            name: 'file',
							header: {
								token: uni.getStorageSync('token')
							},
				            formData: {
				                'user': 'test',
				            },
				            success: (uploadFileRes) => {
								console.log(uploadFileRes)
								let avatar = JSON.parse(uploadFileRes.data)
								that.avatar = avatar.data.fullurl
				            }
				        });
				    }
				})
			},
			
			// 修改手机号
			setPhone(){
				uni.navigateTo({
					url: '/pages/my/setPhone'
				})
			},
			
			// 姓名输入
			inputName(e){
				this.name = e.detail.value
			},
			
			// 电话输入
			inputPhone(e){
				this.phone = e.detail.value
			},
			
			// 发送验证码
			clickSend(){
				let that = this
				
				if(that.showSend){
					uni.request({
						url: getApp().globalData.url + 'api.user/sendAliDaYuAuthCode',
						method: 'POST',
						header: {
							token: uni.getStorageSync('token')
						},
						data: {
							phone: that.phone
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
								uni.showToast({
									title: res.data.msg,
									icon: 'none'
								})
							}
						}
					})
				}
			},
			// 输入验证码
			inputVerification(e){
				let that = this
				
				that.verification = e.detail.value
			},
			
			// 设置密码
			setPassword(title){
				if(uni.getStorageSync('phone')){
					uni.navigateTo({
						url: '/pages/my/setPassword?title='+title
					})
				}else{
					uni.showToast({
						title: '请先保存手机号',
						icon: 'none'
					})
				}
			},
			
			// 保存
			confirm(){
				let that = this
				
				if(that.name){
					// if(that.phone){
						uni.request({
							url: getApp().globalData.url + 'api.user/profile',
							method: 'POST',
							header: {
								token: uni.getStorageSync('token')
							},
							data: {
								nickname: that.name,
								avatar: that.avatar,
								// tel: that.phone,
								// code: that.verification
							},
							success(res) {
								if(res.data.code==1){
									uni.setStorageSync('phone',that.phone)
									uni.showToast({
										title: res.data.msg,
										success() {
											setTimeout(()=>{
												uni.switchTab({
													url: '/pages/my/my'
												})
											},1500)
										}
									})
								}else{
									uni.showToast({
										title: res.data.msg,
										icon: 'none'
									})
								}
							}
						})
					// }else{
					// 	uni.showToast({
					// 		title: '用户名必填',
					// 		icon: 'none'
					// 	})
					// }
				}else{
					uni.showToast({
						title: '用户名必填',
						icon: 'none'
					})
				}
			},
			
			// 退出登录
			logout(){
				uni.showModal({
					title: '提示',
					content: '您确定退出登录吗？',
					success: function (res) {
						if (res.confirm) {
							uni.request({
								url: getApp().globalData.url + 'api.user/logout',
								method: 'POST',
								header: {
									token: uni.getStorageSync('token')
								},
								success(res) {
									if(res.data.code==1){
										uni.showToast({
											title: '请重新登录',
											icon: 'none',
											success() {
												uni.removeStorageSync('user')
												setTimeout(()=>{
													uni.reLaunch({
														url: '/pages/login/login',
														success() {
															// #ifdef H5
															window.location.href = 'https://fx.mouthwash.top/h5/index.html#/pages/login/login'
															// window.location.href = 'https://gch.gargle.top/h5/index.html#/pages/login/login'
															// #endif
														}
													})
												},1500)
											}
										})
									}else{
										uni.showToast({
											title: res.data.msg,
											icon: 'none'
										})
									}
								}
							})
						} else if (res.cancel) {
							console.log('用户点击取消');
						}
					}
				});
			},
			
			// 注销账号
			userdel(){
				let that = this
				
				uni.showModal({
					title: '提示',
					content: '您确定要注销账号吗？注销后所有信息不可恢复',
					success: function (res) {
						if (res.confirm) {
							uni.request({
								url: getApp().globalData.url + 'api.user/userdel',
								method: 'POST',
								header: {
									token: uni.getStorageSync('token')
								},
								success(res) {
									if(res.data.code==1){
										uni.showToast({
											title: '请重新登录',
											icon: 'none',
											success() {
												uni.removeStorageSync('user')
												setTimeout(()=>{
													uni.reLaunch({
														url: '/pages/login/login'
													})
												},1500)
											}
										})
									}else{
										uni.showToast({
											title: res.data.msg,
											icon: 'none'
										})
									}
								}
							})
						} else if (res.cancel) {
							console.log('用户点击取消');
						}
					}
				});
			},
		}
	}
</script>

<style lang="scss">
	page{
		background: #F4F4F4;
	}
	.boxes{
		margin: 0rpx 47rpx;
		height: 104rpx;
		border-bottom: 2rpx solid #EEC873;
		overflow: hidden;
		
		&_l{
			float: left;
			margin-top: 50rpx;
			width: 50%;
			height: 40rpx;
			font-size: 28rpx;
			font-family: PingFangSC-Medium, PingFang SC;
			font-weight: 500;
			color: #333333;
			line-height: 40rpx;
		}
		&_imgr{
			float: right;
			margin-top: 50rpx;
			margin-left: 16rpx;
			height: 40rpx;
			font-size: 28rpx;
			font-family: PingFangSC-Medium, PingFang SC;
			font-weight: 500;
			color: #333333;
			line-height: 40rpx;
			text-align: right;
			// background: yellow;
		}
		&_img{
			float: right;
			margin-top: 50rpx;
			width: 40rpx;
			height: 40rpx;
			border-radius: 4rpx;
		}
		&_r{
			float: right;
			margin-top: 50rpx;
			width: 50%;
			height: 40rpx;
			font-size: 28rpx;
			font-family: PingFangSC-Medium, PingFang SC;
			font-weight: 500;
			color: #333333;
			line-height: 40rpx;
			text-align: right;
			// background: yellow;
		}
		&_code_l{
			float: left;
			margin-top: 40rpx;
			width: 50%;
			height: 40rpx;
			font-size: 28rpx;
			font-family: PingFangSC-Medium, PingFang SC;
			font-weight: 500;
			color: #333333;
			line-height: 40rpx;
		}
		&_code_r{
			float: right;
			margin-top: 22rpx;
			width: 208rpx;
			height: 60rpx;
			border-radius: 38rpx;
			border: 2rpx solid #999999;
			text-align: center;
			font-size: 28rpx;
			font-family: PingFangSC-Regular, PingFang SC;
			font-weight: 400;
			color: #FF4553;
			line-height: 60rpx;
		}
	}
	.button{
		margin: 100rpx 24rpx;
		width: 662rpx;
		height: 80rpx;
		background: linear-gradient(90deg, #F6E1B0 0%, #EDC66E 100%);
		border-radius: 40rpx;
		text-align: center;
		font-size: 28rpx;
		font-family: PingFangSC-Medium, PingFang SC;
		font-weight: 500;
		color: #FFFFFF;
		line-height: 80rpx;
	}
</style>
