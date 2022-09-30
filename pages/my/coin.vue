<template>
<!-- 我的现金 -->
	<view>
		<!-- 头部 -->
		<view class="top">
			<!-- <image src="/static/coin_bg.png" class="top_img"></image> -->
			<view class="topbox">
				<view class="topbox_box">
					<view class="topbox_box_t">{{tole_money}}</view>
					<view class="topbox_box_f">总收入</view>
				</view>
				<view class="topbox_box">
					<view class="topbox_box_t">{{reduce_money}}</view>
					<view class="topbox_box_f">已提现</view>
				</view>
				<view class="topbox_box">
					<view class="topbox_box_t">{{money}}</view>
					<view class="topbox_box_f">剩余</view>
				</view>
			</view>
			<view class="top_f" @click="withdraw">去提现></view>
		</view>
		
		<view class="title"><text></text>现金明细</view>
		<!-- <view class="top">
			<view class="top_t">
				<view class="top_t_num">{{tole_money}}</view>
				<view class="top_t_num">{{reduce_money}}</view>
				<view class="top_t_num">{{money}}</view>
			</view>
			<view class="top_c">
				<view class="top_c_text">总收入</view>
				<view class="top_c_text">已提现</view>
				<view class="top_c_text">剩余</view>
			</view>
			<view class="top_text" @click="withdraw">去提现></view>	
		</view> -->
		<!-- 数据 -->
		<view class="boxes">
			<view class="boxes_t">
				<view class="boxes_t_box">时间</view>
				<view class="boxes_t_box">类型</view>
				<view class="boxes_t_box">现金</view>
				<view class="boxes_t_box">剩余金额</view>
			</view>
			<block v-for="(item,index) in list.list" :key="index">
				<view class="box">
					<view class="box_item">{{item.cattime}}</view>
					<view class="box_item" v-if="item.type==1">VIP卡推荐返利</view>
					<view class="box_item" v-else-if="item.type==2">现金转积分</view>
					<view class="box_item" v-else-if="item.type==3">金额提现</view>
					<view class="box_item" v-else-if="item.type==4">职位奖励</view>
					<view class="box_item" v-else-if="item.type==5">漱口水职位奖励</view>
					<view class="box_item" v-else-if="item.type==6">领取现金红包</view>
					<view class="box_item" v-else-if="item.type==7">购买VIP卡</view>
					<view class="box_item" v-else-if="item.type==8">退还红包</view>
					<view class="box_item" v-else-if="item.type==9">赠送好友</view>
					<view class="box_item" v-else-if="item.type==10">充值</view>
					<view class="box_item" v-else-if="item.type==11">股东福利1</view>
					<view class="box_item" v-else-if="item.type==12">股东福利2</view>
					<view class="box_item" v-else-if="item.type==13">邀请佣金</view>
					<view class="box_item" v-else-if="item.type==14">每日红包</view>
					<view class="box_item" v-else-if="item.type==15">售后分红</view>
					<view class="box_item" v-else-if="item.type==16">大富翁红包</view>
					<view class="box_item" v-else-if="item.type==17">退还本金</view>
					<view class="box_item" style="color: #CBA55F;">{{item.status==1?'+':'-'}}{{item.money}}</view>
					<view class="box_item">{{item.money2}}</view>
				</view>
			</block>
		</view>
		
		<service></service>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				list: [],
				money: 0,
				reduce_money: 0,
				tole_money: 0,
			};
		},
		onShow() {
			let that = this
			
			uni.request({
				url: getApp().globalData.url + 'api.user/money_list',
				method: 'POST',
				header: {
					token: uni.getStorageSync('token')
				},
				success(res) {
					if(res.data.code==1){
						let money = parseFloat(res.data.data.money)
						let reduce_money = parseFloat(res.data.data.reduce_money)
						let tole_money = parseFloat(res.data.data.tole_money)
						that.list = res.data.data
						that.money = money.toFixed(2)
						that.reduce_money = reduce_money.toFixed(2)
						that.tole_money = tole_money.toFixed(2)
					}else{
						uni.showToast({
							title: res.data.msg,
							icon: 'none'
						})
					}
				}
			})
		},
		methods: {
			// 赠送红包
			redPacket(){
				uni.navigateTo({
					url: '/pages/index/redPacket'
				})
			},
			// 提现
			withdraw(){
				// #ifdef APP
				uni.navigateTo({
					url: '/pages/withdraw/index'
				})
				// #endif
				
				// #ifdef MP-WEIXIN
				uni.navigateTo({
					url: '/pages/withdraw/index'
				})
				// #endif
				// #ifdef H5
				location.href = 'https://fx.mouthwash.top/withdraw/index.html'
				// #endif
			}
		}
	}
</script>

