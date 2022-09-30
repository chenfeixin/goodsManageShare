<template>
	<view>
		<image :src="contract" mode="" style="width: 750rpx;height: 4163rpx;"></image>	
		<!-- #ifdef APP-PLUS -->
		<view class="button" v-if="list.status==2" @click="download">下载</view>
		<view class="button" @click="signature" v-else>立即签署</view>
		<!-- #endif -->
		<!-- #ifdef MP-WEIXIN -->
		<view class="button" v-if="list.status==2" @click="download">下载</view>
		<view class="button" @click="signature" v-else>立即签署</view>
		<!-- #endif -->
		<!-- #ifdef H5 -->
		<view class="button" v-if="list.status==2">长按上方合同保存</view>
		<view class="button" @click="signature" v-else>立即签署</view>
		<!-- #endif -->
		
		<canvas canvas-id="canvas" style="width:750rpx;height:4163rpx;position:fixed;left:9000px;"></canvas>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				contract: '', //合同图
				id: '', //合同id
				image: '',
				list: '',
			};
		},
		onLoad(options) {
			let that = this
			
			if(options.id){
				that.id = options.id
			}
			
			uni.request({
				url: getApp().globalData.url + 'api.user/contract',
				method: 'POST',
				header: {
					token: uni.getStorageSync('token')
				},
				data: {
					id: options.id
				},
				success(res){
					if(res.data.code==1){
						that.list = res.data.data
						if(!res.data.data.img_url){
							// 下载合同图
							uni.getImageInfo({
								src: 'https://www.gargle.top/img/new_signature.png',
								success: function (image) {
									that.image = image.path
									setTimeout(()=>{
										that.save()
									},100)
								}
							})
						}else{
							that.contract = res.data.data.img_url
						}
					}
				}
			})
			
			// uni.request({
			// 	url: getApp().globalData.url + 'api.user/contract',
			// 	method: 'POST',
			// 	data: {
			// 		id: 1,
			// 	},
			// 	success(res) {
			// 		if(res.data.code==1){
			// 			that.list = res.data.data
			// 		}
			// 	}
			// })
		},
		methods: {
			// 下载海报
			save() {
				let that = this
				let rpx = null
				uni.getSystemInfo({
					success: function(res) {
						rpx = res.windowWidth / 375
					},
				})
			
				var ctx = wx.createCanvasContext('canvas')
				// 设置背景
				ctx.setFillStyle('#FFFFFF')
				ctx.fillRect(0, 0, 375 * rpx,2082 * rpx)
				// 背景图片
				ctx.drawImage(that.image, 0 * rpx, 0 * rpx, 375 * rpx, 2082 * rpx)
				// 合同编号
				ctx.setFontSize(11)
				ctx.textAlign = 'left'
				ctx.setFillStyle("#000000")
				ctx.fillText(that.list.no, 62 * rpx, 22.5 * rpx)
				// // 身份证号码
				// ctx.setFontSize(16)
				// ctx.setFillStyle("#000000")
				// ctx.fillText('442458741236547895', 90 * rpx, 194 * rpx)
				// // 手机号码
				// ctx.setFontSize(16)
				// ctx.setFillStyle("#000000")
				// ctx.fillText('18888888888', 90 * rpx, 220 * rpx)
				// 总机数
				ctx.setFontSize(12)
				ctx.textAlign = 'center'
				ctx.setFillStyle("#ff0000")
				ctx.fillText(that.list.machine,149 * rpx, 340 * rpx, 180 *rpx)
				// 楼宇数
				// ctx.setFontSize(12)
				// ctx.setFillStyle("#ff0000")
				// ctx.fillText(that.list.machinel,288 * rpx, 298 * rpx, 100 * rpx)
				// // 饭桌数
				// ctx.setFontSize(12)
				// ctx.setFillStyle("#ff0000")
				// ctx.fillText(that.list.machinez,40 * rpx, 314 * rpx, 100* rpx)
				// 中文-元
				ctx.setFontSize(10)
				ctx.textAlign = 'center'
				ctx.setFillStyle("#ff0000")
				ctx.fillText(that.list.payment2,280 * rpx, 603 * rpx, 200 *rpx)
				// 元
				ctx.setFontSize(10)
				ctx.textAlign = 'center'
				ctx.setFillStyle("#ff0000")
				ctx.fillText(that.list.payment,70 * rpx, 618 * rpx, 160 *rpx)
				// 年
				ctx.setFontSize(10)
				ctx.setFillStyle("#000000")
				ctx.fillText(that.list.year,74 * rpx, 1935 * rpx)
				// 月
				ctx.setFontSize(10)
				ctx.setFillStyle("#000000")
				ctx.fillText(that.list.month,114 * rpx, 1935 * rpx)
				// 月
				ctx.setFontSize(10)
				ctx.setFillStyle("#333333")
				ctx.fillText(that.list.day,146 * rpx, 1935 * rpx)
				ctx.draw(true, setTimeout(function() {
					wx.canvasToTempFilePath({
						x: 0,
						y: 0,
						width: 375 * rpx,
						height: 2082 * rpx,
						destWidth: 375 * wx.getSystemInfoSync().pixelRatio,
						destHeight: 2082 * wx.getSystemInfoSync().pixelRatio,
						fileType: 'jpg',
						quality: 1,
						canvasId: 'canvas',
						success: function(res) {
							that.contract = res.tempFilePath
							// 上传
							uni.uploadFile({
								url: 'https://www.gargle.top/api/common/upload',
								filePath: res.tempFilePath,
								name: 'file',
								header: {
									token: uni.getStorageSync('token')
								},
								formData: {
									'user': 'test',
								},
								success: (uploadFileRes) => {
									let img = JSON.parse(uploadFileRes.data)
									img = img.data.fullurl
									
									uni.request({
										url: getApp().globalData.url + 'api.user/contractup',
										method: 'POST',
										header: {
											token: uni.getStorageSync('token')
										},
										data: {
											img_url: img,
											id: that.id
										},
										success(res) {
											console.log(res)
										}
									})
								}
							});
						}
					})
				}, 500))
			},
			
			// 签名
			signature(){
				uni.redirectTo({
					url: '/pages/my/signature?id='+this.id
				})
			},
			
			// 下载
			download(){
				let that = this
				
				uni.showLoading({
					title: '下载中'
				})
				
				// #ifdef APP
				uni.saveImageToPhotosAlbum({
					filePath: that.contract,
					success: function () {
						uni.hideLoading()
						uni.showToast({
							title: '下载成功'
						})
					}
				});
				// #endif
				
				// #ifdef MP-WEIXIN
				uni.getImageInfo({
				  src: that.contract,
				  success (res) {
					uni.saveImageToPhotosAlbum({
						filePath: res.path,
						complete: function () {
							uni.hideLoading()
							uni.showToast({
								title: '下载成功'
							})
						}
					});
				  }
				})
				// #endif
			},
		}
	}
</script>

<style lang="scss">
	.button{
		position: fixed;
		bottom: 50rpx;
		left: 40rpx;
		width: 670rpx;
		height: 80rpx;
		background: linear-gradient(90deg, #F6E1B0 0%, #EDC66E 100%);
		border-radius: 200rpx;
		text-align: center;
		font-size: 28rpx;
		font-family: PingFangSC-Medium, PingFang SC;
		font-weight: 500;
		color: #FFFFFF;
		line-height: 80rpx;
	}
</style>
