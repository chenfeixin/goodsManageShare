<template>
	<view>
		<view class="box">
			<view class="box_title">《合作协议书》</view>
			<view class="boxes">
				<view class="boxes_l">姓名：</view>
				<input type="text" maxlength="8" class="boxes_r" @input="inputName">
			</view>
			<view class="boxes">
				<view class="boxes_l">身份证号：</view>
				<input type="idcard" maxlength="18" class="boxes_r" @input="inputNumber">
			</view>
			<!-- <view class="boxes">
				<view class="boxes_l">手机号：</view>
				<input type="number" maxlength="11" class="boxes_r" @input="inputPhone">
			</view> -->
			<view class="title">签名： <text>请在下面手写签名</text> <text style="float: right;color: #2200FF;" @tap='clearClick' v-if="confirmcClick">重填</text></view>
			<view class="signature" v-if="!signImage">
				<canvas class='firstCanvas' style="width: 666rpx;height: 358rpx;" canvas-id="firstCanvas" @touchmove='move' @touchstart='start($event)' @touchend='end'
				@touchcancel='cancel' @longtap='tap' disable-scroll='true' @error='error'>
				</canvas>
			   
				<view class="caozuo">
					<view class="caozuo_l" @tap='clearClick'>
						重签
					</view>
					<view class="caozuo_r" @click="overSign">
						完成签名
					</view>
				</view>
			</view>
			<image class="signImage" :src="signImage" v-if="signImage"></image>
			<view class="confirm" v-if="signImage&&confirmcClick" @click="confirm">生成合同</view>
			<!-- <view class="confirm" v-else-if="signImage&&!confirmcClick">提交</view> -->
			<canvas canvas-id="canvas" style="width:750rpx;height:4163rpx;position:fixed;left:9000px;"></canvas>
		</view>
		<block v-if="contract">	
			<image :src="contract" style="width: 750rpx;height: 4163rpx;"></image>
			<view class="footer">
				<view class="footer_l" @click="again">重填</view>
				<view class="footer_r" @click="affirm">核对无误</view>
			</view>
		</block>
	</view>
</template>

