<template>
<!-- 绑卡 -->
	<view>
		<view class="box">
			<view class="box_l">开卡银行：</view>
			<input type="text" class="box_c" placeholder="请填写开户行名称" :value="bank" @input="inputBank">
		</view>
		<view class="box">
			<view class="box_l">持卡人：</view>
			<input type="text" maxlength="10" class="box_c" placeholder="请填写持卡人姓名" :value="name" @input="inputName">
		</view>
		<view class="box">
			<view class="box_l">卡号：</view>
			<input type="number" maxlength="21" class="box_c" placeholder="请输入银行卡号" :value="card" @input="inputCard">
		</view>
		<view class="box">
			<view class="box_l">身份证号：</view>
			<input type="text" maxlength="18" class="box_c" placeholder="请输入身份证号" :value="number" @input="inputNumber">
		</view>
		<view class="box">
			<view class="box_l">持卡人电话：</view>
			<input type="number" maxlength="11" class="box_c" placeholder="请输入持卡人手机号" :value="phone" @input="inputPhone">
		</view>
<!-- 		<view class="box">
			<view class="box_l">网点所在地：</view>
			<input type="text" class="box_c" placeholder="请选择>">
		</view> -->
		<view class="box">
			<view class="box_l">支行名称：</view>
			<input type="text" class="box_c" placeholder="请输入开户支行名称" :value="address" @input="inputAddress">
		</view>
		<!-- <view class="box">
			<view class="box_l">支付密码：</view>
			<input type="text" class="box_c" placeholder="请输入开户支行名称" :value="address" @input="inputAddress">
		</view> -->
		<view class="button" @click="comfirn">提交</view>
		
		<view class="agreement"><text @click="server('用户服务协议')">《用户服务协议》</text> 及 <text @click="server('隐私政策')">《隐私政策》</text></view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				bank: '', //开户银行
				name: '', //持卡人姓名
				card: '', //卡号
				phone: '', //手机号
				address: '', //支行名称
				number: '', //身份证号
			};
		},
		onShow() {
			let that = this
			
			uni.request({
				url: getApp().globalData.url + 'api.user/mingxi',
				method: 'POST',
				header: {
					token: uni.getStorageSync('token')
				},
				success(res) {
					if(res.data.code==1){
						that.phone = res.data.data.tel
						that.bank = res.data.data.bank_of_deposit
						that.name = res.data.data.payee
						that.card = res.data.data.collection_account
						that.address = res.data.data.bank_address
						that.number = res.data.data.number
					}
				}
			})
		},
		methods: {
			// 输入开户银行
			inputBank(e){
				let that = this
				
				that.bank = e.detail.value
			},
			// 输入持卡人姓名
			inputName(e){
				let that = this
				
				that.name = e.detail.value
			},
			// 输入卡号
			inputCard(e){
				let that = this
				
				that.card = e.detail.value
			},
			// 输入身份证号
			inputNumber(e){
				let that = this
				
				that.number = e.detail.value
			},
			// 输入手机号
			inputPhone(e){
				let that = this
				
				that.phone = e.detail.value
			},
			// 输入支行名称
			inputAddress(e){
				let that = this
				
				that.address = e.detail.value
			},
			// 提交
			comfirn(){
				let that = this
				
				 if(that.bank){
					if(that.name){
						if(that.card){
							if(that.number.length==18){
								if(that.phone.length==11){
									if(that.address){
										uni.request({
											url: getApp().globalData.url + 'api.user/yinang',
											method: 'POST',
											header: {
												token: uni.getStorageSync('token')
											},
											data: {
												payee: that.name,
												collection_account: that.card,
												bank_of_deposit: that.bank,
												bank_address: that.address,
												tel: that.phone,
												number: that.number
											},
											success(res) {
												if(res.data.code==1){
													uni.showToast({
														title: res.data.msg,
														success() {
															setTimeout(()=>{
																uni.navigateBack()
															},1500)
														}
													})
												}else{
													uni.showToast({
														title: res.data.msg,
														icon: 'none'
													})
												}
											}
										})
									}else{
										uni.showToast({
										 	title: '请输入支行名称',
										 	icon: 'none'
										})
									}
								}else{
									uni.showToast({
									 	title: '请输入持卡人手机号',
									 	icon: 'none'
									})
								}
							}else{
								uni.showToast({
								 	title: '请输入正确的身份证号',
								 	icon: 'none'
								})
							}
						}else{
							uni.showToast({
							 	title: '请输入银行卡号',
							 	icon: 'none'
							})
						}
					}else{
						uni.showToast({
						 	title: '请输入持卡人姓名',
						 	icon: 'none'
						})
					}
				 }else{
					uni.showToast({
					 	title: '请输入银行卡',
						icon: 'none'
					})
				 }
			},
			
			// 服务协议/隐私政策
			server(e){
				console.log(123)
				uni.navigateTo({
					url: '/pages/withdraw/serve?title='+e
				})
			},
		}
	}
</script>

<style lang="scss">
	.box{
		margin: 0rpx 42rpx;
		width: 660rpx;
		height: 104rpx;
		border-bottom: 2rpx solid #D8D8D8;
		
		&_l{
			float: left;
			width: 180rpx;
			height: 104rpx;
			font-size: 28rpx;
			font-family: PingFangSC-Medium, PingFang SC;
			font-weight: 500;
			color: #333333;
			line-height: 104rpx;
		}
		&_c{
			float: left;
			margin-left: 32rpx;
			width: 400rpx;
			height: 104rpx;
			font-size: 28rpx;
			font-family: PingFangSC-Medium, PingFang SC;
			font-weight: 500;
			color: #000000;
			line-height: 104rpx;
			overflow: hidden;
		}
	}
	
	.button{
		margin: 160rpx 95rpx 0rpx;
		width: 560rpx;
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
	
	
	.agreement{
	  margin-top: 200rpx;
	  height: 40rpx;
	  font-size: 28rpx;
	  font-family: PingFangSC-Regular, PingFang SC;
	  font-weight: 400;
	  color: #333333;
	  line-height: 40rpx;
	  text-align: center;
	}
	.agreement>text{
	  color: #6236FF;
	}
</style>