<style lang="scss">
	page{
		padding-bottom: 60rpx;
		background: #F4F4F4;
	}
	// .top{
	// 	margin: 20rpx 24rpx;
	// 	width: 702rpx;
	// 	height: 260rpx;
	// 	background: linear-gradient(135deg, #FEE6CB 0%, #FEF8E9 100%);
	// 	border-radius: 16rpx;
	// 	overflow: hidden;
		
	// 	&_t{
	// 		margin-top: 32rpx;
	// 		height: 50rpx;
	// 		overflow: hidden;
			
	// 		&_num{
	// 			float: left;
	// 			width: 33%;
	// 			height: 50rpx;
	// 			font-size: 36rpx;
	// 			font-family: PingFangSC-Medium, PingFang SC;
	// 			font-weight: 500;
	// 			color: #000000;
	// 			line-height: 50rpx;
	// 			text-align: center;
	// 		}
	// 	}
	// 	&_c{
	// 		margin-top: 24rpx;
	// 		height: 40rpx;
	// 		overflow: hidden;
			
	// 		&_text{
	// 			float: left;
	// 			width: 33%;
	// 			height: 40rpx;
	// 			font-size: 28rpx;
	// 			font-family: PingFangSC-Regular, PingFang SC;
	// 			font-weight: 400;
	// 			color: #6A6A6A;
	// 			line-height: 40rpx;
	// 			text-align: center;
	// 		}
	// 	}
	// 	&_text{
	// 		margin-top: 40rpx;
	// 		height: 40rpx;
	// 		font-size: 28rpx;
	// 		font-family: PingFangSC-Regular, PingFang SC;
	// 		font-weight: 400;
	// 		color: #2962FF;
	// 		line-height: 40rpx;
	// 		text-align: center;
	// 	}
	// 	&_button{
	// 		margin: 28rpx 171rpx;
	// 		width: 360rpx;
	// 		height: 80rpx;
	// 		background: #FFB766;
	// 		border-radius: 39rpx;
	// 		text-align: center;
	// 		font-size: 28rpx;
	// 		font-family: PingFangSC-Medium, PingFang SC;
	// 		font-weight: 500;
	// 		color: #FFFFFF;
	// 		line-height: 80rpx;
	// 	}
	// }
	
	.top{
		position: relative;
		margin: 12rpx 24rpx;
		width: 702rpx;
		height: 246rpx;
		background: linear-gradient(135deg, #EDC66E 0%, #F6E1B0 100%);
		border-radius: 16rpx;
		overflow: hidden;
		
		&_img{
			width: 702rpx;
			height: 246rpx;
		}
		.topbox{
			position: absolute;
			top: 0rpx;
			left: 0rpx;
			width: 702rpx;
			height: 180rpx;
			display: flex;
			flex-direction: row;
			align-items: center;
			overflow: hidden;
			
			&_box{
				flex: 1;
				
				&_t{
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
					font-family: PingFangSC-Regular, PingFang SC;
					font-weight: 400;
					color: #404040;
					line-height: 40rpx;
					text-align: center;
				}
			}
		}
		&_f{
			position: absolute;
			top: 182rpx;
			left: 0rpx;
			width: 702rpx;
			height: 40rpx;
			font-size: 28rpx;
			font-family: PingFangSC-Medium, PingFang SC;
			font-weight: 500;
			color: #000000;
			line-height: 40rpx;
			text-align: center;
		}
	}
	
	.title{
		margin: 40rpx 0rpx 24rpx 28rpx;
		height: 40rpx;
		font-size: 28rpx;
		font-family: PingFangSC-Medium, PingFang SC;
		font-weight: 500;
		color: #333333;
		line-height: 40rpx;
		
		text{
			float: left;
			margin-top: 6rpx;
			margin-right: 8rpx;
			width: 8rpx;
			height: 28rpx;
			background: #CBA55F;
			border-radius: 4rpx;
		}
	}
	
	// 数据
	.boxes{
		padding-bottom: 60rpx;
		margin: 20rpx 24rpx;
		width: 702rpx;
		background: #FFFFFF;
		border-radius: 16rpx;
		overflow: hidden;
		
		&_title{
			margin-top: 20rpx;
			margin-left: 20rpx;
			height: 40rpx;
			font-size: 28rpx;
			font-family: PingFangSC-Medium, PingFang SC;
			font-weight: 500;
			color: #000000;
			line-height: 40rpx;
		}
		&_t{
			margin-top: 24rpx;
			margin-bottom: 20rpx;
			height: 40rpx;
			overflow: hidden;
			
			&_box{
				float: left;
				width: 25%;
				height: 40rpx;
				font-size: 28rpx;
				font-family: PingFangSC-Medium, PingFang SC;
				font-weight: 500;
				color: #000000;
				line-height: 40rpx;
				text-align: center;
			}
		}
		.box{
			height: 114rpx;
			border-top: 2rpx solid #F4F4F4;
			overflow: hidden;
			
			&_item{
				float: left;
				margin-top: 18rpx;
				width: 25%;
				height: 80rpx;
				font-size: 28rpx;
				font-family: PingFangSC-Regular, PingFang SC;
				font-weight: 400;
				color: #000000;
				line-height: 40rpx;
				text-align: center;
				overflow: hidden;
				
				&:nth-child(3){
					line-height: 80rpx;
				}
				&:nth-child(4){
					line-height: 80rpx;
				}
			}
		}
	}
</style>