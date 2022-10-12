<template>
<!-- 设置密码 -->
	<view>
		<view class="boxes">
			<view class="boxes_title">{{title}}</view>
			<view class="box">
				<view class="box_l">手机号：</view>
				<input type="number" maxlength="11" class="box_phone" placeholder="请输入手机号" :value="mobile" @input="inputPhone" v-if="type==1">
				<input type="number" maxlength="11" class="box_phone" disabled placeholder="请输入手机号" :value="mobile" @input="inputPhone" v-else>
				<view class="box_verification" @click="clickSend">{{timeText}}</view>
			</view>
			<view class="box">
				<view class="box_l">验证码：</view>
				<input type="number" maxlength="11" class="box_phone" placeholder="请输入验证码" :value="verification" @input="inputVerification">
			</view>
			<view class="box">
				<view class="box_l">{{title=='登录密码'?'登录密码：':'支付提现密码：'}}</view>
				<view class="box_r">
					<view class="box_r_item" v-for="(item,index) in 6" :key="index">
						<text class="box_r_item_text" v-if="password.length==index&&showPassword">|</text>
						<text v-if="password.length>index">●</text>
					</view>
					<input type="text" maxlength="6" class="box_r_input" @input="inputPassword" @focus="focusPassword" @blur="blurPassword">
				</view>
			</view>
			<view class="box">
				<view class="box_l">确认密码：</view>
				<view class="box_r">
					<view class="box_r_item" v-for="(item,index) in 6" :key="index">
						<text v-if="confirmPassword.length==index&&showComfirn">|</text>
						<text v-if="confirmPassword.length>index">●</text>
					</view>
					<input type="text" maxlength="6" class="box_r_input" @input="inputConfirm" @focus="focusConfirm" @blur="blurConfirm">
				</view>
			</view>
			<view class="hint" v-if="title=='设置平台支付密码'">支付提现密码必须为6位数字</view>
			<view class="hint" v-if="title=='登录密码'">登录密码必须为6位数字</view>
			<view class="button" @click="confirm" v-if="mobile&&verification&&password&&confirmPassword">提交</view>
			<view class="button" @click="hint" v-else>提交</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				mobile: '',
				title: '',
				password: '', //密码
				confirmPassword: '', //确认密码
				showPassword: false, //是否显示密码光标
				passwordFour: null, //密码光标时间定时器
				showComfirn: false, //是否显示确定密码光标
				comfrinFour: null, //确定密码定时器
				verification: '', //验证码
				showSend: true, //是否可以发送
				time: 60,
				timeText: '发送验证码', //倒计时文本
				type: false,
			};
		},
		onLoad(options) {
			let that = this
			
			that.title = options.title
			if(options.type){
				that.type = 1
			}
			uni.setNavigationBarTitle({
				title: options.title
			});
		},
		onShow() {
			let that = this
			
			that.mobile = uni.getStorageSync('phone')
		},
		methods: {
			// 输入手机号
			inputPhone(e){
				this.mobile = e.detail.value
			},
			// 发送验证码
			clickSend(){
				let that = this
				
				let url = 'api.user/sendAliDaYuAuthCode'
				if(that.type==1){
					url = 'api.login/sendAliDaYuAuthCode'
				}
				
				if(that.showSend){
					uni.request({
						url: getApp().globalData.url + url,
						method: 'POST',
						header: {
							token: uni.getStorageSync('token')
						},
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
			// 输入密码
			inputPassword(e){
				let that = this
				that.password = e.detail.value
			},
			// 密码光标聚焦
			focusPassword(){
				let that = this
				
				clearInterval(that.comfrinFour)
				that.passwordFour = setInterval(()=>{
					that.showPassword = !that.showPassword
				},500)
			},
			// 密码光标失去焦点
			blurPassword(){
				let that = this
				
				clearInterval(that.passwordFour)
				that.showPassword = false
			},
			
			// 确定密码光标聚焦
			focusConfirm(){
				let that = this
				
				clearInterval(that.passwordFour)
				that.comfrinFour = setInterval(()=>{
					that.showComfirn = !that.showComfirn
				},500)
			},
			// 确定密码光标失去焦点
			blurConfirm(){
				let that = this
				
				clearInterval(that.comfrinFour)
				that.showComfirn = false
			},
			
			// 确认输入
			inputConfirm(e){
				this.confirmPassword = e.detail.value
			},
			// 提示
			hint(){
				uni.showToast({
					title: '请填写所有相关信息',
					icon: 'none'
				})
			},
			// 确定提交
			confirm(){
				let that = this
				
				let data = {}
				if(that.title == '设置平台支付密码'){
					// 支付密码
					data = {
						mobile: that.mobile,
						code: that.verification,
						payment_password : that.password
					}
				}else{
					// 登录密码
					data = {
						mobile: that.mobile,
						code: that.verification,
						newpassword : that.password
					}
				}
				
				let url = 'api.user/getzhifu'
				if(that.type==1){
					url = 'api.login/getzhifu'
				}
				
				if(that.password==that.confirmPassword){
					uni.request({
						url: getApp().globalData.url + url,
						method: 'POST',
						header: {
							token: uni.getStorageSync('token')
						},
						data: data,
						success(res) {
							if(res.data.code==1){
								uni.showToast({
									title: res.data.msg,
									success() {
										setTimeout(()=>{
											// uni.setStorageSync('phone',that.mobile)
											uni.navigateBack()
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
				}else{
					uni.showToast({
						title: '两次输入的密码不同',
						icon: 'none'
					})
				}
			},
		}
	}
</script>

<style lang="scss">
	page{
		background: #F4F4F4;
	}
	.boxes{
		margin: 60rpx 24rpx;
		width: 702rpx;
		height: 946rpx;
		background: #FFFFFF;
		border-radius: 8rpx;
		overflow: hidden;
		
		&_title{
			margin-top: 64rpx;
			height: 44rpx;
			font-size: 32rpx;
			font-family: PingFangSC-Medium, PingFang SC;
			font-weight: 500;
			color: #000000;
			line-height: 44rpx;
			text-align: center;
		}
		.box{
			margin-top: 32rpx;
			height: 76rpx;
			
			&_l{
				float: left;
				margin-left: 16rpx;
				width: 200rpx;
				height: 76rpx;
				font-size: 28rpx;
				font-family: PingFangSC-Regular, PingFang SC;
				font-weight: 400;
				color: #333333;
				line-height: 76rpx;
			}
			&_phone{
				float: left;
				width: 240rpx;
				height: 76rpx;
				font-size: 28rpx;
				font-family: PingFangSC-Regular, PingFang SC;
				font-weight: 400;
				color: #333333;
				line-height: 76rpx;
			}
			&_verification{
				float: left;
				width: 208rpx;
				height: 76rpx;
				border-radius: 38rpx;
				border: 2rpx solid #EEC873;
				text-align: center;
				font-size: 28rpx;
				font-family: PingFangSC-Regular, PingFang SC;
				font-weight: 400;
				color: #EEC873;
				line-height: 76rpx;
				box-sizing: border-box;
			}
			&_r{
				position: relative;
				float: left;
				height: 76rpx;
				overflow: hidden;
				
				&_item{
					float: left;
					margin-right: 12rpx;
					width: 66rpx;
					height: 76rpx;
					border: 2rpx solid #EEC873;
					text-align: center;
					font-size: 28rpx;
					font-family: PingFangSC-Regular, PingFang SC;
					font-weight: 400;
					color: #EEC873;
					line-height: 76rpx;
					box-sizing: border-box;
				}
				&_input{
					position: absolute;
					top: 0rpx;
					left: -1000rpx;
					width: 1480rpx;
					height: 76rpx;
					opacity: 0;
					z-index: 999;
				}
			}
		}
		.hint{
			margin-top: 60rpx;
			height: 40rpx;
			font-size: 24rpx;
			font-family: PingFangSC-Regular, PingFang SC;
			font-weight: 400;
			color: #A4A4A4;
			line-height: 40rpx;
			text-align: center;
		}
		.button{
			margin: 100rpx 20rpx;
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
	}
</style>
