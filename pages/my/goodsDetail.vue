<template>
<!-- 商品详情 -->
	<view>
		<!-- 轮播图 -->
		<view class="swiperbox">
			<swiper class="swiper" autoplay interval="3000" duration="500" circular>
				<block v-for="(item,index) in list.images" :key="index">
					<swiper-item>
						<image :src="item" class="swiper_img"></image>
					</swiper-item>
				</block>
			</swiper>
		</view>
		<!-- 价格 -->
		<view class="pricebox">
			<view class="pricebox_now">{{list.price}}</view>
			<view class="pricebox_origin">{{list.marketprice}}</view>
			<view class="pricebox_count">{{list.sales}}人购买</view>
		</view>
		<!-- 商品名 -->
		<view class="name">{{list.title}}</view>
		<view class="border"></view>
		<!-- 商品详情 -->
		<view class="detail">
			<rich-text :nodes="rich"></rich-text>
		</view>
		<!-- 底部按钮 -->
		<view class="footer">
			<view class="footer_button" @click="showPopup(2)">免费领取</view>
		</view>
		<!-- 弹窗 -->
		<view class="popup_bg" v-if="popup" @click="hidePopup"></view>
		<view class="popup" v-if="popup">
			<image src="/static/goodsDetail_detele.png" class="popup_img" @click="hidePopup"></image>
			<view class="popup_title">免费领取</view>
			<view class="popup_num">领取数量 <text>500颗</text></view>
			<view class="popup_siteTitle">收货地址</view>
			<view class="popup_site" @click="selectAddress">
				<image src="/static/site.png" class="popup_site_img"></image>
				<view class="popup_site_box" v-if="address.receiver||address.mobile">
					<view class="popup_site_box_t">{{address.receiver}} <text>{{address.mobile}}</text></view>
					<view class="popup_site_box_f">{{address.address}}</view>
				</view>
				<view class="popup_site_no" v-else>请选择收货地址></view>
			</view>
			<view class="popup_confirm" @click="clickConfirm" v-if="showConfirm">确定领取</view>
			<view class="popup_confirm" v-else>确定领取</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				popup: false, //弹窗是否显示
				list: [],
				rich: '',
				address: '',
				address_id: '',
				id: '',
				showConfirm: '',
			};
		},
		
		onLoad() {
			let that = this
			
			// 获取默认地址
			uni.request({
				url: getApp().globalData.url + 'api.address/def_address',
				method: 'GET',
				header: {
					token: uni.getStorageSync('token')
				},
				success(res) {
					if(res.data.code==1){
						that.address = res.data.data
						that.address_id = res.data.data.id
					}
				}
			})
		},
		
		onShow() {
			let that = this
			
			that.showConfirm = true
			// 商品详情
			uni.request({
				url: getApp().globalData.url + 'api.goods/detail',
				method: 'GET',
				header: {
					token: uni.getStorageSync('token')
				},
				data: {
					id: 45
				},
				success(res) {
					if(res.data.code==1){
						that.list = res.data.data
						that.rich = res.data.data.content.replace(/\<img/gi, '<img style="max-width:100%;height:auto" ')
					}
				}
			})
		},
		
		methods: {
			skip(url){
				uni.switchTab({
					url,
				})
			},
			
			// 选择地址
			selectAddress(){
				let that = this
				
				uni.navigateTo({
					url: '/pages/my/address?select=1'
				})
			},
			
			// 关闭弹窗
			hidePopup(){
				let that = this
				
				that.popup = false
			},
			
			// 打开弹窗
			showPopup(type){
				let that = this
				
				uni.request({
					url: getApp().globalData.url + 'api.cart/add',
					method: 'POST',
					header: {
						token: uni.getStorageSync('token')
					},
					data: {
						goods_id: 45,
						nums: 500,
						sceneval: 2
					},
					success(res) {
						uni.hideLoading()
						if(res.data.code==1){
							that.popup = true
							that.id = res.data.data
						}else{
							uni.showToast({
								title: res.data.msg,
								icon: 'none'
							})
						}
					}
				})
			},
			
			// 关闭弹窗
			hidePopup(){
				let that = this
				
				that.popup = false
			},
			
			// 确定领取
			clickConfirm(){
				let that = this
				
				that.showConfirm = false
				uni.request({
					url: getApp().globalData.url + 'api.order/add',
					method: 'POST',
					header: {
						token: uni.getStorageSync('token')
					},
					data: {
						ids: that.id,
						address_id: that.address_id,
					},
					success(res) {
						if(res.data.code==1){
							uni.showToast({
								title: '领取成功!',
								success() {
									that.popup = false
									that.showConfirm = true
								}
							})
						}else{
							that.showConfirm = true
							uni.showToast({
								title: res.data.msg,
								icon: 'none',
							})
							
						}
					}
				})
			},
		}
	}
</script>

