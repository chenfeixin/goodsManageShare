<template>
<!-- 我的VIP卡 -->
	<view>
		<!-- <view class="box" v-for="(item,index) in list" :key="index" @click="agreement(item.order_id)">
			<view class="box_t">
				订单号：{{item.order_sn}}<text>购卡成功</text>
			</view>	
			<view class="box_c">
				购卡数量：{{item.num}} <text>支付金额：{{item.payment}}</text>
			</view>
			<view class="box_time">支付时间：{{item.paymenttime}}</view>	
			<view class="box_f">
				合作协议： <text>《合作协议书》</text>
				<text style="float: right;font-size: 24rpx;" v-if="item.status==1">立即签署></text>
				<text style="float: right;font-size: 24rpx;" v-else>已签署</text>
			</view>
		</view> -->
		
		<view class="box" v-for="(item,index) in list" :key="index" @click="agreement(item.order_id)">
			<view class="box_t">订单号：{{item.order_sn}}<text>购卡成功</text></view>
			<view class="box_c">购卡数量：<text>{{item.num}}</text> </view>
			<view class="box_c">支付金额：<text>{{item.payment}}</text> </view>
			<view class="box_c">支付时间：<text>{{item.paymenttime}}</text> </view>
			<view class="box_f">
				合作协议： <text>《合作协议书》</text>
				<text style="float: right;font-size: 24rpx;" v-if="item.status==1">立即签署></text>
				<text style="float: right;font-size: 24rpx;" v-else>已签署</text>
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
				url: getApp().globalData.url + 'api.user/contractlist',
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
		},
		methods : {
			// 合同
			agreement(id){
				uni.navigateTo({
					url: '/pages/my/agreement?id='+id
				})
			},
			// 签名
			signature(id){
				uni.navigateTo({
					url: '/pages/my/signature?id='+id
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
		margin: 24rpx;
		width: 702rpx;
		height: 344rpx;
		background: #FFFFFF;
		border-radius: 16rpx;
		overflow: hidden;
		
		&_t{
			margin: 24rpx 28rpx;
			height: 40rpx;
			font-size: 24rpx;
			font-family: PingFangSC-Medium, PingFang SC;
			font-weight: 500;
			color: #666666;
			line-height: 40rpx;
			
			text{
				float: right;
				font-size: 28rpx;
				color: #EEC873;
			}
		}
		&_c{
			margin: 24rpx 28rpx;
			height: 40rpx;
			font-size: 28rpx;
			font-family: PingFangSC-Medium, PingFang SC;
			font-weight: 500;
			color: #666666;
			line-height: 40rpx;
			
			text{
				margin-left: 12rpx;
				color: #333333;
			}
		}
		&_f{
			margin: 24rpx 28rpx;
			height: 40rpx;
			font-size: 28rpx;
			font-family: PingFangSC-Medium, PingFang SC;
			font-weight: 500;
			color: #666666;
			line-height: 40rpx;
			
			text{
				margin-left: 12rpx;
				color: #EEC873;
			}
		}
	}
	
</style>
