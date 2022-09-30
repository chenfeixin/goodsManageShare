<template>
<!-- 名片 -->
	<view>
		<view class="box" v-if="list">
			<view class="box_name">
				<!-- <view class="box_name_text">{{list.nickname}} <text>{{list.level}}</text></view> -->
				<view class="box_name_text">{{list.nickname}} <text>{{list.user_category}}</text></view>
			</view>
			<view class="box_c">
				<view class="box_c_box">
					<view class="box_c_box_t">{{list.mobile}}</view>
					<view class="box_c_box_f">联系电话</view>
				</view>
				<view class="box_c_box">
					<view class="box_c_box_t" style="text-align: center;">{{list.order_count}}</view>
					<view class="box_c_box_f" style="text-align: center;">购卡数量</view>
				</view>
				<!-- <view class="box_c_box" @click="heInvite">
					<view class="box_c_box_t" style="text-align: center;">{{list.inviter1_count}}</view>
					<view class="box_c_box_f" style="text-align: center;">团队</view>
				</view> -->
				<view class="box_c_box">
					<view class="box_c_box_t" style="text-align: center;">{{id}}</view>
					<view class="box_c_box_f" style="text-align: center;">用户ID</view>
				</view>
			</view>
			<view class="box_f">
				<!-- <view class="box_f_t">{{list.inviter1}} <text>{{list.inviter1_level}}</text></view> -->
				<view class="box_f_t">{{list.inviter1}} <text>{{list.inviter1_category}}</text></view>
				<view class="box_f_b">邀请人</view>
			</view>
			<view class="box_time">
				<view class="box_timebox">
					<view class="box_timebox_t">{{list.createtime}}</view>
					<view class="box_timebox_b">注册时间</view>
				</view>
				<!-- <view class="box_timebox">
					<view class="box_timebox_t">{{list.leveltime}}</view>
					<view class="box_timebox_b">成为经销商时间</view>
				</view> -->
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				list: '',
				id: '',
			};
		},
		onLoad(options) {
			let that = this
			
			that.id = options.id
			uni.request({
				url: getApp().globalData.url + 'api.order/business',
				method: 'POST',
				header: {
					token: uni.getStorageSync('token')
				},
				data: {
					uid: options.id
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
			// 他的邀请
			heInvite(){
				let that = this
				uni.navigateTo({
					url: '/pages/my/heInvite?id='+that.id + '&level=' + that.list.level + '&name=' + that.list.nickname + '&phone=' + that.list.mobile
				})
			},
		}
	}
</script>

<style lang="scss">
	page{
		background: #F4F4F4;
	}
	.box{
		margin: 20rpx;
		width: 710rpx;
		height: 416rpx;
		// background: linear-gradient(90deg, #6C6666 0%, #D1AFA5 100%);
		background: linear-gradient(90deg, #31b8d6 0%, #27b3a6 100%);
		// background: linear-gradient(90deg, #033b46 0%, #28bbae 100%);
		border-radius: 16rpx;
		overflow: hidden;
		
		&_name{
			margin-top: 44rpx;
			margin-left: 50rpx;
			width: 500rpx;
			height: 50rpx;
			font-size: 36rpx;
			font-family: PingFangSC-Medium, PingFang SC;
			font-weight: 500;
			color: #FFFFFF;
			line-height: 50rpx;
			overflow: hidden;
			
			text{
				margin-left: 20rpx;
				color: #ffe200;
			}
		}
		&_c{
			margin-top: 24rpx;
			margin-left: 50rpx;
			height: 70rpx;
			overflow: hidden;
			
			&_box{
				float: left;
				width: 33%;
				height: 70rpx;
				overflow: hidden;
				
				&:nth-child(2){
					width: 160rpx;
				}
				&:nth-child(3){
					width: 150rpx;
				}
				&:nth-child(4){
					width: 120rpx;
				}
				&_t{
					// width: 120rpx;
					height: 40rpx;
					font-size: 28rpx;
					font-family: PingFangSC-Medium, PingFang SC;
					font-weight: 500;
					color: #FFFFFF;
					line-height: 40rpx;
				}
				&_f{
					margin-top: 2rpx;
					height: 28rpx;
					font-size: 20rpx;
					font-family: PingFangSC-Regular, PingFang SC;
					font-weight: 400;
					color: #FFFFFF;
					line-height: 28rpx;
				}
			}
		}
		&_f{
			margin-top: 24rpx;
			margin-left: 50rpx;
			height: 70rpx;
			overflow: hidden;
			
			&_t{
				height: 40rpx;
				font-size: 28rpx;
				font-family: PingFangSC-Medium, PingFang SC;
				font-weight: 500;
				color: #FFFFFF;
				line-height: 40rpx;
				
				text{
					margin-left: 20rpx;
					color: #ffe200;
				}
			}
			&_b{
				margin-top: 2rpx;
				height: 28rpx;
				font-size: 20rpx;
				font-family: PingFangSC-Regular, PingFang SC;
				font-weight: 400;
				color: #FFFFFF;
				line-height: 28rpx;
			}
		}
		&_time{
			margin-top: 24rpx;
			height: 70rpx;
			overflow: hidden;
			
			.box_timebox{
				float: left;
				margin-left: 50rpx;
				width: 288rpx;
				height: 70rpx;
				overflow: hidden;
				
				&_t{
					width: 288rpx;
					height: 40rpx;
					font-size: 28rpx;
					font-family: PingFangSC-Medium, PingFang SC;
					font-weight: 500;
					color: #FFFFFF;
					line-height: 40rpx;
				}
				&_b{
					margin-top: 2rpx;
					height: 28rpx;
					font-size: 20rpx;
					font-family: PingFangSC-Regular, PingFang SC;
					font-weight: 400;
					color: #FFFFFF;
					line-height: 28rpx;
				}
			}
		}
	}
</style>
