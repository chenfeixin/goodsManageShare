<template>
<!-- 会员卡数据 推广收益-->
	<view>
		<!-- 数据 -->
		<view class="top">
			<block v-for="(item,index) in topList" :key="index">
				<view class="top_box" :class="[topIndex==index?'top_boxActive':'']" @click="clickTop(index)">{{item}}</view>
			</block>
		</view>
		
		<view class="box">
			<block v-if="topIndex==0">
				<view class="box_t">
					<view class="box_t_box">日期</view>
					<view class="box_t_box">数量</view>
					<view class="box_t_box" v-if="category==1">奖励</view>
					<view class="box_t_box">详情</view>
				</view>
				<block v-for="(item,index) in list" :key="index">
					<view class="boxes" @click="memberDetail(topIndex,item.createtime)">
						<view class="boxes_box">{{item.createtime}}</view>
						<view class="boxes_box">{{item.num}}</view>
						<!-- <view class="boxes_box" style="color: #CBA55F" v-if="category==1">{{item.currency}}</view> -->
						<view class="boxes_box" style="color: #CBA55F">{{item.currency}}</view>
						<view class="boxes_box" style="color: #0091FF">查看</view>
					</view>
				</block>
			</block>
			
			<block v-if="topIndex==1">
				<view class="box_t">
					<view class="box_t_box">月份</view>
					<view class="box_t_box">数量</view>
					<!-- <view class="box_t_box" v-if="category==1">奖励</view> -->
					<view class="box_t_box">奖励</view>
					<view class="box_t_box">详情</view>
				</view>
				<block v-for="(item,index) in list" :key="index">
					<view class="boxes" @click="memberDetail(topIndex,item.createtime)">
						<view class="boxes_box">{{item.createtime}}</view>
						<view class="boxes_box">{{item.num}}</view>
						<!-- <view class="boxes_box" style="color: #CBA55F" v-if="category==1">{{item.currency}}</view> -->
						<view class="boxes_box" style="color: #CBA55F">{{item.currency}}</view>
						<view class="boxes_box" style="color: #0091FF">查看</view>
					</view>
				</block>
			</block>
		</view>
		<!-- <view class="boxes">
			<view class="boxes_t">
				<block v-for="(item,index) in topList" :key="index">
					<view class="boxes_t_box" :class="[topIndex==index?'boxes_t_active':'']" @click="clickTop(index)">
						{{item}}
					</view>
				</block>
			</view>
			
			<block v-if="topIndex==0">
				<view class="box">
					<view class="box_t">
						<view class="box_t_boxes" :style="{width: category==1?'25%' : '33%'}">日期</view>
						<view class="box_t_boxes" :style="{width: category==1?'25%' : '33%'}">数量</view>
						<view class="box_t_boxes" v-if="category==1">奖励</view>
						<view class="box_t_boxes" :style="{width: category==1?'25%' : '33%'}">详情</view>
					</view>
					<block v-for="(item,index) in list" :key="index">
						<view class="box_c" @click="memberDetail(topIndex,item.createtime)">
							<view class="box_c_item_box" :style="{width: category==1?'25%' : '33%'}">{{item.createtime}}</view>
							<view class="box_c_item_box" :style="{width: category==1?'25%' : '33%'}">{{item.num}}</view>
							<view class="box_c_item_box" v-if="category==1">{{item.currency}}</view>
							<view class="box_c_item_box" :style="{width: category==1?'25%' : '33%'}" style="color: #6236FF;">查看</view>
						</view>
					</block>
				</view>
			</block>
			<block v-if="topIndex==1">
				<view class="box">
					<view class="box_t">
						<view class="box_t_box" :style="{width: category==1?'25%' : '33%'}">月份</view>
						<view class="box_t_box" :style="{width: category==1?'25%' : '33%'}">数量</view>
						<view class="box_t_box" v-if="category==1">奖励</view>
						<view class="box_t_box" :style="{width: category==1?'25%' : '33%'}">详情</view>
					</view>
					<block v-for="(item,index) in list" :key="index">
						<view class="box_c" @click="memberDetail(topIndex,item.createtime)">
							<view class="box_c_item" :style="{width: category==1?'25%' : '33%'}">{{item.createtime}}</view>
							<view class="box_c_item" :style="{width: category==1?'25%' : '33%'}">{{item.num}}</view>
							<view class="box_c_item" v-if="category==1">{{item.currency}}</view>
							<view class="box_c_item" :style="{width: category==1?'25%' : '33%'}" style="color: #6236FF;">查看</view>
						</view>
					</block>
				</view>
			</block>
			<block v-if="topIndex==2">
				<view class="box">
					<view class="box_t">
						<view class="box_t_box">日期</view>
						<view class="box_t_box">名字</view>
						<view class="box_t_box">数量</view>
						<view class="box_t_box">详情</view>
					</view>
					<block v-for="(item,index) in list" :key="index">
						<view class="box_c" @click="callingCard(item.id)">
							<view class="box_c_item">{{item.date}}</view>
							<view class="box_c_item">{{item.nickname}}</view>
							<view class="box_c_item">{{item.num}}</view>
							<view class="box_c_item" style="color: #6236FF;">查看</view>
						</view>
					</block>
				</view>
			</block>
		</view>
		<service></service> -->
	</view>
