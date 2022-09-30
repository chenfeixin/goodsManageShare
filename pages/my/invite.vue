<template>
	<!-- 邀请好友 -->
	<view>
		<!-- <view class="top">
			<text>邀请好友成为经销商，购卡成功即可获得
				2000现金</text>
		</view> -->

		<view class="box">
<!-- 			<image src="/static/invite_bg.jpg" class="box_bg"></image>
			<image :src="prcode" class="box_code"></image>
			<view class="box_name">{{nickname}} 邀请您成为VIP</view> -->
			<image :src="shareImg" class="box_bg" v-if="shareImg"></image>
		</view>
<!-- 		<view class="button" @click="save">
			长按上方图片保存
			<canvas canvas-id="canvas" style="width:702rpx;height:100rpx;position:fixed;left:9000px;"></canvas>
		</view> -->
		<!-- <view class="button">点击右上角进行分享</view> -->
		<!-- #ifdef APP -->
		<view class="button" @click="download">下载</view>
		<!-- <image :src="prcode" class="shareImg"></image> -->
		<!-- #endif -->
		<!-- #ifdef MP-WEIXIN -->
		<view class="button" @click="shareWx">邀请好友</view>
		<!-- #endif -->
		<!-- #ifdef H5 -->
		<view class="button" @click="share">点击右上角进行分享</view>
		<!-- #endif -->
		<canvas canvas-id="canvas" style="width:702rpx;height:1000rpx;position:fixed;left:9000px;"></canvas>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				nickname: '',
				background: '/static/invite_bg.jpg',
				prcode: '',
				shareImg: '',
			};
		},
		onShow() {
			let that = this
			
			uni.showLoading({
				title: '邀请海报生成中~'
			})
			
			uni.request({
				url: getApp().globalData.url + 'api.user/index',
				method: 'GET',
				header: {
					token: uni.getStorageSync('token')
				},
				success(res) {
					if(res.data.code==1){
						that.nickname = res.data.data.nickname
					}
				}
			})
			
			// #ifdef APP
			uni.request({
				url: getApp().globalData.url + 'api.user/qrcodes',
				method: 'POST',
				header: {
					token: uni.getStorageSync('token')
				},
				success(res) {
					console.log(res)
					if(res.data.code==1){
						// console.log(res.data.data)
						that.prcode = res.data.data
						that.save()
						// uni.getImageInfo({
						// 	src: res.data.data,
						// 	success: function (image) {
						// 		that.prcode = image.path
						// 		setTimeout(()=>{
						// 			that.save()
						// 		},100)
						// 	}
						// })
					}else{
						console.log(res)
					}
				}
			})
			// #endif
			
			// #ifdef MP-WEIXIN
			uni.login({
			  success: function (res) {
			    uni.request({
			    	url: getApp().globalData.url + 'api.user/qrcode',
			    	method: 'POST',
			    	header: {
			    		token: uni.getStorageSync('token')
			    	},
					data: {
						code: res.code
					},
			    	success(res) {
			    		console.log(res)
			    		if(res.data.code==1){
			    			console.log(res.data.data)
			    			// that.prcode = res.data.data
			    			// that.save()
			    			uni.getImageInfo({
			    				src: res.data.data,
			    				success: function (image) {
			    					that.prcode = image.path
			    					setTimeout(()=>{
			    						that.save()
			    					},100)
			    				}
			    			})
			    		}else{
			    			console.log(res)
			    		}
			    	}
			    })
			  }
			});
			// #endif
		},
		
		methods: {
			// 下载海报
			save() {
				let that = this
				let rpx = null
				uni.hideLoading()
				uni.getSystemInfo({
					success: function(res) {
						rpx = res.windowWidth / 375
					},
				})

				var ctx = wx.createCanvasContext('canvas')
				// 设置背景
				ctx.setFillStyle('#FFFFFF')
				ctx.fillRect(0, 0, 351 * rpx, 500 * rpx)
				// 背景图片
				ctx.drawImage(that.background, 0 * rpx, 0 * rpx, 351 * rpx, 500 * rpx)
				// // 名
				ctx.setFontSize(18)
				ctx.textAlign = 'center'
				ctx.setFillStyle("#FFFFFF")
				ctx.fillText(that.nickname+' 邀请您成为会员', 180 * rpx, 318 * rpx, 500 * rpx)
				// 二维码
				ctx.drawImage(that.prcode, 105 * rpx, 119 * rpx, 136 * rpx, 130 * rpx)
				ctx.draw(true, setTimeout(function() {
					uni.canvasToTempFilePath({
						x: 0,
						y: 0,
						width: 351 * rpx,
						height: 500 * rpx,
						destWidth: 351 * wx.getSystemInfoSync().pixelRatio,
						destHeight: 500 * wx.getSystemInfoSync().pixelRatio,
						fileType: 'jpg',
						quality: 1,
						canvasId: 'canvas',
						success: function(res) {
							console.log('---------------------res',res)
							that.shareImg = res.tempFilePath
							console.log(that.shareImg)
						}
					})
				}, 500))
			},
			
			share(){
				let that = this
				// uni.share({
				// 	provider: "weixin",
				// 	scene: "WXSceneSession",
				// 	type: 0,
				// 	href: "http://uniapp.dcloud.io/",
				// 	title: "好掌柜APP", ]0
				// 	summary: that.nickname+' 邀请您成为VIP',
				// 	imageUrl: "https://bjetxgzv.cdn.bspapp.com/VKCEYUGU-uni-app-doc/d8590190-4f28-11eb-b680-7980c8a877b8.png",
				// 	success: function (res) {
				// 		console.log("success:" + JSON.stringify(res));
				// 	},
				// 	fail: function (err) {
				// 		console.log("fail:" + JSON.stringify(err));
				// 	}
				// });
			},
			
			// 下载
			download(){
				let that = this
				
				uni.showLoading({
					title: '下载中'
				})
				uni.saveImageToPhotosAlbum({
					filePath: that.shareImg,
					complete: function () {
						uni.hideLoading()
						uni.showToast({
							title: '下载成功'
						})
					}
				});
			},
			
			// 微信分享
			shareWx(){
				let that = this
				
				wx.showShareImageMenu({
				   path: that.shareImg
				 })
				// wx.downloadFile({
				//    url: that.shareImg,
				//    success: (res) => {
				//      wx.showShareImageMenu({
				//        path: res.tempFilePath
				//      })
				//    }
				//  })
			},
			
		}
	}