<style lang="scss">
	// 轮播图
	.swiperbox{
		width: 750rpx;
		height: 750rpx;
		overflow: hidden;
		
		.swiper{
			width: 750rpx;
			height: 750rpx;
			overflow: hidden;
			
			&_img{
				width: 750rpx;
				height: 750rpx;
			}
		}
	}
	// 价格
	.pricebox{
		margin: 28rpx 24rpx;
		height: 50rpx;
		overflow: hidden;
		
		&_img{
			float: left;
			margin-top: 16rpx;
			width: 20rpx;
			height: 18rpx;
			overflow: hidden;
		}
		&_now{
			float: left;
			margin-left: 12rpx;
			height: 50rpx;
			font-size: 36rpx;
			font-family: PingFangSC-Medium, PingFang SC;
			font-weight: 500;
			color: #CBA55F;
			line-height: 50rpx;
			
			text{
				display: inline-block;
				margin-left: 8rpx;
				font-size: 28rpx;
				font-family: PingFangSC-Regular, PingFang SC;
				font-weight: 400;
			}
		}
		&_origin{
			float: left;
			margin-left: 32rpx;
			font-size: 28rpx;
			font-family: PingFangSC-Medium, PingFang SC;
			font-weight: 500;
			color: #999999;
			line-height: 50rpx;
			text-decoration:line-through;
		}
		&_count{
			float: right;
			font-size: 28rpx;
			font-family: PingFangSC-Medium, PingFang SC;
			font-weight: 500;
			color: #CCCCCC;
			line-height: 50rpx;
		}
	}
	// 商品名
	.name{
		margin: 28rpx 24rpx;
		width: 702rpx;
		font-size: 32rpx;
		font-family: PingFangSC-Medium, PingFang SC;
		font-weight: 500;
		color: #333333;
		line-height: 44rpx;
		overflow: hidden;
	}
	// border
	.border{
		width: 750rpx;
		height: 12rpx;
		background: #F4F4F4;
		opacity: 0.89;
		overflow: hidden;
	}
	// 商品详情
	.detail{
		margin-top: 28rpx;
		padding-bottom: 120rpx;
		width: 750rpx;
		overflow: hidden;
		
		image{
			width: 100%;
			height: 100%;
		}
		&_img{
			width: 750rpx;
			height: 60rpx;
		}
	}
	// 底部按钮
	.footer{
		position: fixed;
		bottom: 0rpx;
		left: 0rpx;
		width: 750rpx;
		height: 120rpx;
		background: #FAFAFA;
		overflow: hidden;
		
		&_button{
			margin: 20rpx 155rpx;
			width: 440rpx;
			height: 84rpx;
			background: linear-gradient(90deg, #F6E1B0 0%, #EDC66E 100%);
			border-radius: 50rpx;
			text-align: center;
			font-size: 32rpx;
			font-family: PingFangSC-Semibold, PingFang SC;
			font-weight: 600;
			color: #333333;
			line-height: 84rpx;
		}
	}
	
	// 弹窗
	.popup_bg{
		position: fixed;
		top: 0rpx;
		left: 0rpx;
		width: 100%;
		height: 100%;
		background: rgba(0,0,0,0.4);
		z-index: 98;
		overflow: hidden;
	}
	.popup{
		position: fixed;
		bottom: 0rpx;
		left: 0rpx;
		width: 750rpx;
		height: 750rpx;
		background: #FFFFFF;
		border-radius: 24rpx 24rpx 0rpx 0rpx;
		z-index: 99;
		overflow: hidden;
		
		&_img{
			margin-top: 42rpx;
			margin-left: 684rpx;
			width: 36rpx;
			height: 36rpx;
		}
		&_title{
			margin-top: 36rpx;
			height: 44rpx;
			font-size: 32rpx;
			font-family: PingFangSC-Medium, PingFang SC;
			font-weight: 500;
			color: #000000;
			line-height: 44rpx;
			text-align: center;
		}
		&_num{
			margin: 44rpx 40rpx 40rpx 44rpx;
			height: 40rpx;
			font-size: 28rpx;
			font-family: PingFangSC-Regular, PingFang SC;
			font-weight: 400;
			color: #000000;
			line-height: 40rpx;
			overflow: hidden;
			
			text{
				float: right;
			}
		}
		&_siteTitle{
			margin-bottom: 30rpx;
			margin-left: 44rpx;
			height: 40rpx;
			font-size: 28rpx;
			font-family: PingFangSC-Regular, PingFang SC;
			font-weight: 400;
			color: #000000;
			line-height: 40rpx;
			overflow: hidden;
		}
		&_site{
			margin: 0rpx 24rpx;
			width: 698rpx;
			height: 176rpx;
			box-shadow: 0rpx 4rpx 8rpx 0rpx rgba(223,223,223,0.5);
			border-radius: 8rpx;
			border: 2rpx solid #EBEBEB;
			overflow: hidden;
			
			&_img{
				float: left;
				margin: 56rpx 24rpx 56rpx;
				width: 64rpx;
				height: 64rpx;
			}
			&_box{
				float: left;
				margin-top: 28rpx;
				overflow: hidden;
				
				&_t{
					height: 40rpx;
					font-size: 28rpx;
					font-family: PingFangSC-Medium, PingFang SC;
					font-weight: 500;
					color: #333333;
					line-height: 40rpx;
					
					text{
						margin-left: 20rpx;
						color: #EDC771;
					}
				}
				&_f{
					margin-top: 12rpx;
					width: 500rpx;
					height: 68rpx;
					font-size: 24rpx;
					font-family: PingFangSC-Medium, PingFang SC;
					font-weight: 500;
					color: #333333;
					line-height: 34rpx;
					overflow: hidden;
				}
			}
			&_no{
				float: left;
				margin-top: 68rpx;
				margin-left: 28rpx;
				height: 40rpx;
				font-size: 28rpx;
				font-family: PingFangSC-Regular, PingFang SC;
				font-weight: 400;
				color: #000000;
				line-height: 40rpx;
			}
		}
		&_confirm{
			margin: 60rpx 155rpx;
			width: 440rpx;
			height: 84rpx;
			background: linear-gradient(90deg, #F6E1B0 0%, #EDC66E 100%);
			border-radius: 50rpx;
			text-align: center;
			font-size: 32rpx;
			font-family: PingFangSC-Semibold, PingFang SC;
			font-weight: 600;
			color: #333333;
			line-height: 84rpx;
		}
	}
</style>
