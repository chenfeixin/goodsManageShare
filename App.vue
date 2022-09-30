<script>
	export default {
		onLaunch: function() {
			if(!uni.getStorageSync('user')){
				uni.reLaunch({
					url: '/pages/login/login'
				})
			}else{
				uni.request({
					url: this.globalData.url + 'api.login/isLogins',
					method: 'POST',
					data: {
						uid: uni.getStorageSync('user').id
					},
					success(res) {
						if(res.data.code==1){
							console.log(res)
						}else{
							uni.reLaunch({
								url: '/pages/login/login'
							})
						}
					}
				})
			}
		},
		onShow: function() {
			console.log('App Show')
		},
		onHide: function() {
			console.log('App Hide')
		},
		globalData: {
			url: 'https://www.gargle.top/addons/shop/',
		},
	}
</script>

<style>
	/*每个页面公共css */
</style>
