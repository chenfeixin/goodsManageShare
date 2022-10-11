<template>
	<view>
		<view class="topBox">
			<image src="/static/home_header.png" class="topImg"></image>
		</view>
		<view class="top">
			<image src="/static/home_top.png" class="top_img"></image>
			<image :src="list.avatar" class="top_avatar" v-if="list.avatar!=''"></image>
			<image src="/static/myInvite_user.png" class="top_avatar" v-else></image>
			<view class="top_name">{{list.nickname}}</view>
			<view class="top_phone">{{list.mobile}}</view>
			<view class="top_integral"><text>余额：</text>{{list.money}}</view>
		</view>
		<view class="box">
			<view class="box_title">购买VIP卡，享更多收益 <text @click="clickInvite">邀请好友></text></view>
			<view class="box_list">
				<view class="boxes" v-for="(item,index) in lists">
					<image :src="item.img" class="boxes_img"></image>
					<view class="boxes_title">{{item.title}}</view>
					<view class="boxes_text">{{item.text}}</view>
				</view>
			</view>
			<view class="box_button" @click="showPopup">购买VIP</view>
		</view>
		
		<view class="popup_bg" v-if="popup" @click="hidePopup"></view>
		<view class="popup" v-if="popup">
			<view class="popup_title">购买VIP卡，享更多收益</view>
			<view class="popup_text">购买数量</view>
			<view class="popup_box">
				<view class="popup_box_l" @click="minus">－</view>
				<input type="number" maxlength="3" class="popup_box_c" :value="number" @input="inputNumber">
				<view class="popup_box_r" @click="add">+</view>
			</view>
			<view class="popup_button" @click="confirmWx">确定购买</view>
		</view>
		
	</view>
</template>

<script>
	export default {
		data() {
			return {
				list: [],
				lists: [
					// {img: '/static/home_1.png', title: '爱牙积分', text: '爱牙医院专享治疗', url: ''},
					{img: '/static/home_2.png', title: '现金收益', text: '邀请好友获得收益', url: ''},
					// {img: '/static/home_3.png', title: '平台分成', text: '邀请会员卡2000现金', url: ''},
					{img: '/static/home_4.png', title: '漱口水运营', text: '全方位委托运营', url: ''},
					{img: '/static/home_5.png', title: '漱口水收益', text: '漱口水销售分成', url: ''}
				],
				number: 1, //购卡数量
				popup: false, //弹窗是否显示
			}
		},
		
		
		onShow() {
			let that = this
			
			if(!uni.getStorageSync('user')){
				uni.reLaunch({
					url: '/pages/login/login'
				})
			}else{
				uni.request({
					url: getApp().globalData.url + 'api.login/isLogins',
					method: 'POST',
					data: {
						uid: uni.getStorageSync('user').id
					},
					success(res) {
						if(res.data.code==1){
							console.log(res)
						}else{
							uni.reLaunch({
								url: '/pages/login/login'
							})
						}
					}
				})
			}
			
			that.request()
			
			// 合同
			uni.request({
				url: getApp().globalData.url + 'api.user/index',
				method: 'GET',
				header: {
					token: uni.getStorageSync('token')
				},
				success(res) {
					if(res.data.code==1){
						that.score = res.data.data.score
						if(res.data.data.mobile){
							uni.setStorageSync('phone',res.data.data.mobile)
						}
						if(res.data.data.contract>0){
							uni.showModal({
								title: '提示',
								content: '请先签署合同',
								showCancel: false,
								success: function (res) {
									if (res.confirm) {
										uni.navigateTo({
											url: '/pages/my/buyList'
										})
									}
								}
							})
						}
					}
				}
			})
		},
		
		methods: {
			request(){
				let that = this
				
				uni.request({
					url: getApp().globalData.url + 'api.user/index',
					method: 'GET',
					header: {
						token: uni.getStorageSync('token')
					},
					success(res) {
						if(res.data.code==1){
							that.list = res.data.data
						}else{
							uni.showToast({
								title: res.data.msg,
								icon: 'none'
							})
						}
					}
				})
			},
			
			// 打开分享
			clickInvite(){
				let that = this
				
				uni.navigateTo({
					url: '/pages/my/invite'
				})
			},
			
			// 打开购买弹窗
			showPopup(){
				let that = this
				
				that.popup = true
			},
			
			// 关闭弹窗
			hidePopup(){
				let that = this
				
				that.popup = false
			},
			
			// 减
			minus(){
				let that = this
				
				if(that.number==1){
					return false
				}
				that.number = that.number-1
			},
			
			// 输入框
			inputNumber(e){
				let that = this
				
				that.number = e.detail.value
			},
			
			// 加
			add(){
				let that = this
				
				if(that.number!=999){	
					that.number = that.number*1+1
				}
			},
			
			// 微信-确认购买
			confirmWx() {
				let that = this

				uni.showLoading({
					title: '请稍后'
				})
				// that.showImg = false
				uni.login({
					success(res) {
						wx.request({
							url: 'https://www.gargle.top/addons/shop/api.login/huiyuank',
							method: 'POST',
							data: {
								code: res.code,
								money: that.number * 2400,
								// money: 0.01,
								user_id: uni.getStorageSync('user').id,
								mode: 'wx_lite',
							},
							success(res) {
								uni.hideLoading()
								if (res.data.code == 1) {
									let info = JSON.parse(res.data.data.expend.pay_info)
									wx.requestPayment({
										timeStamp: info.timeStamp,
										nonceStr: info.nonceStr,
										package: info.package,
										signType: 'MD5',
										paySign: info.paySign,
										success(res) {
											uni.redirectTo({
												url: '/pages/my/buyList'
											})
										},
										fail(res) {
											wx.showToast({
												title: '支付失败',
												icon: 'none'
											})
										}
									})
								} else {
									uni.showToast({
										title: res.data.msg,
										icon: 'none'
									})
								}
							}
						})
					}
				})
			},
		}
	}