</script>

<style lang="scss">
	page {
		background: #F4F4F4;
	}

	.top {
		padding: 30rpx 99rpx;
		margin: 20rpx 24rpx;
		background: #FFFFFF;
		border-radius: 16rpx;
		font-size: 28rpx;
		font-family: PingFangSC-Medium, PingFang SC;
		font-weight: 500;
		color: #000000;
		line-height: 40rpx;
		text-align: center;
		overflow: hidden;
	}

	.box {
		position: relative;
		margin: 20rpx 24rpx;
		width: 702rpx;
		height: 1000rpx;
		background: #FFFFFF;
		border-radius: 16rpx;
		overflow: hidden;

		&_bg {
			width: 702rpx;
			height: 1000rpx;
			display: block;
		}
		&_code{
			position: absolute;
			top: 238rpx;
			left: 210rpx;
			width: 280rpx;
			height: 280rpx;
		}
		&_name{
			position: absolute;
			top: 600rpx;
			left: 0rpx;
			text-align: center;
			width: 702rpx;
			height: 60rpx;
			font-size: 36rpx;
			font-family: PingFangSC-Medium, PingFang SC;
			font-weight: 500;
			color: #FFFFFF;
			line-height: 60rpx;
		}
	}

	.button {
		margin: 42rpx 95rpx;
		width: 560rpx;
		height: 80rpx;
		background: linear-gradient(90deg, #F6E1B0 0%, #EDC66E 100%);
		border-radius: 39rpx;
		text-align: center;
		font-size: 28rpx;
		font-family: PingFangSC-Medium, PingFang SC;
		font-weight: 500;
		color: #FFFFFF;
		line-height: 80rpx;
	}
	.shareImg{
		// position: fixed;
		// top: 200rpx;
		// left: 24rpx;
		width: 700rpx;
		height: 700rpx;
		overflow: hidden;
		// opacity: 0.1;
		// z-index: 999;
	}
</style>
