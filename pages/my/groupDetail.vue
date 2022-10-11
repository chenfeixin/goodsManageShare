<template>
	<!-- 团队详情 -->
	<view>
		<view class="box">
			<view class="top">
				<view class="top_box" v-for="(item,index) in topList" :key="index">{{item}}</view>
			</view>
			<block v-for="(item,index) in list" :key="index">
				<view class="boxes" @click="callingCard(item.id)">
					<view class="boxes_box">{{item.date}}</view>
					<view class="boxes_box">{{item.nickname}}</view>
					<view class="boxes_box">{{item.num}}</view>
					<view class="boxes_box" style="color: #6236FF;">查看</view>
				</view>
			</block>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				topList: ['日期','名字','数量','详情'],
				list: [],
			};
		},
		
		onShow() {
			let that = this
			
			uni.request({
				url: getApp().globalData.url + 'api.order/healthy',
				method: 'POST',
				header: {
					token: uni.getStorageSync('token')
				},
				data: {
					type: 6,
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
		
		methods: {
			// 名片
			callingCard(id){
				uni.navigateTo({
					url: '/pages/my/callingCard?id='+id
				})
			},
		}
	}
</script>

<style lang="scss">
	page{
		background: #F9F9F9;
	}
	.box{
		padding-bottom: 80rpx;
		margin: 24rpx;
		width: 702rpx;
		background: #FFFFFF;
		border-radius: 16rpx;
		overflow: hidden;
		
		.top{
			margin-top: 16rpx;
			width: 702rpx;
			height: 80rpx;
			background: #FFFFFF;
			box-shadow: inset 0rpx -2rpx 0rpx 0rpx #F9F9F9;
			display: flex;
			flex-direction: row;
			align-items: center;
			overflow: hidden;
			
			&_box{
				flex: 1;
				height: 80rpx;
				font-size: 28rpx;
				font-family: PingFangSC-Medium, PingFang SC;
				font-weight: 500;
				color: #666666;
				line-height: 80rpx;
				text-align: center;
				overflow: hidden;
				word-break: break-all;
				text-overflow: ellipsis;
				display: -webkit-box;
				-webkit-box-orient: vertical;
				-webkit-line-clamp: 1;
			}
		}
		.boxes{
			width: 702rpx;
			height: 80rpx;
			background: #FFFFFF;
			box-shadow: inset 0rpx -2rpx 0rpx 0rpx #F9F9F9;
			display: flex;
			flex-direction: row;
			align-items: center;
			overflow: hidden;
			
			&_box{
				flex: 1;
				height: 80rpx;
				font-size: 28rpx;
				font-family: PingFangSC-Regular, PingFang SC;
				font-weight: 400;
				color: #000000;
				line-height: 80rpx;
				text-align: center;
				overflow: hidden;
				word-break: break-all;
				text-overflow: ellipsis;
				display: -webkit-box;
				-webkit-box-orient: vertical;
				-webkit-line-clamp: 1;
			}
		}
	}
</style>
