<template>
<!-- 漱口水收益-->
	<view>
		<view class="top">
			<block v-for="(item,index) in select" :key="index">
				<view class="top_box" :class="[selectIndex==index?'top_boxActive':'']" @click="clickSelect(index)">{{item}}</view>
			</block>
		</view>
		
		<view class="box">
			<view class="box_t">
				<view class="box_t_box">日期</view>
				<view class="box_t_box">{{selectIndex==0?'我的分红':'我的提成'}}</view>
			</view>
			<block v-for="(item,index) in list" :key="index">
				<view class="boxes">
					<view class="boxes_box">{{item.createtime}}</view>
					<view class="boxes_box">{{item.money}}</view>
				</view>
			</block>
		</view>
		
		<!-- <view class="box">
			<block v-for="(item,index) in select" :key="index">	
				<view class="box_box" :style="{color:selectIndex==index?'#FF4553':'#000000'}" @click="clickSelect(index)">{{item}}</view>
			</block>
		</view> -->
		<!-- 数据 -->
		<!-- <view class="boxes">
			<view class="boxes_c">
				<view class="boxes_c_item">日期</view>
				<view class="boxes_c_item">{{selectIndex==0?'我的分红':'我的提成'}}</view>
			</view>
			<block v-for="(item,index) in list" :key="index">
				<view class="boxes_f">
					<view class="boxes_f_item">{{item.createtime}}</view>
					<view class="boxes_f_item">{{item.money}}</view>
				</view>
			</block>
		</view> -->
	</view>
</template>

