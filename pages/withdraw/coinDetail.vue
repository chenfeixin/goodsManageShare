<template>
<!-- 健康币明细 -->
	<view>
		<customHead mode='deNav' title='健康币明细' :isReturn='true'></customHead>
		<view class="top">
			<view class="top_box">
				<picker mode="date" :value="date" @change="bindDateChange">
					<view class="top_box_text">{{date}}</view>
				</picker>
				<image src="/static/down.png" class="top_box_img"></image>
			</view>
			<view class="top_box" style="margin-left: 30rpx;" @click="selectType">
				<view class="top_box_text">{{typeName}}</view>
				<image src="/static/down.png" class="top_box_img"></image>
			</view>
		</view>
		<view class="boxes">
			<view class="boxes_t">
				<view class="boxes_t_box">时间</view>
				<view class="boxes_t_box">类型</view>
				<view class="boxes_t_box">获得现金</view>
			</view>
			<block v-for="(item,index) in list.list" :key="index">
				<view class="box">
					<view class="box_item"><text>{{item.cattime}}</text></view>
					<view class="box_item">
<!-- 						<text v-if="item.type==1">VIP卡返利</text>
						<text v-if="item.type==2">转积分</text>
						<text v-if="item.type==3">{{item.status==1?'提现失败退还':'健康币提现'}}</text>
						<text v-if="item.type==5">漱口水奖励</text>
						<text v-if="item.type==7">购买VIP卡</text>
						<text v-if="item.type==10">充值</text> -->
						<text v-if="item.type==1">VIP卡推荐返利</text>
						<text v-else-if="item.type==2">现金转积分</text>
						<text v-else-if="item.type==3">现金提现</text>
						<text v-else-if="item.type==4">职位奖励</text>
						<text v-else-if="item.type==5">漱口水职位奖励</text>
						<text v-else-if="item.type==6">领取现金红包</text>
						<text v-else-if="item.type==7">购买VIP卡</text>
						<text v-else-if="item.type==8">退还红包</text>
						<text v-else-if="item.type==9">赠送好友</text>
						<text v-else-if="item.type==10">充值</text>
						<text v-else-if="item.type==11">股东福利1</text>
						<text v-else-if="item.type==12">股东福利2</text>
					</view>
					<view class="box_item" style="line-height: 80rpx;">{{item.status==1?'+':'-'}}{{item.money}}</view>
				</view>
			</block>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				list: [],
				date: '',
				type: 0,
				typeList: ['全部','充值','转积分','提现','会员卡返利','漱口水奖励'],
				typeName: '全部类型',
			};
		},
		onShow() {
			let that = this
			
			let date = new Date();
			let year = date.getFullYear();
			// 在日期格式中，月份是从0开始的，因此要加0，使用三元表达式在小于10的前面加0，以达到格式统一  如 09:11:05
			let month = date.getMonth() + 1 < 10 ? "0" + (date.getMonth() + 1) : date.getMonth() + 1;
			let day = date.getDate() < 10 ? "0" + date.getDate() : date.getDate();
			that.date = year + '-' + month + '-' + day
			
			that.request()
		},
		methods: {
			request(){
				let that = this
				
				uni.request({
					url: getApp().globalData.url + 'api.user/money_list',
					method: 'POST',
					header: {
						token: uni.getStorageSync('token')
					},
					data: {
						date: that.date,
						type: that.type
					},
					success(res) {
						if(res.data.code==1){
							that.list = res.data.data
						}
					}
				})
			},
			// 选择时间
			bindDateChange(e){
				let that = this
				if(that.date == e.detail.value){
					return false
				}
				console.log(e.detail.value)
				that.date = e.detail.value
				that.request()
			},
			// 选择类型
			selectType(){
				let that = this
				uni.showActionSheet({
					itemList: that.typeList,
					success: function (res) {
						that.typeName = that.typeList[res.tapIndex]
						that.type = res.tapIndex
						if(that.type==1){
							that.type=10
						}
						that.request()
						// console.log('选中了第' + (res.tapIndex + 1) + '个按钮');
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
	.top{
		height: 96rpx;
		overflow: hidden;
		background: #F4F4F4;
		z-index: 2;
		
		&_box{
			position: relative;
			float: left;
			margin-top: 16rpx;
			margin-left: 24rpx;
			width: 336rpx;
			height: 60rpx;
			background: #FF4553;
			border-radius: 8rpx;
			
			&_text{
				float: left;
				width: 336rpx;
				height: 60rpx;
				font-size: 28rpx;
				font-family: PingFangSC-Regular, PingFang SC;
				font-weight: 400;
				color: #FFFFFF;
				line-height: 60rpx;
				text-align: center;
			}
			&_img{
				position: absolute;
				top: 24rpx;
				right: 50rpx;
				width: 20rpx;
				height: 18rpx;
				overflow: hidden;
			}
		}
	}
	
	.boxes{
		margin: 20rpx 24rpx;
		width: 702rpx;
		background: #FFFFFF;
		border-radius: 16rpx;
		overflow: hidden;
		
		&_t{
			margin-top: 24rpx;
			height: 84rpx;
			border-bottom: 2rpx solid #F4F4F4;
			overflow: hidden;
			
			&_box{
				float: left;
				width: 234rpx;
				height: 84rpx;
				font-size: 28rpx;
				font-family: PingFangSC-Medium, PingFang SC;
				font-weight: 500;
				color: #000000;
				line-height: 84rpx;
				text-align: center;
			}
		}
		.box{
			height: 114rpx;
			border-bottom: 2rpx solid #F4F4F4;
			overflow: hidden;
			
			&_item{
				float: left;
				margin-top: 18rpx;
				width: 234rpx;
				height: 80rpx;
				font-size: 28rpx;
				font-family: PingFangSC-Regular, PingFang SC;
				font-weight: 400;
				color: #000000;
				line-height: 40rpx;
				text-align: center;
				overflow: hidden;
				word-break: break-all;
				text-overflow: ellipsis;
				display: -webkit-box;
				-webkit-box-orient: vertical;
				-webkit-line-clamp: 2;
			}
		}
	}
</style>
