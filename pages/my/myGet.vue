<template>
<!-- 漱口水领取详情 -->
	<view>
		<view class="boxes">
			<view class="boxes_box">
				<view class="boxes_box_t">{{lists.receive_cont>99999999?'无限领取':lists.receive_cont}}</view>
				<!-- <view class="boxes_box_t">无限领取</view> -->
				<view class="boxes_box_t">{{lists.receive_use}}</view>
			</view>
			<view class="boxes_box">
				<view class="boxes_box_b">可领取数量（颗）</view>
				<view class="boxes_box_b">已领取数量（颗）</view>
			</view>
		</view>
		<view class="content">
			<view class="content_title">漱口水领取详情</view>
			<view class="box">
				<view class="box_t">时间</view>
				<view class="box_t">领取数</view>
			</view>
			<view class="box" v-for="(item,index) in list" :key="index">
				<view class="box_item">{{item.cattime}}</view>
				<view class="box_item" style="color: #CBA55F;">{{item.num}}颗</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				list: [],
				lists: '',
			};
		},
		onShow() {
			let that = this
			
			uni.request({
				url: getApp().globalData.url + 'api.user/lingqu',
				method: 'POST',
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
			
			uni.request({
				url: getApp().globalData.url + 'api.user/lingift',
				method: 'POST',
				header: {
					token: uni.getStorageSync('token')
				},
				success(res) {
					if(res.data.code==1){
						that.lists = res.data.data
					}else{
						uni.showToast({
							title: res.data.msg,
							icon: 'none'
						})
					}
				}
			})
		}
	}
</script>

<style lang="scss">
	page{
		background: #F4F4F4;
	}
	.boxes{
		margin: 32rpx 24rpx;
		width: 702rpx;
		height: 174rpx;
		border-radius: 16rpx;
		background: #FFFFFF;
		overflow: hidden;
		
		&_box{
			overflow: hidden;
			
			&_t{
				float: left;
				margin-top: 36rpx;
				width: 50%;
				height: 50rpx;
				font-size: 36rpx;
				font-family: PingFangSC-Regular, PingFang SC;
				font-weight: 400;
				color: #CBA55F;
				line-height: 50rpx;
				text-align: center;
			}
			&_b{
				float: left;
				margin-top: 16rpx;
				width: 50%;
				height: 40rpx;
				font-size: 28rpx;
				font-family: PingFangSC-Regular, PingFang SC;
				font-weight: 400;
				color: #999999;
				line-height: 40rpx;
				text-align: center;
			}
		}
	}
	
	.content{
		margin: 32rpx 24rpx;
		width: 710rpx;
		background: #FFFFFF;
		border-radius: 16rpx;
		overflow: hidden;
		
		&_title{
			margin: 32rpx 20rpx;
			height: 40rpx;
			font-size: 28rpx;
			font-family: PingFangSC-Regular, PingFang SC;
			font-weight: 400;
			color: #000000;
			line-height: 40rpx;
			overflow: hidden;
		}
		.box{
			height: 80rpx;
			border-bottom: 2rpx solid #F4F4F4;
			overflow: hidden;
			
			&_t{
				float: left;
				width: 50%;
				height: 80rpx;
				font-size: 28rpx;
				font-family: PingFangSC-Medium, PingFang SC;
				font-weight: 500;
				color: #999999;
				line-height: 80rpx;
				text-align: center;
			}
			&_item{
				float: left;
				width: 50%;
				height: 80rpx;
				font-size: 28rpx;
				font-family: PingFangSC-Regular, PingFang SC;
				font-weight: 400;
				color: #000000;
				line-height: 80rpx;
				text-align: center;
			}
		}
	}
</style>
