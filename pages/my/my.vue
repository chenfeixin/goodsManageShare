<template>
	<view>
		<!-- 个人中心 -->
		<view class="top">
			<image :src="list.avatar" class="top_img" v-if="list.avatar" @click="calling"></image>
			<image src="/static/myInvite_user.png" class="top_img" @click="calling" v-else></image>
			<view class="top_c">
				<view class="top_c_name">{{list.nickname}}</view>
				<view class="top_c_phone">{{list.mobile}}</view>
			</view>
			<view class="top_r">
				<view class="top_r_text">{{list.user_category}}</view>
				<view class="top_r_border"></view>
				<view class="top_r_group" @click="skip('/pages/my/groupDetail')">团队详情</view>
			</view>
		</view>
		
		<!-- 数据 -->
		<view class="list" v-if="list">
			<view class="list_box" @click="skip('/pages/my/buyList')">
				<view class="list_box_t">{{list.user_order}}</view>
				<view class="list_box_f">购买记录</view>
			</view>
			<view class="list_box" @click="skip('/pages/my/coin')">
				<view class="list_box_t">{{list.money}}</view>
				<view class="list_box_f">现金</view>
			</view>
		</view>
		
		<view class="boxes" v-if="list">
			<view class="boxes_box" @click="skip('/pages/my/member')">
				<view class="boxes_box_t">¥{{list.moneys}}</view>
				<view class="boxes_box_f">我的奖励</view>
			</view>
			<view class="boxes_box" style="margin-left: 28rpx;" @click="skip('/pages/my/myInvite')">
				<view class="boxes_box_t">¥{{list.profit}}</view>
				<view class="boxes_box_f">漱口水收益</view>
			</view>
		</view>
		
		<!-- 工具 -->
		<view class="tool_title">工具</view>
		<view class="toolbox" @click="skip('/pages/my/invite')">
			<view class="toolbox_l">邀请好友</view>
			<image src="/static/more.png" class="toolbox_r"></image>
		</view>
		<view class="toolbox" @click="skip('/pages/my/goodsDetail')">
			<view class="toolbox_l">免费领取漱口水</view>
			<image src="/static/more.png" class="toolbox_r"></image>
		</view>
		<view class="toolbox" @click="skip('/pages/my/myGet')">
			<view class="toolbox_l">漱口水领取详情</view>
			<image src="/static/more.png" class="toolbox_r"></image>
		</view>
		<view class="toolbox" @click="skip('/pages/my/address')">
			<view class="toolbox_l">收货地址</view>
			<image src="/static/more.png" class="toolbox_r"></image>
		</view>
		<!-- <view class="toolbox" @click="skip('/pages/my/groupDetail')">
			<view class="toolbox_l">团队详情</view>
			<image src="/static/more.png" class="toolbox_r"></image>
		</view> -->
		<!-- <view class="toolbox" @click="call">
			<view class="toolbox_l">客服热线</view>
			<image src="/static/more.png" class="toolbox_r"></image>
		</view> -->
		<view class="toolbox" @click="skip('/pages/my/set')">
			<view class="toolbox_l">设置</view>
			<image src="/static/more.png" class="toolbox_r"></image>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				list: '',
			};
		},
		
		onShow() {
			let that = this
			
			if(!uni.getStorageSync('user')){
				uni.showModal({
					title: '提示',
					content: '请先登录！',
					success: function (res) {
						if (res.confirm) {
							uni.navigateTo({
								url: '/pages/login/login'
							})
						} else if (res.cancel) {
							uni.switchTab({
								url: '/pages/index/index'
							})
						}
					}
				});
			}else{
				that.request()
				
				uni.request({
					url: getApp().globalData.url + 'api.login/isLogins',
					method: 'POST',
					header: {
						token: uni.getStorageSync('token')
					},
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
			
			// 点击跳转
			skip(url){
				let that = this
				
				if(url=='/pages/my/set'){
					uni.navigateTo({
						url: url + '?name='+that.list.nickname + '&avatar='+that.list.avatar + '&phone=' + that.list.mobile
					})
				}else{
					uni.navigateTo({
						url
					})
				}
			},
			
			// 名片
			calling(){
				let that = this
				
				uni.navigateTo({
					url: '/pages/my/callingCard?id='+that.list.id
				})
			},
			
			// 拨打电话
			call(){
				uni.makePhoneCall({
					phoneNumber: '4001360376'
				})
			},
		}
	}
</script>

<style lang="scss">
	page{
		background: #F9F9F9;
	}
	.top{
		width: 750rpx;
		height: 440rpx;
		background: linear-gradient(90deg, #F6E1B0 0%, #EDC66E 100%);
		overflow: hidden;
		
		&_img{
			float: left;
			margin-top: 180rpx;
			margin-left: 32rpx;
			width: 120rpx;
			height: 120rpx;
			border-radius: 50%;
			overflow: hidden;
		}
		&_c{
			float: left;
			margin-top: 200rpx;
			margin-left: 16rpx;
			overflow: hidden;
			
			&_name{
				height: 42rpx;
				font-size: 30rpx;
				font-family: PingFangSC-Medium, PingFang SC;
				font-weight: 500;
				color: #593C13;
				line-height: 42rpx;
				text-shadow: 0rpx 2rpx 4rpx rgba(255,255,255,0.5);
				overflow: hidden;
			}
			&_phone{
				margin-top: 10rpx;
				height: 34rpx;
				font-size: 24rpx;
				font-family: PingFangSC-Regular, PingFang SC;
				font-weight: 400;
				color: #593C13;
				line-height: 34rpx;
			}
		}
		&_r{
			float: right;
			margin-top: 204rpx;
			margin-right: 42rpx;
			width: 130rpx;
			overflow: hidden;
			
			&_text{
				width: 130rpx;
				height: 40rpx;
				font-size: 28rpx;
				font-family: PingFangSC-Medium, PingFang SC;
				font-weight: 500;
				color: #181919;
				line-height: 40rpx;
				text-align: center;
			}
			&_border{
				margin-top: -10rpx;
				margin-left: 9rpx;
				width: 112rpx;
				height: 12rpx;
				background: #FFFFFF;
				border-radius: 6rpx;
			}
			&_group{
				margin-top: 8rpx;
				width: 130rpx;
				height: 42rpx;
				font-size: 28rpx;
				font-family: PingFangSC-Medium, PingFang SC;
				font-weight: 500;
				color: #2200FF;
				line-height: 40rpx;
				text-align: center;
				text-decoration: underline;
			}
		}
	}
	
	// 数据
	.list{
		margin: -32rpx 24rpx 0rpx;
		width: 702rpx;
		height: 198rpx;
		background: #FFFFFF;
		border-radius: 8rpx;
		display: flex;
		flex-direction: row;
		align-items: center;
		overflow: hidden;
		
		&_box{
			flex: 1;
			overflow: hidden;
			
			&_t{
				height: 50rpx;
				font-size: 36rpx;
				font-family: PingFangSC-Semibold, PingFang SC;
				font-weight: 600;
				color: #EEC873;
				line-height: 50rpx;
				text-align: center;
			}
			&_f{
				margin-top: 26rpx;
				height: 40rpx;
				font-size: 28rpx;
				font-family: PingFangSC-Regular, PingFang SC;
				font-weight: 400;
				color: #666666;
				line-height: 40rpx;
				text-align: center;
			}
		}
	}
	
	.boxes{
		margin: 28rpx 24rpx 0rpx;
		height: 186rpx;
		overflow: hidden;
		
		&_box{
			float: left;
			width: 336rpx;
			height: 186rpx;
			background: #F5DEA9;
			border-radius: 8rpx;
			overflow: hidden;
			
			&_t{
				margin-top: 40rpx;
				height: 50rpx;
				font-size: 36rpx;
				font-family: PingFangSC-Medium, PingFang SC;
				font-weight: 500;
				color: #000000;
				line-height: 50rpx;
				text-align: center;
			}
			&_f{
				margin-top: 24rpx;
				height: 40rpx;
				font-size: 28rpx;
				font-family: PingFangSC-Medium, PingFang SC;
				font-weight: 500;
				color: #515151;
				line-height: 40rpx;
				text-align: center;
			}
		}
	}
	
	// 工具
	.tool_title{
		margin: 56rpx 28rpx 26rpx;
		height: 44rpx;
		font-size: 32rpx;
		font-family: PingFangSC-Medium, PingFang SC;
		font-weight: 500;
		color: #333333;
		line-height: 44rpx;
		overflow: hidden;
	}
	.toolbox{
		margin: 0rpx 22rpx 0rpx 28rpx;
		width: 700rpx;
		height: 76rpx;
		border-bottom: 2rpx solid #F6E1B0;
		overflow: hidden;
		
		&_l{
			float: left;
			margin-top: 26rpx;
			height: 40rpx;
			font-size: 28rpx;
			font-family: PingFangSC-Regular, PingFang SC;
			font-weight: 400;
			color: #000000;
			line-height: 40rpx;
		}
		&_r{
			float: right;
			margin-top: 28rpx;
			width: 24rpx;
			height: 36rpx;
		}
	}
</style>
