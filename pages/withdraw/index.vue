<template>
	<view>
		<view class="top">
			<!-- <image src="/static/coin_bg.png" class="top_img"></image> -->
			<view class="top_t">
				<view class="top_t_item">{{tol_money}}</view>
				<view class="top_t_item">{{use_money}}</view>
				<view class="top_t_item">{{money}}</view>
				<view class="top_t_item">{{dongjie}}</view>
			</view>
			<view class="top_c">
				<view class="top_c_item">总收入</view>
				<view class="top_c_item">已提现</view>
				<view class="top_c_item">剩余金额</view>
				<view class="top_c_item">待审核</view>
			</view>
			<view class="top_b">
				<view class="top_b_l" @click="withdraw">去提现</view>
				<!-- <view class="top_b_r" @click="integral">转为购物积分</view> -->
			</view>
		</view>
		<view class="tool">
			<view class="tool_title"><text></text>工具</view>
			<!-- <view class="tool_box" @click="coinBuy">
				<view class="tool_box_l">现金充值</view>
				<view class="tool_box_r">去充值></view>
			</view> -->
			<!-- <view class="tool_box" @click="coinDetail">
				<view class="tool_box_l">金额明细</view>
				<view class="tool_box_r">查看></view>
			</view> -->
			<view class="tool_boxes">
				<view class="tool_box" @click="record">
					<view class="tool_box_l">提现记录</view>
					<view class="tool_box_r">查看></view>
				</view>
				<view class="tool_box" @click="bindCard">
					<view class="tool_box_l">账号</view>
					<view class="tool_box_r">查看></view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				list: '',
				tol_money: '',
				use_money: '',
				money: '',
				dongjie: '',
			}
		},
		onLoad() {

		},
		onShow() {
			let that = this
			
			// #ifdef APP
			uni.request({
				url: getApp().globalData.url + 'api.user/tixiani',
				method: 'POST',
				header: {
					token: uni.getStorageSync('token')
				},
				success(res) {
					if(res.data.code==1){
						that.list = res.data.data
						uni.setStorageSync('mymoney',res.data.data.money)
						that.tol_money = parseInt(res.data.data.tol_money)
						that.use_money = parseInt(res.data.data.use_money)
						that.money = parseInt(res.data.data.money)
						that.dongjie = parseInt(res.data.data.dongjie)
					}
				}
			})
			// #endif
			
			// #ifdef MP-WEIXIN
			uni.request({
				url: getApp().globalData.url + 'api.user/tixiani',
				method: 'POST',
				header: {
					token: uni.getStorageSync('token')
				},
				success(res) {
					if(res.data.code==1){
						that.list = res.data.data
						uni.setStorageSync('mymoney',res.data.data.money)
						that.tol_money = parseInt(res.data.data.tol_money)
						that.use_money = parseInt(res.data.data.use_money)
						that.money = parseInt(res.data.data.money)
						that.dongjie = parseInt(res.data.data.dongjie)
					}
				}
			})
			// #endif
			
			// #ifdef H5
			if(!uni.getStorageSync('my')){
				uni.navigateTo({
					url: '/pages/index/login'
				})
			}else{
				uni.request({
					url: getApp().globalData.url + 'api.user/tixiani',
					method: 'POST',
					header: {
						token: uni.getStorageSync('token')
					},
					success(res) {
						if(res.data.code==1){
							that.list = res.data.data
							uni.setStorageSync('mymoney',res.data.data.money)
							that.tol_money = parseInt(res.data.data.tol_money)
							that.use_money = parseInt(res.data.data.use_money)
							that.money = parseInt(res.data.data.money)
							that.dongjie = parseInt(res.data.data.dongjie)
						}else{
							uni.navigateTo({
								url: '/pages/index/login'
							})
							uni.showToast({
								title: res.data.msg,
								icon: 'none'
							})
						}
					}
				})
			}
			// #endif
		},
		methods: {
			// 提现
			withdraw(){
				uni.navigateTo({
					url: '/pages/withdraw/withdraw'
				})
			},
			// 转为积分
			integral(){
				uni.navigateTo({
					url: '/pages/withdraw/integral'
				})
			},
			// 健康币充值
			// coinBuy(){
			// 	uni.navigateTo({
			// 		url: '/pages/index/coinBuy'
			// 	})
			// },
			// 健康币明细
			coinDetail(){
				uni.navigateTo({
					url: '/pages/withdraw/coinDetail'
				})
			},
			
			// 使用记录
			record(){
				uni.navigateTo({
					url: '/pages/withdraw/record'
				})
			},
			
			//账号
			bindCard(){
				uni.navigateTo({
					url: '/pages/withdraw/bindCard'
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
		position: relative;
		margin: 36rpx 32rpx;
		width: 686rpx;
		height: 296rpx;
		border-radius: 16rpx;
		background: linear-gradient(135deg, #EDC66E 0%, #F6E1B0 100%);
		overflow: hidden;
		
		&_img{
			width: 686rpx;
			height: 296rpx;
		}
		&_t{
			position: absolute;
			top: 48rpx;
			width: 686rpx;
			height: 60rpx;
			overflow: hidden;
			
			&_item{
				float: left;
				width: 25%;
				height: 60rpx;
				font-size: 32rpx;
				font-family: PingFangSC-Medium, PingFang SC;
				font-weight: 500;
				color: #000000;
				line-height: 44rpx;
				text-align: center;
			}
		}
		&_c{
			position: absolute;
			top: 130rpx;
			width: 686rpx;
			height: 34rpx;
			overflow: hidden;
			
			&_item{
				float: left;
				width: 25%;
				height: 34rpx;
				font-size: 28rpx;
				font-family: PingFangSC-Medium, PingFang SC;
				font-weight: 500;
				color:#404040;
				line-height: 34rpx;
				text-align: center;
			}
		}
		&_b{
			position: absolute;
			top: 200rpx;
			left: 207rpx;
			width: 686rpx;
			height: 80rpx;
			overflow: hidden;
			
			&_l{
				width: 262rpx;
				height: 80rpx;
				background: #EEC873;
				border-radius: 39rpx;
				text-align: center;
				font-size: 28rpx;
				font-family: PingFangSC-Medium, PingFang SC;
				font-weight: 500;
				color: #000000;
				line-height: 80rpx;
			}
			// &_l{
			// 	float: left;
			// 	width: 272rpx;
			// 	height: 72rpx;
			// 	border-radius: 36rpx;
			// 	border: 2rpx solid #FFFFFF;
			// 	text-align: center;
			// 	font-size: 28rpx;
			// 	font-family: PingFangSC-Regular, PingFang SC;
			// 	font-weight: 400;
			// 	color: #FFFFFF;
			// 	line-height: 72rpx;
			// 	box-sizing: border-box;
			// }
			&_r{
				float: right;
				width: 272rpx;
				height: 72rpx;
				border-radius: 36rpx;
				border: 2rpx solid #FFFFFF;
				text-align: center;
				font-size: 28rpx;
				font-family: PingFangSC-Regular, PingFang SC;
				font-weight: 400;
				color: #FFFFFF;
				line-height: 72rpx;
				box-sizing: border-box;
			}
		}
	}
	// 工具
	.tool{
		margin: 36rpx 24rpx;
		overflow: hidden;
		
		&_title{
			height: 40rpx;
			font-size: 28rpx;
			font-family: PingFangSC-Medium, PingFang SC;
			font-weight: 500;
			color: #333333;
			line-height: 40rpx;
			
			text{
				float: left;
				margin-top: 6rpx;
				margin-right: 12rpx;
				width: 8rpx;
				height: 28rpx;
				background: #EEC873;
				border-radius: 4rpx;
			}
		}
		&_boxes{
			padding-top: 12rpx;
			padding-bottom: 60rpx;
			margin-top: 32rpx;
			width: 702rpx;
			background: #FFFFFF;
			border-radius: 16rpx;
			
			.tool_box{
				padding: 0rpx 24rpx;
				height: 80rpx;
				border-bottom: 2rpx solid #F9F9F9;
				overflow: hidden;
				
				&_l{
					float: left;
					height: 80rrpx;
					font-size: 28rpx;
					font-family: PingFangSC-Regular, PingFang SC;
					font-weight: 400;
					color: #333333;
					line-height: 80rpx;
				}
				&_r{
					float: right;
					height: 80rpx;
					font-size: 28rpx;
					font-family: PingFangSC-Regular, PingFang SC;
					font-weight: 400;
					color: #9D9D9D;
					line-height: 80rpx;
				}
			}
		}
	}
</style>
