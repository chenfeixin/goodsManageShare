<template>
<!-- 提现 -->
	<view>
		<view class="box">
			<view class="box_t"><text>可提现金额：</text>{{myMoney}}</view>
			<view class="box_text">请输入本次提现额度：</view>
			<input type="number" maxlength="8" placeholder="请输入本次提现额度" placeholder-style="font-size:32rpx" class="box_input" :value="money" @input="inputMoney">
			<!-- <view class="box_t"><text>税  费：</text>￥{{serviceMoney}}</view>
			<view class="box_t"><text>到账金额：</text>￥{{residue}}</view> -->
			<view class="password_title" v-if="bankName">收款账号： <text style="color:#333333;margin-left: 20rpx;">{{bankName}}</text></view>
			<view class="password_title" @click="bindCard" v-else>收款账号： <text style="color:#6236FF;margin-left: 20rpx;">未绑卡，立即绑定></text></view>
			<!-- <view class="password_title">提示：税费为提现金额的{{service}}%</view> -->
			<view class="password_title">提示：提现到账T+1；周末及节假日顺延,每次提现最小额度1000元起</view>
			<view class="password_title">请输入平台支付提现密码：</view>
			<view class="password">
				<block v-for="(item,index) in 6" :key="index">
					<view class="password_box"><text v-if="password.length>index">●</text></view>
				</block>
				<input type="number" maxlength="6" class="password_input" @input="inputPassword">
			</view>
			<view class="button" v-if="showButton" @click="confirm">提交</view>
			<view class="button" v-else>提交</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				money: 1000, //转换金额
				password: '', //密码
				showButton: true, //是否可以点击按钮
				bankName: '', //银行信息
				service: 0, //手续费
				serviceMoney: 0, //手续费金额
				residue: 0, //剩余金额
				myMoney: uni.getStorageSync('mymoney')?uni.getStorageSync('mymoney'):0, //剩余金额
			}
		},
		onShow() {
			let that = this
			
			let myMoney = uni.getStorageSync('mymoney')?uni.getStorageSync('mymoney'):0
			that.myMoney = parseInt(myMoney)
			uni.request({
				url: getApp().globalData.url + 'api.user/mingxi',
				method: 'POST',
				header: {
					token: uni.getStorageSync('token')
				},
				success(res) {
					if(res.data.code==1){
						if(res.data.data.payee!=null){
							that.bankName = res.data.data.collection_account
							// that.service = res.data.data.withdrawal_fee / 10	
						}
					}
				}
			})
		},
		methods: {
			// 输入转换金额
			inputMoney(e){
				let that = this
				
				that.money = e.detail.value
				
				// if(that.money>=e.detail.value){
				// 	that.money = e.detail.value
				// 	let serviceMoney = that.service * e.detail.value
				// 	that.serviceMoney = serviceMoney.toFixed(2)
				// 	that.residue = that.money - that.serviceMoney
				// }else{
				// 	that.money = e.detail.value
				// 	let serviceMoney = that.service * that.money
				// 	that.serviceMoney = serviceMoney.toFixed(2)
				// 	that.residue = that.money - that.serviceMoney
				// }
				
			},
			// 失去光标触发
			// blurMoney(e){
			// 	let that = this
			// 	console.log(e.detail.value)
			// 	that.money = e.detail.value
			// 	that.residue = that.service * e.detail.value
			// 	that.serviceMoney = that.money - that.residue
			// },
			// 输入密码
			inputPassword(e){
				let that = this
				
				that.password = e.detail.value
			},
			// 去绑定银行卡
			bindCard(){
				uni.navigateTo({
					url: '/pages/withdraw/bindCard'
				})
			},
			// 确定转为积分
			confirm(){
				let that = this
				
				if(that.money>=1000){
					if(that.password.length==6){
						that.showButton = false
						
						uni.request({
							url: getApp().globalData.url + 'api.user/tixiamn',
							method: 'POST',
							header: {
								token: uni.getStorageSync('token')
							},
							data: {
								money: that.money,
								payment: that.password
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
						title: '最小提现金额1000元起',
						icon: 'none'
					})
				}
				
			},
		}
	}
</script>

<style lang="scss">
	.box{
		padding-bottom: 30rpx;
		margin: 40rpx 24rpx;
		width: 702rpx;
		// height: 696rpx;
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
			color: #EEC873;
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
			border: 2rpx solid #EEC873;
			text-align: center;
			font-size: 40rpx;
			font-family: PingFangSC-Medium, PingFang SC;
			font-weight: 500;
			color: #EEC873;
			line-height: 96rpx;
		}
		.password_title{
			margin-top: 40rpx;
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
				border: 2rpx solid #EEC873;
				text-align: center;
				color: #EEC873;
				font-size: 36rpx;
				line-height: 72rpx;
				box-sizing: border-box;
				overflow: hidden;
				z-index: 1;
			}
			&_input{
				position: absolute;
				top: 0rpx;
				left: -1000rpx;
				width: 1500rpx;
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
			background: #EEC873;
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
