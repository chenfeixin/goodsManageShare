<template>
<!-- 转为积分 -->
	<view>
		<customHead mode='deNav' title='转为积分' :isReturn='true'></customHead>
		<view class="box">
			<view class="box_t"><text>可转换现金</text>{{myMoney}}</view>
			<view class="box_text">请输入转换金额(转换比例1:5)：</view>
			<input type="number" placeholder="请输入转换金额" placeholder-style="font-size:32rpx" class="box_input" @input="inputMoney">
			<view class="password_title">请输入平台支付提现密码：</view>
			<view class="password">
				<block v-for="(item,index) in 6" :key="index">
					<view class="password_box"><text v-if="password.length>index">●</text></view>
				</block>
				<input type="number" maxlength="6" class="password_input" @input="inputPassword">
			</view>
			<view class="button" v-if="showButton" @click="confirm">确定转为积分</view>
			<view class="button" v-else>确定转为积分</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				money: '', //转换金额
				password: '', //密码
				showButton: true, //是否可以点击按钮
				myMoney: '', //我的金额
			};
		},
		onShow() {
			let that = this
			
			let myMoney = uni.getStorageSync('mymoney')?uni.getStorageSync('mymoney'):0
			that.myMoney = parseInt(myMoney)
		},
		methods: {
			// 输入转换金额
			inputMoney(e){
				let that = this
				
				that.money = e.detail.value
			},
			// 输入密码
			inputPassword(e){
				let that = this
				
				that.password = e.detail.value
			},
			
			// 确定转为积分
			confirm(){
				let that = this
				
				if(that.money>0){
					if(that.password.length==6){
						that.showButton = false
						
						uni.request({
							url: getApp().globalData.url + 'api.user/zhuan',
							method: 'POST',
							header: {
								token: uni.getStorageSync('token')
							},
							data: {
								money: that.money
							},
							success(res) {
								if(res.data.code==1){
									uni.showToast({
										title: res.data.msg,
										success() {
											setTimeout(()=>{
												uni.navigateBack()
											},2000)
										}
									})
								}else{
									that.showButton = true
									uni.showToast({
										title: res.data.msg,
										icon: 'none'
									})
								}
							}
						})
					}else{
						uni.showToast({
							title: '请确认密码',
							icon: 'none'
						})
					}
				}else{
					uni.showToast({
						title: '请输入正确的转换金额',
						icon: 'none'
					})
				}
				
			},
		}
	}
</script>

<style lang="scss">
	.box{
		margin: 40rpx 24rpx;
		width: 702rpx;
		height: 696rpx;
		background: #F4F4F4;
		border-radius: 8rpx;
		overflow: hidden;
		
		&_t{
			margin-top: 32rpx;
			margin-left: 20rpx;
			height: 50rpx;
			font-size: 36rpx;
			font-family: PingFangSC-Medium, PingFang SC;
			font-weight: 500;
			color: #FF4454;
			line-height: 50rpx;
			overflow: hidden;
			
			text{
				margin-right: 8rpx;
				font-size: 24rpx;
				color: #666666;
			}
		}
		&_text{
			margin-top: 32rpx;
			margin-left: 20rpx;
			height: 34rpx;
			font-size: 24rpx;
			font-family: PingFangSC-Medium, PingFang SC;
			font-weight: 500;
			color: #666666;
			line-height: 34rpx;
		}
		&_input{
			margin: 12rpx 20rpx;
			width: 656rpx;
			height: 96rpx;
			border-radius: 4rpx;
			border: 2rpx solid #FF4553;
			text-align: center;
			font-size: 40rpx;
			font-family: PingFangSC-Medium, PingFang SC;
			font-weight: 500;
			color: #000000;
			line-height: 96rpx;
		}
		.password_title{
			margin-top: 72rpx;
			margin-left: 20rpx;
			height: 34rpx;
			font-size: 24rpx;
			font-family: PingFangSC-Medium, PingFang SC;
			font-weight: 500;
			color: #666666;
			line-height: 34rpx;
		}
		.password{
			position: relative;
			margin-top: 40rpx;
			margin-left: 20rpx;
			height: 76rpx;
			overflow: hidden;
			
			&_box{
				float: left;
				margin-right: 36rpx;
				width: 76rpx;
				height: 76rpx;
				border: 2rpx solid #FF4553;
				text-align: center;
				color: #FF4553;
				font-size: 36rpx;
				line-height: 72rpx;
				box-sizing: border-box;
				overflow: hidden;
				z-index: 1;
			}
			&_input{
				position: absolute;
				top: 0rpx;
				left: 0rpx;
				width: 750rpx;
				height: 76rpx;
				opacity: 0;
				z-index: 2;
			}
		}
		.button{
			margin-top: 72rpx;
			margin-left: 51rpx;
			width: 600rpx;
			height: 80rpx;
			background: #FF4553;
			border-radius: 39rpx;
			text-align: center;
			font-size: 28rpx;
			font-family: PingFangSC-Medium, PingFang SC;
			font-weight: 500;
			color: #FFFFFF;
			line-height: 80rpx;
		}
	}
</style>