<script>
	export default {
		data() {
			return {
				select: ['托管分红','我的提成'],
				selectIndex: 0,
				list: [],
				// level: '',
			};
		},
		onShow() {
			let that = this
			
			that.request()
		},
		methods: {
			request(){
				let that = this
				
				uni.request({
					url: getApp().globalData.url + 'api.order/healthy',
					method: 'POST',
					header: {
						token: uni.getStorageSync('token')
					},
					data: {
						type: that.selectIndex + 2
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
			// 点击切换
			clickSelect(index){
				let that = this
				
				if(that.selectIndex==index){
					return false
				}
				that.selectIndex = index
				that.list = []
				that.request()
			}
		}
	}
</script>

<style lang="scss">
	
	page{
		background: #F4F4F4;
	}
	// uni-page-head {
	//   display: none;
	// }
	
	.top{
		position: fixed;
		top: 0rpx;
		left: 0rpx;
		height: 100rpx;
		width: 100%;
		overflow: hidden;
		
		&_box{
			float: left;
			margin-top: 20rpx;
			margin-left: 24rpx;
			width: 336rpx;
			height: 60rpx;
			background: #6A6A6A;
			border-radius: 8rpx;
			text-align: center;
			font-size: 28rpx;
			font-family: PingFangSC-Regular, PingFang SC;
			font-weight: 400;
			color: #FFFFFF;
			line-height: 60rpx;
			
			&:nth-child(2){
				margin-left: 30rpx;
			}
		}
		&_boxActive{
			background: #CBA55F;
		}
	}
	
	.box{
		padding-bottom: 60rpx;
		margin: 120rpx 24rpx;
		width: 702rpx;
		background: #FFFFFF;
		border-radius: 16rpx;
		overflow: hidden;
		
		&_t{
			margin-top: 16rpx;
			width: 702rpx;
			height: 80rpx;
			background: #FFFFFF;
			box-shadow: inset 0rpx -2rpx 0rpx 0rpx #F9F9F9;
			display: flex;
			flex-direction: row;
			align-items: center;
			
			&_box{
				flex: 1;
				height: 80rpx;
				font-size: 28rpx;
				font-family: PingFangSC-Medium, PingFang SC;
				font-weight: 500;
				color: #666666;
				line-height: 80rpx;
				text-align: center;
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
			
			&_box{
				flex: 1;
				height: 80rpx;
				font-size: 28rpx;
				font-family: PingFangSC-Regular, PingFang SC;
				font-weight: 400;
				color: #000000;
				line-height: 80rpx;
				text-align: center;
				
				&:nth-child(2){
					color: #CBA55F;
				}
			}
		}
	}
	
	// 头部
	// .top{
	// 	margin: 20rpx;
	// 	width: 710rpx;
	// 	height: 152rpx;
	// 	background: #FFFFFF;
	// 	border-radius: 16rpx;
	// 	overflow: hidden;
		
	// 	&_l{
	// 		float: left;
	// 		margin-top: 32rpx;
	// 		margin-left: 36rpx;
	// 		height: 88rpx;
	// 		overflow: hidden;
			
	// 		&_img{
	// 			float: left;
	// 			width: 88rpx;
	// 			height: 88rpx;
	// 			border-radius: 50%;
	// 			overflow: hidden;
	// 		}
	// 		&_box{
	// 			float: left;
	// 			margin-left: 12rpx;
	// 			height: 88rpx;
	// 			overflow: hidden;
				
	// 			&_name{
	// 				width: 112rpx;
	// 				height: 40rpx;
	// 				font-size: 28rpx;
	// 				font-family: PingFangSC-Regular, PingFang SC;
	// 				font-weight: 400;
	// 				color: #333333;
	// 				line-height: 40rpx;
	// 				-webkit-text-stroke: 2rpx #333333;
	// 				text-stroke: 2rpx #333333;
	// 				overflow: hidden;
	// 				word-break: break-all;
	// 				text-overflow: ellipsis;
	// 				display: -webkit-box;
	// 				-webkit-box-orient: vertical;
	// 				-webkit-line-clamp: 1;
	// 			}
	// 			&_level{
	// 				margin-top: 8rpx;
	// 				width: 112rpx;
	// 				height: 40rpx;
	// 				font-size: 28rpx;
	// 				font-family: PingFangSC-Regular, PingFang SC;
	// 				font-weight: 400;
	// 				color: #FF4553;
	// 				line-height: 40rpx;
	// 				-webkit-text-stroke: 2rpx #FF4553;
	// 				text-stroke: 2rpx #FF4553;
	// 			}
	// 		}
	// 	}
	// 	&_c{
	// 		float: left;
	// 		margin: 32rpx 20rpx 0rpx;
	// 		width: 202rpx;
	// 		overflow: hidden;
			
	// 		&_t{
	// 			height: 34rpx;
	// 			font-size: 24rpx;
	// 			font-family: PingFangSC-Medium, PingFang SC;
	// 			font-weight: 500;
	// 			color: #FF4553;
	// 			line-height: 34rpx;
	// 			text-align: center;
	// 			overflow: hidden;
				
	// 			&_img{
	// 				display: inline-block;
	// 				margin-top: 6rpx;
	// 				width: 20rpx;
	// 				height: 24rpx;
	// 			}
	// 		}
	// 		&_c{
	// 			width: 202rpx;
	// 			height: 16rpx;
	// 			display: inline-block;
	// 			overflow: hidden;
	// 			background: #999990;
	// 		}
	// 		&_b{
	// 			height: 34rpx;
	// 			font-size: 24rpx;
	// 			font-family: PingFangSC-Medium, PingFang SC;
	// 			font-weight: 500;
	// 			color: #FF4553;
	// 			line-height: 34rpx;
	// 			text-align: center;
	// 		}
	// 	}
	// 	&_r{
	// 		float: left;
	// 		margin-top: 32rpx;
	// 		height: 88rpx;
	// 		overflow: hidden;
			
	// 		&_img{
	// 			float: left;
	// 			width: 88rpx;
	// 			height: 88rpx;
	// 			overflow: hidden;
	// 		}
	// 		&_level{
	// 			float: left;
	// 			margin-left: 12rpx;
	// 			height: 88rpx;
	// 			font-size: 28rpx;
	// 			font-family: PingFangSC-Regular, PingFang SC;
	// 			font-weight: 400;
	// 			color: #FF4553;
	// 			line-height: 88rpx;
	// 			-webkit-text-stroke: 2rpx #FF4553;
	// 			text-stroke: 2rpx #FF4553;
	// 		}
	// 	}
	// }
	
	// .box{
	// 	margin: 20rpx;
	// 	width: 710rpx;
	// 	height: 80rpx;
	// 	background: #FFFFFF;
	// 	border-radius: 16rpx;
	// 	overflow: hidden;
		
	// 	&_box{
	// 		float: left;
	// 		width: 50%;
	// 		height: 80rpx;
	// 		font-size: 28rpx;
	// 		font-family: PingFangSC-Medium, PingFang SC;
	// 		font-weight: 500;
	// 		line-height: 80rpx;
	// 		text-align: center;
	// 	}
	// }
	
	// 数据
	// .boxes{
	// 	padding-bottom: 20rpx;
	// 	margin: 20rpx;
	// 	background: #FFFFFF;
	// 	border-radius: 16rpx;
	// 	overflow: hidden;
		
	// 	&_t{
	// 		margin-top: 32rpx;
	// 		height: 50rpx;
	// 		overflow-y: hidden;
			
	// 		&_box{
	// 			float: left;
	// 			margin: 0rpx 30rpx;
	// 			height: 50rpx;
	// 			font-size: 28rpx;
	// 			font-family: PingFangSC-Regular, PingFang SC;
	// 			font-weight: 400;
	// 			color: #000000;
	// 			line-height: 50rpx;
	// 			box-sizing: border-box;
	// 			overflow: hidden;
	// 		}
	// 		&_active{
	// 			color: #FF4553;
	// 			border-bottom: 4rpx solid #FF4553;
	// 		}
	// 	}
	// 	&_c{
	// 		margin-top: 36rpx;
	// 		margin-bottom: 20rpx;
	// 		width: 710rpx;
	// 		height: 40rpx;
	// 		overflow: hidden;
			
	// 		&_item{
	// 			float: left;
	// 			width: 50%;
	// 			height: 40rpx;
	// 			font-size: 28rpx;
	// 			font-family: PingFangSC-Medium, PingFang SC;
	// 			font-weight: 500;
	// 			color: #999999;
	// 			line-height: 40rpx;
	// 			text-align: center;
	// 		}
	// 	}
	// 	&_f{
	// 		width: 710rpx;
	// 		height: 80rpx;
	// 		border-top: 2rpx solid #F4F4F4;
	// 		overflow: hidden;
			
	// 		&_item{
	// 			float: left;
	// 			width: 50%;
	// 			height: 80rpx;
	// 			font-size: 28rpx;
	// 			font-family: PingFangSC-Regular, PingFang SC;
	// 			font-weight: 400;
	// 			color: #000000;
	// 			line-height: 80rpx;
	// 			text-align: center;
	// 		}
	// 	}
	// }
</style>