<script>
	var content = null;
	var touchs = [];
	var canvasw = 0;
	var canvash = 0;
	var _that;
	export default {
		data() {
			return {
				signImage: '', //签名图片
				isEnd: false ,// 是否签名
				name: '', //姓名
				number: '', //身份证卡号
				phone: '', //手机号
				id: '', //合同id
				image: '',
				contract: '',
				confirmcClick: true,
				start_year: 0,
				start_month: 0,
				start_day: 0,
				end_year: 0,
				end_month: 0,
				end_day: 0,
			};
		},
		onLoad(options) {
			_that = this
			let that = this
			
			that.id = options.id
			//获得Canvas的上下文
			content = uni.createCanvasContext('firstCanvas',_that)
			
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
						// console.log(that.list)
						let start = res.data.data.date_start
						that.start_year = start.substring(0,4)
						that.start_month = start.substring(5,7)
						that.start_day = start.substring(8,10)
						
						let end = res.data.data.date_end
						that.end_year = end.substring(0,4)
						that.end_month = end.substring(5,7)
						that.end_day = end.substring(8,10)
						console.log(that.end_year,that.end_month,that.end_day)
						
						// 下载合同图
						uni.getImageInfo({
							src: 'https://www.gargle.top/img/new_signature.png',
							success: function (image) {
								that.image = image.path
							}
						})
					}
				}
			})
		},
		methods: {
			// 输入姓名
			inputName(e){
				this.name = e.detail.value
			},
			//输入身份证
			inputNumber(e){
				this.number = e.detail.value
			},
			inputPhone(e){
				this.phone = e.detail.value
			},
			
			overSign: function() {
				if (_that.isEnd) {
					uni.canvasToTempFilePath({
						canvasId: 'firstCanvas',
						success: function(res) {
							//打印图片路径
							console.log(res.tempFilePath)
							console.log('完成签名')
							//设置图片
							_that.signImage = res.tempFilePath;
						}
					})
				} else {
					uni.showToast({
						title: '请先完成签名',
						icon: "none",
						duration: 1500,
						mask: true
					})
				}
			
			},
			
			// 画布的触摸移动开始手势响应
			start: function(event) {
				// console.log(event)
				// console.log("触摸开始" + event.changedTouches[0].x)
				// console.log("触摸开始" + event.changedTouches[0].y)
				//获取触摸开始的 x,y
				let point = {
					x: event.changedTouches[0].x,
					y: event.changedTouches[0].y
				}
				// console.log(point)
				touchs.push(point);
			
			},
			// 画布的触摸移动手势响应
			move: function(e) {
				let point = {
					x: e.touches[0].x,
					y: e.touches[0].y
				}
				// console.log(point)
				touchs.push(point)
				if (touchs.length >= 2) {
					this.draw(touchs)
				}
			},
			
			// 画布的触摸移动结束手势响应
			end: function(e) {
				// 设置为已经签名
				this.isEnd = true;
				console.log('end')
				// 清空轨迹数组
				touchs.pop()
				// for (let i = 0; i < touchs.length; i++) {
				// 	touchs.pop()
				// }
			
			},
			
			//绘制
			draw: function(touchs) {
				let point1 = touchs[0]
				let point2 = touchs[1]
				touchs.shift()
				//设置线的颜色
				content.setStrokeStyle("#000")
				//设置线的宽度
				content.setLineWidth(5)
				//设置线两端端点样式更加圆润
				content.setLineCap('round')
				//设置两条线连接处更加圆润
				content.setLineJoin('round')
				content.moveTo(point1.x, point1.y)
				content.lineTo(point2.x, point2.y)
				content.stroke()
				content.draw(true)
			},
			//清除操作
			clearClick: function() {
				// 设置为未签名
				this.isEnd = false
				_that.signImage = null
				//清除画布
				content.clearRect(0, 0, 666, 385)
				content.draw(true)
			},
			
			// 提交
			confirm(){
				let that = this
				
				if(that.name){
					if(that.number){
						if(that.signImage){
							if(that.confirmcClick){
								that.save()
								that.confirmcClick = false
							}
						}else{
							uni.showToast({
								title: '未检测到签名，请重新签名',
								icon: 'none'
							})
						}
					}else{
						uni.showToast({
							title: '请输入身份证',
							icon: 'none'
						})
					}
				}else{
					uni.showToast({
						title: '请输入姓名',
						icon: 'none'
					})
				}
			},
			
			// 下载海报
			save() {
				let that = this
				
				let rpx = null
				uni.getSystemInfo({
					success: function(res) {
						rpx = res.windowWidth / 375
					},
				})
				
				uni.showLoading({
					title: '合同生成中~',
					mask: true
				})
				
				// let date = new Date()
				// let month  = date.getMonth() >9 ? date.getMonth() : '0'+date.getMonth()
				// let day = date.getDate() > 9 ? date.getDate() : '0'+ date.getDate()
				var ctx = uni.createCanvasContext('canvas')
				// 设置背景
				ctx.setFillStyle('#FFFFFF')
				ctx.fillRect(0, 0, 375 * rpx, 2082 * rpx)
				// 背景图片
				ctx.drawImage(that.image, 0 * rpx, 0 * rpx, 375 * rpx, 2082 * rpx)
				// 合同编号
				ctx.setFontSize(11)
				ctx.textAlign = 'left'
				ctx.setFillStyle("#000000")
				ctx.fillText(that.list.no, 62 * rpx, 22.5 * rpx)
				// 名
				ctx.textAlign="start"
				ctx.setFontSize(10)
				ctx.setFillStyle("#000000")
				ctx.fillText(that.name, 140 * rpx, 143 * rpx)
				// 身份证号码
				ctx.textAlign="start"
				ctx.setFontSize(10)
				ctx.setFillStyle("#000000")
				ctx.fillText(that.number, 140 * rpx, 156 * rpx)
				// // 手机号码
				// ctx.setFontSize(12)
				// ctx.setFillStyle("#000000")
				// ctx.fillText(that.phone, 66 * rpx, 147 * rpx, 200 * rpx)
				// 总机数
				ctx.setFontSize(12)
				ctx.textAlign = 'center'
				ctx.setFillStyle("#ff0000")
				ctx.fillText(that.list.machine,149 * rpx, 340 * rpx, 180 *rpx)
				// 开始年
				ctx.setFontSize(10)
				// ctx.textAlign="start"
				ctx.setFillStyle("#ff0000")
				ctx.fillText(that.start_year,166 * rpx, 423 * rpx)
				// 开始月
				ctx.setFontSize(10)
				// ctx.textAlign="start"
				ctx.setFillStyle("#ff0000")
				ctx.fillText(that.start_month,198 * rpx, 423 * rpx)
				// 开始日
				ctx.setFontSize(10)
				// ctx.textAlign="start"
				ctx.setFillStyle("#ff0000")
				ctx.fillText(that.start_day,229 * rpx, 423 * rpx)
				// 结束年
				ctx.setFontSize(10)
				// ctx.textAlign="start"
				ctx.setFillStyle("#ff0000")
				ctx.fillText(that.end_year,269 * rpx, 423 * rpx)
				// 结束月
				ctx.setFontSize(10)
				// ctx.textAlign="start"
				ctx.setFillStyle("#ff0000")
				ctx.fillText(that.end_month,302 * rpx, 423 * rpx)
				// 结束日
				ctx.setFontSize(10)
				// ctx.textAlign="start"
				ctx.setFillStyle("#ff0000")
				ctx.fillText(that.end_day,331 * rpx, 423 * rpx)
				// // 楼宇数
				// ctx.setFontSize(12)
				// ctx.setFillStyle("#ff0000")
				// ctx.fillText(that.list.machinel,288 * rpx, 298 * rpx, 100 * rpx)
				// // 饭桌数
				// ctx.setFontSize(12)
				// ctx.setFillStyle("#ff0000")
				// ctx.fillText(that.list.machinez,41 * rpx, 314 * rpx, 100* rpx)
				// 中文-元
				ctx.setFontSize(10)
				ctx.textAlign = 'center'
				ctx.setFillStyle("#ff0000")
				ctx.fillText(that.list.payment2,280 * rpx, 603 * rpx, 200 *rpx)
				// 元
				ctx.setFontSize(12)
				ctx.textAlign = 'center'
				ctx.setFillStyle("#ff0000")
				ctx.fillText(that.list.payment,70 * rpx, 618 * rpx, 160 *rpx)
				// 年
				ctx.setFontSize(10)
				ctx.textAlign = 'center'
				ctx.setFillStyle("#000000")
				ctx.fillText(that.list.year,74 * rpx, 1935 * rpx)
				// 月
				ctx.setFontSize(10)
				ctx.textAlign = 'center'
				ctx.setFillStyle("#000000")
				ctx.fillText(that.list.month,114 * rpx, 1935 * rpx)
				// 日
				ctx.setFontSize(10)
				ctx.textAlign = 'center'
				ctx.setFillStyle("#333333")
				ctx.fillText(that.list.day,146 * rpx, 1935 * rpx)
				// 乙方签名
				ctx.drawImage(that.signImage, 136 * rpx, 1982 * rpx, 55 * rpx, 32 * rpx)
				// // 乙方 -月
				// ctx.setFontSize(10)
				// ctx.setFillStyle("#000000")
				// ctx.fillText(that.list.month,56 * rpx, 764 * rpx)
				// // 乙方-日
				// ctx.setFontSize(10)
				// ctx.setFillStyle("#000000")
				// ctx.fillText(that.list.day,77 * rpx, 764 * rpx)
				ctx.draw(true, setTimeout(function() {
					uni.canvasToTempFilePath({
						x: 0,
						y: 0,
						width: 375 * rpx,
						height: 2082 * rpx,
						destWidth: 375 * uni.getSystemInfoSync().pixelRatio,
						destHeight: 2082 * uni.getSystemInfoSync().pixelRatio,
						fileType: 'jpg',
						quality: 1,
						canvasId: 'canvas',
						success: function(res) {
							uni.hideLoading()
							that.contract = res.tempFilePath
							uni.showToast({
								title: '请往下滑动核对您的合同',
								icon: 'none'
							})
						}
					})
				}, 500))
			},
			
			// 重填
			again(){
				let that = this
				
				that.signImage = ''
				that.contract = ''
				that.confirmcClick = true
			},
			
			// 核对无误
			affirm(){
				let that = this
				
				uni.showModal({
					title: '温馨提示',
					content: '您是否确定合同核对无误了？',
					success: function (res) {
						if (res.confirm) {
							// 上传
							uni.uploadFile({
								url: 'https://www.gargle.top/api/common/upload',
								filePath: that.contract,
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
											id: that.id,
											status: 2
										},
										success(res) {
											uni.redirectTo({
												url: '/pages/my/agreement?id='+that.id
											})
										}
									})
								}
							});
						} else if (res.cancel) {
							console.log('用户点击取消');
						}
					}
				});
			},
		}
	}
