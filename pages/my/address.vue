<template>
<!-- 收货地址 -->
	<view>
		<view class="box" v-for="(item,index) in list" :key="index">
			<block v-if="select==1">
				<view class="box_l" @click="confirmSelect(item.id,index)">
					<view class="box_l_name">
						{{item.receiver}}
						<text>{{item.mobile}}</text> 
						<view class="box_l_name_default" v-if="item.isdefault">默认</view>
					</view>
					<view class="box_l_address">{{item.address}}</view>
				</view>
			</block>
			<block v-else>
				<view class="box_l" @click="site(item.id)">
					<view class="box_l_name">
						{{item.receiver}}
						<text>{{item.mobile}}</text> 
						<view class="box_l_name_default" v-if="item.isdefault">默认</view>
					</view>
					<view class="box_l_address">{{item.address}}</view>
				</view>
			</block>
			<view class="box_border"></view>
			<view class="box_select" @click="confirmSelect(item.id,index)" v-if="select==1">选择</view>
			<view class="box_select" @click="site(item.id)" v-else>编辑</view>
		</view>
		<view class="button" @click="site(0)">添加收货地址</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				list: [],
				select: 0, //0为编辑，1位选择地址
			};
		},
		onLoad(options) {
			let that = this
			
			if(options.select){
				that.select = options.select
			}
		},
		onShow() {
			let that = this
			
			that.request()
		},
		
		methods: {
			request(){
				let that = this
				
				uni.request({
					url: getApp().globalData.url + 'api.address/index',
					method: 'GET',
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
			
			// 选择地址
			confirmSelect(id,index){
				let that = this
				
				var pages = getCurrentPages();// 获取当前页面栈的实例，以数组形式按栈的顺序给出，第一个元素为首页，最后一个元素为当前页面。
				// #ifdef APP
				var currPage = pages[pages.length - 1];   //当前页面
				var prevPage = pages[pages.length - 2];  //上一个页面
				// console.log(prevPage)
				let address = that.list[index]
				prevPage.$vm.address =  address
				prevPage.$vm.address_id =  id
				uni.navigateBack()
				// #endif
				
				// #ifdef MP-WEIXIN
				var currPage = pages[pages.length - 1];   //当前页面
				var prevPage = pages[pages.length - 2];  //上一个页面
				let address = that.list[index]
				prevPage.$vm.address =  address
				prevPage.$vm.address_id =  id
				uni.navigateBack()
				// #endif
				
				// #ifdef H5
				var currPage = pages[pages.length - 1];   //当前页面
				var prevPage = pages[pages.length - 2];  //上一个页面
				let address = that.list[index]
				
				prevPage._data.address =  address
				prevPage._data.address_id =  id
				uni.navigateBack()
				// #endif
			},
			
			// 添加收货地址
			site(id){
				uni.navigateTo({
					url: '/pages/my/site?id='+id
				})
			},
		}
	}
</script>

<style lang="scss">
	page{
		padding-bottom: 100rpx;
		background: #F4F4F4;
	}
	.box{
		margin: 24rpx;
		width: 702rpx;
		height: 182rpx;
		background: #FFFFFF;
		border-radius: 16rpx;
		overflow: hidden;
		
		&_l{
			float: left;
			margin-top: 32rpx;
			margin-left: 28rpx;
			width: 510rpx;
			overflow: hidden;
			
			&_name{
				height: 40rpx;
				font-size: 28rpx;
				font-family: PingFangSC-Medium, PingFang SC;
				font-weight: 500;
				color: #333333;
				line-height: 40rpx;
				overflow: hidden;
				
				text:nth-child(1){
					margin-left: 12rpx;
					color: #999999;
				}
				&_default{
					display: inline-block;
					margin-left: 24rpx;
					width: 80rpx;
					height: 40rpx;
					background: #CBA55F;
					border-radius: 39rpx;
					text-align: center;
					font-size: 28rpx;
					font-family: PingFangSC-Medium, PingFang SC;
					font-weight: 500;
					color: #FFFFFF;
					line-height: 40rpx;
				}
			}
			&_address{
				margin-top: 10rpx;
				width: 510rpx;
				height: 68rpx;
				font-size: 28rpx;
				font-family: PingFangSC-Medium, PingFang SC;
				font-weight: 500;
				color: #333333;
				line-height: 34rpx;
				overflow: hidden;
				word-break: break-all;
				text-overflow: ellipsis;
				display: -webkit-box;
				-webkit-box-orient: vertical;
				-webkit-line-clamp: 2;
			}
		}
		&_border{
			float: left;
			margin-top: 42rpx;
			margin-left: 28rpx;
			width: 2rpx;
			height: 76rpx;
			background: #DFDFDF;
		}
		&_select{
			float: right;
			margin-top: 66rpx;
			margin-right: 40rpx;
			height: 40rpx;
			font-size: 28rpx;
			font-family: PingFangSC-Medium, PingFang SC;
			font-weight: 500;
			color: #CBA55F;
			line-height: 40rpx;
		}
	}
	.button{
		position: fixed;
		bottom: 20rpx;
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