</template>

<script>
	export default {
		data() {
			return {
				topList: ['每日详情','每月详情'],
				topIndex: 0,
				list: [],
				category: '', //1=代理, 2=员工， 3 = 合伙人
			};
		},
		onLoad(options) {
			let that = this
			
			if(options.topIndex){
				that.topIndex = 2
			}
		},
		onShow() {
			let that = this
			
			that.request()
			
			
			uni.request({
				url: getApp().globalData.url + 'api.user/index',
				method: 'GET',
				header: {
					token: uni.getStorageSync('token')
				},
				success(res) {
					if(res.data.code==1){
						that.category = res.data.data.category
					}
				}
			})
			
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
						type: that.topIndex==2?6:that.topIndex,
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
			
			// 每日/每月详情
			memberDetail(topIndex,time){
				uni.navigateTo({
					url: '/pages/my/memberDetail?topIndex='+topIndex+'&time='+time
				})
			},
			
			requestMyInvite(){
				let that = this
				
				uni.request({
					url: getApp().globalData.url + 'api.user/invitation',
					method: 'POST',
					header: {
						token: uni.getStorageSync('token')
					},
					data: {
						type: that.topIndex - 1
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
			
			// 等级详情
			levelDetail(level){
				uni.navigateTo({
					url: '/pages/my/levelDetail?level='+level
				})
			},
			
			// 点击头部
			clickTop(index){
				let that = this
				
				if(that.topIndex == index){
					return false
				}
				
				that.topIndex = index
				that.list = []
				that.request()
			},
			// 邀请明细
			inviteDetail(date){
				uni.navigateTo({
					url: '/pages/my/inviteDetail?date=' + date
				})
			},
			// 购卡vip详情
			buyVipDetail(date){
				uni.navigateTo({
					url: '/pages/my/buyVipDetail?date='+date
				})
			},
			// TA的邀请
			heInvite(index){
				let that = this
				
				let level = that.list.list[index].level
				let levels = that.list.list[index].levels
				let name = that.list.list[index].nickname
				let phone = that.list.list[index].mobile
				let id = that.list.list[index].id
				uni.navigateTo({
					url: '/pages/my/heInvite?level=' + level + '&name=' + name + '&phone=' + phone + '&id=' + id + '&levels=' + levels
				})
			},
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
		background: #F9F9F9;
	}
	
	.top{
		position: fixed;
		top: 0rpx;
		left: 0rpx;
		width: 750rpx;
		height: 100rpx;
		background: #F9F9F9;
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
			
			&:nth-child(2) {
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
			display: flex;
			flex-direction: row;
			align-items: center;
			box-shadow: inset 0rpx -2rpx 0rpx 0rpx #F9F9F9;
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
			}
		}
		.boxes{
			width: 702rpx;
			height: 80rpx;
			background: #FFFFFF;
			display: flex;
			flex-direction: row;
			align-items: center;
			box-shadow: inset 0rpx -2rpx 0rpx 0rpx #F9F9F9;
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
			}
		}
	}
	
	// // 头部
	// .top{
	// 	position: relative;
	// 	margin: 20rpx;
	// 	width: 710rpx;
	// 	height: 400rpx;
	// 	overflow: hidden;
		
	// 	&_img{
	// 		width: 710rpx;
	// 		height: 400rpx;
	// 		display: block;
	// 	}
	// 	.topbox{
	// 		position: absolute;
	// 		top: 0rpx;
	// 		left: 0rpx;
	// 		width: 710rpx;
	// 		height: 400rpx;
	// 		overflow: hidden;
			
	// 		&_box{
	// 			margin-top: 36rpx;
	// 			height: 90rpx;
	// 			overflow: hidden;
				
	// 			&_item{
	// 				float: left;
	// 				width: 33.3%;
	// 				overflow: hidden;
					
	// 				&_t{
	// 					height: 44rpx;
	// 					font-size: 32rpx;
	// 					font-family: PingFangSC-Medium, PingFang SC;
	// 					font-weight: 500;
	// 					color: #FFFFFF;
	// 					line-height: 44rpx;
	// 					text-align: center;
	// 				}
	// 				&_f{
	// 					margin-top: 12rpx;
	// 					height: 34rpx;
	// 					font-size: 24rpx;
	// 					font-family: PingFangSC-Regular, PingFang SC;
	// 					font-weight: 400;
	// 					color: #FFFFFF;
	// 					line-height: 34rpx;
	// 					text-align: center;
	// 				}
	// 			}
	// 			&_text{
	// 				margin-top: 36rpx;
	// 				margin-left: 36rpx;
	// 				height: 34rpx;
	// 				font-size: 24rpx;
	// 				font-family: PingFangSC-Regular, PingFang SC;
	// 				font-weight: 400;
	// 				color: #FFFFFF;
	// 				line-height: 34rpx;
	// 			}
	// 		}
	// 	}
	// }
	
	// // 升级区域
	// .content{
	// 	margin: 20rpx;
	// 	width: 710rpx;
	// 	height: 228rpx;
	// 	background: #FFFFFF;
	// 	border-radius: 16rpx;
	// 	overflow: hidden;
	// }
	
	// // 数据
	// .boxes{
	// 	padding-bottom: 30rpx;
	// 	margin: 20rpx;
	// 	width: 710rpx;
	// 	background: #FFFFFF;
	// 	border-radius: 16rpx;
	// 	overflow: hidden;
		
	// 	&_t{
	// 		margin-top: 32rpx;
	// 		height: 50rpx;
	// 		overflow: hidden;
			
	// 		&_box{
	// 			float: left;
	// 			margin: 0rpx 20rpx;
	// 			height: 50rpx;
	// 			font-size: 28rpx;
	// 			font-family: PingFangSC-Regular, PingFang SC;
	// 			font-weight: 400;
	// 			color: #000000;
	// 			line-height: 40rpx;
	// 			box-sizing: border-box;
	// 			overflow: hidden;
	// 		}
	// 		&_active{
	// 			font-size: 28rpx;
	// 			font-family: PingFangSC-Medium, PingFang SC;
	// 			font-weight: 500;
	// 			color: #FF4553;
	// 			border-bottom: 4rpx solid #FF4553;
	// 		}
	// 	}
		
	// 	.box{
	// 		margin-top: 32rpx;
	// 		overflow: hidden;
			
	// 		&_t{
	// 			margin-bottom: 20rpx;
	// 			height: 40rpx;
	// 			overflow: hidden;
				
	// 			&_box{
	// 				float: left;
	// 				width: 15%;
	// 				height: 40rpx;
	// 				font-size: 28rpx;
	// 				font-family: PingFangSC-Medium, PingFang SC;
	// 				font-weight: 500;
	// 				color: #999999;
	// 				line-height: 40rpx;
	// 				text-align: center;
					
	// 				&:nth-child(1){
	// 					width: 25%;
	// 				}
	// 				&:nth-child(2){
	// 					width: 25%;
	// 				}
	// 				&:nth-child(3){
	// 					width: 25%;
	// 				}
	// 				&:nth-child(4){
	// 					width: 25%;
	// 				}
	// 				&:nth-child(5){
	// 					width: 11%;
	// 				}
					
	// 				&_hint{
	// 					height: 80rpx;
	// 					font-size: 28rpx;
	// 					font-family: PingFangSC-Medium, PingFang SC;
	// 					font-weight: 500;
	// 					color: #999999;
	// 					line-height: 80rpx;
	// 					text-align: center;
	// 				}
	// 			}
	// 			&_boxes{
	// 				float: left;
	// 				width: 25%;
	// 				height: 40rpx;
	// 				font-size: 28rpx;
	// 				font-family: PingFangSC-Medium, PingFang SC;
	// 				font-weight: 500;
	// 				color: #999999;
	// 				line-height: 40rpx;
	// 				text-align: center;
	// 			}
	// 		}
	// 		&_c{
	// 			height: 80rpx;
	// 			border-top: 2rpx solid #F4F4F4;
	// 			overflow: hidden;
				
	// 			&_item{
	// 				float: left;
	// 				height: 80rpx;
	// 				font-size: 28rpx;
	// 				font-family: PingFangSC-Regular, PingFang SC;
	// 				font-weight: 400;
	// 				color: #000000;
	// 				line-height: 80rpx;
	// 				text-align: center;
	// 				overflow: hidden;
	// 				word-break: break-all;
	// 				text-overflow: ellipsis;
	// 				display: -webkit-box;
	// 				-webkit-box-orient: vertical;
	// 				-webkit-line-clamp: 1;
					
	// 				&:nth-child(1){
	// 					width: 25%;
	// 				}
	// 				&:nth-child(2){
	// 					width: 25%;
	// 				}
	// 				&:nth-child(3){
	// 					width: 25%;
	// 				}
	// 				&:nth-child(4){
	// 					width: 25%;
	// 				}
	// 				&:nth-child(5){
	// 					width: 11%;
	// 				}
	// 			}
	// 			&_item_box{
	// 				float: left;
	// 				width: 25%;
	// 				height: 80rpx;
	// 				font-size: 28rpx;
	// 				font-family: PingFangSC-Regular, PingFang SC;
	// 				font-weight: 400;
	// 				color: #000000;
	// 				line-height: 80rpx;
	// 				text-align: center;
	// 				overflow: hidden;
	// 				word-break: break-all;
	// 				text-overflow: ellipsis;
	// 				display: -webkit-box;
	// 				-webkit-box-orient: vertical;
	// 				-webkit-line-clamp: 1;
	// 			}
	// 		}
	// 	}
	// }
</style>