</script>

<style lang="scss">
	page{
		background: #F4F4F4;
	}
	.box{
		padding-bottom: 24rpx;
		margin: 24rpx;
		width: 702rpx;
		// height: 1000rpx;
		background: #FFFFFF;
		border-radius: 8rpx;
		overflow: hidden;
		
		&_title{
			margin-top: 40rpx;
			margin-bottom: 48rpx;
			height: 44rpx;
			font-size: 32rpx;
			font-family: PingFangSC-Medium, PingFang SC;
			font-weight: 500;
			color: #2200FF;
			line-height: 44rpx;
			text-align: center;
		}
		.boxes{
			margin: 32rpx 28rpx 0rpx 16rpx;
			height: 76rpx;
			overflow: hidden;
			
			&_l{
				float: left;
				width: 142rpx;
				height: 76rpx;
				font-size: 28rpx;
				font-family: PingFangSC-Regular, PingFang SC;
				font-weight: 400;
				color: #333333;
				line-height: 76rpx;
				// text-align: right;
			}
			&_r{
				float: left;
				width: 510rpx;
				height: 72rpx;
				border-radius: 38rpx;
				border: 2rpx solid #999999;
				text-align: center;
				font-size: 32rpx;
				font-family: PingFangSC-Regular, PingFang SC;
				font-weight: 400;
				color: #333333;
				line-height: 72rpx;
			}
		}
		.title{
			margin: 32rpx 20rpx 0rpx 16rpx;
			height: 40rpx;
			font-size: 28rpx;
			font-family: PingFangSC-Regular, PingFang SC;
			font-weight: 400;
			color: #333333;
			line-height: 40rpx;
			overflow: hidden;
			
			text{
				font-size: 20rpx;
				font-family: PingFangSC-Regular, PingFang SC;
				font-weight: 400;
				color: #333333;
				line-height: 40rpx;
			}
		}
		.signature{
			margin-left: 18rpx;
			width: 666rpx;
			height: 358rpx;
			border: 2rpx solid #999999;
		}
		.caozuo{
			margin: 40rpx 60rpx;
			
			&_l{
				float: left;
				width: 240rpx;
				height: 64rpx;
				background: #CBA55F;
				border-radius: 32rpx;
				text-align: center;
				font-size: 28rpx;
				font-family: PingFangSC-Medium, PingFang SC;
				font-weight: 500;
				color: #FFFFFF;
				line-height: 64rpx;
			}
			&_r{
				float: right;
				width: 240rpx;
				height: 64rpx;
				background: #CBA55F;
				border-radius: 32rpx;
				text-align: center;
				font-size: 28rpx;
				font-family: PingFangSC-Medium, PingFang SC;
				font-weight: 500;
				color: #FFFFFF;
				line-height: 64rpx;
			}
		}
		.signImage{
			margin-left: 18rpx;
			width: 666rpx;
			height: 358rpx;
			border: 2rpx solid #999999;
		}
		.confirm{
			margin: 84rpx 20rpx 0rpx;
			width: 662rpx;
			height: 80rpx;
			background: #CBA55F;
			border-radius: 40rpx;
			text-align: center;
			font-size: 28rpx;
			font-family: PingFangSC-Medium, PingFang SC;
			font-weight: 500;
			color: #FFFFFF;
			line-height: 80rpx;
		}
	}
	.footer{
		margin: 20rpx 60rpx 150rpx;
		height: 64rpx;
		overflow: hidden;
		
		&_l{
			float: left;
			width: 280rpx;
			height: 64rpx;
			background: linear-gradient(90deg, #F6E1B0 0%, #EDC66E 100%);
			border-radius: 32rpx;
			text-align: center;
			font-size: 28rpx;
			font-family: PingFangSC-Medium, PingFang SC;
			font-weight: 500;
			color: #FFFFFF;
			line-height: 64rpx;
		}
		&_r{
			float: right;
			width: 280rpx;
			height: 64rpx;
			background: linear-gradient(90deg, #F6E1B0 0%, #EDC66E 100%);
			border-radius: 32rpx;
			text-align: center;
			font-size: 28rpx;
			font-family: PingFangSC-Medium, PingFang SC;
			font-weight: 500;
			color: #FFFFFF;
			line-height: 64rpx;
		}
	}
</style>