<template>
<!-- 使用记录 -->
	<view>
		<view class="boxes">
			<view class="boxes_t">
				<view class="boxes_t_box">时间</view>
				<view class="boxes_t_box">金额</view>
				<!-- <view class="boxes_t_box">税费</view> -->
				<view class="boxes_t_box">状态</view>
				<!-- <view class="boxes_t_box">类型</view> -->
			</view>
			<view class="boxes_c">
				<block v-for="(item,index) in list" :key="index">
					<view class="box">
						<view class="box_item">{{item.cattime}}</view>
						<view class="box_item" style="color: #EEC873;">￥{{item.apply}}</view>
						<!-- <view class="box_item">￥0.23</view> -->
						<view class="box_item" v-if="item.status==1">申请中</view>
						<view class="box_item" v-if="item.status==2">已打款</view>
						<view class="box_item" v-if="item.status==3">申请拒绝</view>
						<!-- <view class="box_item">提现</view> -->
					</view>
				</block>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				list: [],
			};
		},
		onShow() {
			let that = this
			
			uni.request({
				url: getApp().globalData.url + 'api.user/zhuan_list',
				method: 'POST',
				header: {
					token: uni.getStorageSync('token')
				},
				data: {
					type: 3
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
		}
	}
</script>

<style lang="scss">
	.boxes{
		padding-bottom: 60rpx;
		margin: 40rpx 20rpx;
		width: 710rpx;
		background: #F9F9F9;
		border-radius: 8rpx;
		overflow: hidden;
		
		&_t{
			margin-top: 24rpx;
			height: 40rpx;
			overflow: hidden;
			
			&_box{
				float: left;
				width: 33%;
				height: 40rpx;
				font-size: 28rpx;
				font-family: PingFangSC-Medium, PingFang SC;
				font-weight: 500;
				color: #333333;
				line-height: 40rpx;
				text-align: center;
				
				// &:nth-child(1){
				// 	width: 23%;
				// }
				// &:nth-child(2){
				// 	width: 26%;
				// }
			}
		}
		&_c{
			margin-top: 16rpx;
			border-top: 2rpx solid #FFFFFF;
			overflow: hidden;
			
			.box{
				height: 74rpx;
				border-bottom: 2rpx solid #FFFFFF;
				overflow: hidden;
				
				&_item{
					float: left;
					width: 33%;
					height: 74rpx;
					font-size: 28rpx;
					font-family: PingFangSC-Regular, PingFang SC;
					font-weight: 400;
					color: #333333;
					line-height: 74rpx;
					text-align: center;
					overflow: hidden;
					// word-break: break-all;
					// text-overflow: ellipsis;
					// display: -webkit-box;
					// -webkit-box-orient: vertical;
					// -webkit-line-clamp: 1;
					

					// &:nth-child(2){
					// 	width: 26%;
					// }
				}
			}
		}
	}
</style>
