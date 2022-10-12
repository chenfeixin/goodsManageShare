<template>
<!-- 新增地址 -->
	<view>
		<view class="box">
			<view class="box_l">姓名：</view>
			<input type="text" placeholder="请输入姓名" class="box_input" :value="name" @input="inputName" >
		</view>
		<view class="box">
			<view class="box_l">手机号：</view>
			<input type="number" maxlength="11" placeholder="请输入手机号码" class="box_input" :value="phone" @input="inputPhone" >
		</view>
		<view class="box">
			<view class="box_l">所在地区：</view>
			<!-- #ifdef APP -->
			<pick-regions @getRegion="handleGetRegion">
			    <view class="box_r">{{region}}</view>
			</pick-regions>
			<!-- #endif -->
			
			<!-- #ifdef MP-WEIXIN -->
			<picker mode="region" @change="bindPickerChange" :value="index" :range="array">
				<view class="box_r">{{region}}</view>
			</picker>
			<!-- #endif -->
		</view>
		<view class="site">
			<view class="site_l">详细地址：</view>
			<textarea placeholder="请输入详情地址" class="site_textarea" :value="address" @input="inputAddress" />
		</view>
		<view class="default">
			<view class="default_l">设为默认地址</view>
			<view class="default_r"><switch color="#CBA55F" :checked="isdefault==1" @change="switch1Change" /></view>
		</view>
		<view class="default" style="margin-top: 0rpx;" v-if="id">
			<view class="default_l" style="color: #999999;" @click="deleteSite">删除地址</view>
		</view>
		<view class="button" @click="confirm">保存地址</view>
	</view>
</template>

<script>
	// import pickRegions from '@/components/pick-regions/pick-regions.vue'
	export default {
		data() {
			return {
				region: '选择省市区>',
				area_id: '', //地区id
				name: '',
				phone: '',
				address: '',
				id: '',
				isdefault: 0,
			};
		},
		
		onLoad(options) {
			let that = this
			
			if(options.id!=0){
				that.id = options.id
				
				that.request()
			}
		},
		
		methods:{
			request(){
				let that = this
				uni.request({
					url: getApp().globalData.url + 'api.address/detail',
					method: 'GET',
					header: {
						token: uni.getStorageSync('token')
					},
					data: {
						id: that.id
					},
					success(res) {
						if(res.data.code==1){
							that.name = res.data.data.receiver
							that.phone = res.data.data.mobile
							that.area_id = res.data.data.area_id
							that.address = res.data.data.address
							that.isdefault = res.data.data.isdefault
						}else{
							uni.showToast({
								title: res.data.msg,
								icon: 'none'
							})
						}
					}
				})
			},
			
			// 输入姓名
			inputName(e){
				let that = this
				that.name = e.detail.value
			},
			
			// 输入手机
			inputPhone(e){
				let that = this
				that.phone = e.detail.value
			},
			
		    // 获取选择的地区
		    handleGetRegion(region){
				console.log(region)
				this.area_id = region[2].code
		        this.region = region.map(item=>item.name).join('')
		    },
			
			bindPickerChange(e){
				let that = this
				console.log(e)
				this.area_id = e.detail.code[2]
				this.region = e.detail.value.map(item=>item).join('')
				console.log(this.area_id,this.region)
			},
			
			// 输入详细地址
			inputAddress(e){
				let that = this
				that.address = e.detail.value
			},
			
			// 默认
			switch1Change(e){
				let that = this
				that.isdefault = e.detail.value? 1:0
			},
			
			// 删除收货地址
			deleteSite(){
				let that = this
				
				uni.showModal({
					title: '提示',
					content: '您确定删除此收货地址吗？',
					success: function (res) {
						if (res.confirm) {
							uni.request({
								url: getApp().globalData.url + 'api.address/del',
								method: 'POST',
								header: {
									token: uni.getStorageSync('token')
								},
								data: {
									id: that.id
								},
								success(res) {
									if(res.data.code==1){
										uni.showToast({
											title: '删除成功!',
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
						}
					}
				})
			},
			
			// 保存
			confirm(){
				let that = this
				console.log(that.area_id)
				if(that.name&&that.phone&&that.area_id&&that.address){
					uni.request({
						url: getApp().globalData.url + 'api.address/addedit',
						method: 'POST',
						header: {
							token: uni.getStorageSync('token')
						},
						data: {
							id: that.id,
							address: that.address,
							area_id: that.area_id,
							isdefault: that.isdefault,
							mobile: that.phone,
							receiver: that.name
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
						title: '请完善相关信息',
						icon: 'none'
					})
				}
			},
		}
	}
</script>

<style lang="scss">
	.box{
		margin: 0rpx 48rpx;
		width: 654rpx;
		height: 102rpx;
		border-bottom: 2rpx solid #D8D8D8;
		overflow: hidden;
		
		&_l{
			float: left;
			margin-top: 48rpx;
			width: 140rpx;
			height: 40rpx;
			font-size: 28rpx;
			font-family: PingFangSC-Medium, PingFang SC;
			font-weight: 500;
			color: #333333;
			line-height: 40rpx;
		}
		&_input{
			float: left;
			margin-top: 48rpx;
			margin-left: 20rpx;
			width: 300rpx;
			height: 40rpx;
			font-size: 28rpx;
			font-family: PingFangSC-Medium, PingFang SC;
			font-weight: 500;
			color: #333333;
			line-height: 40rpx;
		}
		&_r{
			float: right;
			margin-top: 48rpx;
			height: 40rpx;
			font-size: 28rpx;
			font-family: PingFangSC-Medium, PingFang SC;
			font-weight: 500;
			color: #ADADAD;
			line-height: 40rpx;
		}
	}
	.site{
		margin: 0rpx 48rpx;
		width: 654rpx;
		height: 156rpx;
		border-bottom: 2rpx solid #D8D8D8;
		overflow: hidden;
		
		&_l{
			float: left;
			margin-top: 48rpx;
			width: 140rpx;
			height: 40rpx;
			font-size: 28rpx;
			font-family: PingFangSC-Medium, PingFang SC;
			font-weight: 500;
			color: #333333;
			line-height: 40rpx;
		}
		&_textarea{
			float: left;
			margin-top: 48rpx;
			margin-left: 20rpx;
			width: 460rpx;
			height: 80rpx;
			font-size: 28rpx;
			font-family: PingFangSC-Medium, PingFang SC;
			font-weight: 500;
			color: #333333;
			line-height: 40rpx;
		}
	}
	.default{
		margin: 32rpx 48rpx;
		height: 80rpx;
		overflow: hidden;
		
		&_l{
			float: left;
			height: 80rpx;
			font-size: 28rpx;
			font-family: PingFangSC-Medium, PingFang SC;
			font-weight: 500;
			color: #333333;
			line-height: 80rpx;
		}
		&_r{
			float: right;
		}
	}
	.button{
		position: fixed;
		bottom: 120rpx;
		left: 0rpx;
		width: 750rpx;
		height: 80rpx;
		background: #CBA55F;
		border-radius: 39rpx;
		text-align: center;
		font-size: 28rpx;
		font-family: PingFangSC-Medium, PingFang SC;
		font-weight: 500;
		color: #FFFFFF;
		line-height: 80rpx;
	}
</style>
