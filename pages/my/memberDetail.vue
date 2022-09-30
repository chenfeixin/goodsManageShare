<template>
	<view>
		<view class="box">
			<view class="box_time">时间: <text>{{time}}</text></view>
			<view class="box_t">
				<view class="box_t_box" :style="{width: category==1?'25%' : '33%'}" v-for="(item,index) in topList" :key="index">{{item}}</view>
			</view>
			<view class="box_f" v-for="(item,index) in list" :key="index" @click="callingCard(item.uid)">
				<view class="box_f_text" :style="{width: category==1?'25%' : '33%'}">{{item.nickname}}</view>
				<view class="box_f_text" :style="{width: category==1?'25%' : '33%'}">{{item.num}}</view>
				<view class="box_f_text" v-if="category==1" style="color: #CBA55F;">{{item.currency}}</view>
				<view class="box_f_text" :style="{width: category==1?'25%' : '33%'}" style="color: #6236FF;">查看</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				topList: ['名字','购卡数量','我的奖励','详情'],
				type: '', //类型，0每日，1每月
				time: '', //时间
				list: [],
				title: '',
				category: '',
			};
		},
		
		onLoad(options) {
			let that = this
			
			if(options.topIndex==0){
				that.title = '每日详情'
			}else{
				that.title = '每月详情'
			}
			
			uni.setNavigationBarTitle({
				title: that.title
			});
			
			that.time = options.time
			that.type = options.topIndex*1+4
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
					type: that.type,
					date: that.time
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
				url: getApp().globalData.url + 'api.user/index',
				method: 'GET',
				header: {
					token: uni.getStorageSync('token')
				},
				success(res) {
					if(res.data.code==1){
						that.category = res.data.data.category
						if(that.category==1){
							that.topList = ['名字','购卡数量','我的奖励','详情']
						}else{
							that.topList = ['名字','购卡数量','详情']
						}
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
			}
		}
	}
</script>

<style lang="scss">
	page{
		background: #F4F4F4;
	}
	.box{
		padding-bottom: 30rpx;
		margin: 20rpx;
		width: 710rpx;
		background: #FFFFFF;
		border-radius: 16rpx;
		overflow: hidden;
		
		&_time{
			margin: 20rpx;
			height: 50rpx;
			font-size: 28rpx;
			font-family: PingFangSC-Regular, PingFang SC;
			font-weight: 400;
			color: #CBA55F;
			line-height: 50rpx;
			
			text{
				margin-left: 32rpx;
			}
		}
		&_t{
			margin-top: 32rpx;
			height: 80rpx;
			border-bottom: 1rpx solid #CCCCCC;
			overflow: hidden;
			
			&_box{
				float: left;
				width: 25%;
				height: 80rpx;
				font-size: 28rpx;
				font-family: PingFangSC-Regular, PingFang SC;
				font-weight: 400;
				color: #000000;
				line-height: 80rpx;
				text-align: center;
			}
		}
		&_f{
			margin-top: 12rpx;
			overflow: hidden;
			
			&_text{
				float: left;
				width: 25%;
				height: 50rpx;
				font-size: 28rpx;
				font-family: PingFangSC-Regular, PingFang SC;
				font-weight: 400;
				color: #000000;
				line-height: 50rpx;
				text-align: center;
			}
		}
	}
</style>