</script>

<style lang="scss">
	page{
		background: linear-gradient(180deg, #373737 0%, #292929 100%);
		box-shadow: 0rpx -8rpx 14rpx 0rpx rgba(32,32,32,0.36);
	}
	.topBox{
		padding-bottom: 180rpx;
		width: 750rpx;
		height: 516rpx;
		background: #FFFFFF;
		overflow: hidden;
	}
	.topImg{
		width: 750rpx;
		height: 516rpx;
		overflow: hidden;
		z-index: 1;
	}
	.top{
		position: relative;
		margin: -200rpx 32rpx 0rpx;
		width: 686rpx;
		height: 214rpx;
		border-radius: 16rpx;
		z-index: 2;
		overflow: hidden;
		
		&_img{
			width: 686rpx;
			height: 214rpx;
		}
		&_avatar{
			position: absolute;
			top: 36rpx;
			left: 52rpx;
			width: 100rpx;
			height: 100rpx;
			border-radius: 50%;
			overflow: hidden;
		}
		&_name{
			position: absolute;
			top: 46rpx;
			left: 180rpx;
			width: 478rpx;
			height: 42rpx;
			font-size: 30rpx;
			font-family: PingFangSC-Medium, PingFang SC;
			font-weight: 500;
			color: #593C13;
			line-height: 42rpx;
			text-shadow: 0rpx 2rpx 4rpx rgba(255,255,255,0.5);
		}
		&_phone{
			position: absolute;
			top: 96rpx;
			left: 184rpx;
			width: 360rpx;
			height: 34rpx;
			font-size: 24rpx;
			font-family: PingFangSC-Regular, PingFang SC;
			font-weight: 400;
			color: #593C13;
			line-height: 34rpx;
		}
		&_integral{
			position: absolute;
			bottom: 32rpx;
			right: 46rpx;
			width: 300rpx;
			height: 44rpx;
			font-size: 32rpx;
			font-family: PingFangSC-Regular, PingFang SC;
			font-weight: 400;
			color: #593C13;
			line-height: 44rpx;
			text-align: right;
			
			text{
				font-size: 22rpx;
			}
		}
	}
	.box{
		padding-bottom: 32rpx;
		margin-top: -32rpx;
		width: 750rpx;
		height: 666rpx;
		background: linear-gradient(180deg, #373737 0%, #292929 100%);
		box-shadow: 0rpx -8rpx 14rpx 0rpx rgba(32,32,32,0.36);
		overflow: hidden;
		
		&_title{
			margin: 64rpx 32rpx 0rpx;
			height: 48rpx;
			font-size: 34rpx;
			font-family: PingFangSC-Medium, PingFang SC;
			font-weight: 500;
			color: #EEE4C1;
			line-height: 48rpx;
			overflow: hidden;
			
			text{
				float: right;
				font-size: 24rpx;
				font-family: PingFangSC-Regular, PingFang SC;
				font-weight: 400;
				color: #B8B1A3;
			}
		}
		&_list{
			margin: 42rpx 0rpx 48rpx;
			overflow: hidden;
			
			.boxes{
				float: left;
				margin-bottom: 36rpx;
				width: 33%;
				height: 180rpx;
				overflow: hidden;
				
				&_img{
					margin-left: 50%;
					transform: translate(-50%);
					width: 80rpx;
					height: 80rpx;
				}
				&_title{
					margin-top: 8rpx;
					height: 36rpx;
					font-size: 26rpx;
					font-family: PingFangSC-Medium, PingFang SC;
					font-weight: 500;
					color: #EEE4C1;
					line-height: 36rpx;
					text-align: center;
				}
				&_text{
					margin-top: 12rpx;
					height: 32rpx;
					font-size: 22rpx;
					font-family: PingFangSC-Regular, PingFang SC;
					font-weight: 400;
					color: #B8B1A3;
					line-height: 32rpx;
					text-align: center;
				}
			}
		}
		&_button{
			margin: 0rpx 33rpx 24rpx;
			width: 684rpx;
			height: 92rpx;
			background: linear-gradient(90deg, #F6E1B0 0%, #EDC66E 100%);
			border-radius: 50rpx;
			text-align: center;
			font-size: 32rpx;
			font-family: PingFangSC-Semibold, PingFang SC;
			font-weight: 600;
			color: #333333;
			line-height: 92rpx;
			overflow: hidden;
		}
	}
	
	.popup_bg{
		position: fixed;
		top: 0rpx;
		left: 0rpx;
		width: 100%;
		height: 100%;
		background: rgba(0,0,0,0.5);
		z-index: 3;
		overflow: hidden;
	}
	.popup{
		position: fixed;
		bottom: 0rpx;
		left: 0rpx;
		width: 100%;
		height: 544rpx;
		background: #FFFFFF;
		border-radius: 24rpx 24rpx 0rpx 0rpx;
		z-index: 4;
		overflow: hidden;
		
		&_title{
			margin-top: 64rpx;
			height: 44rpx;
			font-size: 32rpx;
			font-family: PingFangSC-Medium, PingFang SC;
			font-weight: 600;
			color: #000000;
			line-height: 44rpx;
			text-align: center;
		}
		&_text{
			margin-top: 48rpx;
			height: 40rpx;
			font-size: 28rpx;
			font-family: PingFangSC-Regular, PingFang SC;
			font-weight: 400;
			color: #000000;
			line-height: 40rpx;
			text-align: center;
		}
		&_box{
			margin-top: 44rpx;
			height: 56rpx;
			overflow: hidden;
			
			&_l{
				float: left;
				margin-left: 260rpx;
				width: 32rpx;
				height: 56rpx;
				font-size: 36rpx;
				font-family: PingFangSC-Medium, PingFang SC;
				font-weight: 500;
				color: #252C3A;
				line-height: 56rpx;
				text-align: center;
			}
			&_c{
				float: left;
				margin: 0rpx 20rpx;
				width: 124rpx;
				height: 56rpx;
				background: #F3F3F7;
				border-radius: 8rpx;
				text-align: center;
				font-size: 28rpx;
				font-family: PingFangSC-Medium, PingFang SC;
				font-weight: 500;
				color: #252C3A;
				line-height: 56rpx;
			}
			&_r{
				float: left;
				width: 32rpx;
				height: 56rpx;
				font-size: 36rpx;
				font-family: PingFangSC-Medium, PingFang SC;
				font-weight: 500;
				color: #252C3A;
				line-height: 56rpx;
				text-align: center;
			}
		}
		&_button{
			margin: 88rpx 155rpx;
			width: 440rpx;
			height: 84rpx;
			background: linear-gradient(90deg, #F6E1B0 0%, #EDC66E 100%);
			border-radius: 50rpx;
			text-align: center;
			font-size: 32rpx;
			font-family: PingFangSC-Semibold, PingFang SC;
			font-weight: 600;
			color: #333333;
			line-height: 84rpx;
		}
	}
</style>
