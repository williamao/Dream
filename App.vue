<script>
	export default {
		onLaunch: function() {
			console.log('App Launch')
				if (!wx.cloud) {
					console.error('请使用 2.2.3 或以上的基础库以使用云能力')
				} else {
					wx.cloud.init({
						traceUser: true,
						env: 'no1-3gffzgxea0abc9f8' //写自己配置的后台环境
					})
				}

		},
		onShow: function() {
			console.log('App Show')
		},
		onHide: function() {
			console.log('App Hide')
		}
	}
</script>

<style>
	/*每个页面公共css */
</style>
