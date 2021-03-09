<script>
	export default {
		globalData: {
			statusBarHeight: null, //状态栏高度
			topbarHeight: null //包括状态栏总高度
		},
		onLaunch: function() {
			// this.$wordbook((res) => {
			// 	if (res.every((e) => e === 1)) {
			// 		this.$isResolve()
			// 	} else {
			// 		this.$message.error('数据初始化失败')
			// 	}
			// })
			uni.getSystemInfo({ //获取系统信息
				success: e => {
					this.globalData.statusBarHeight = e.statusBarHeight
					this.globalData.topbarHeight = e.statusBarHeight + 44
				}
			})
			// #ifdef MP-WEIXIN
			const updateManager = uni.getUpdateManager();
			updateManager.onCheckForUpdate(function(res) {
				// 请求完新版本信息的回调
				if (res.hasUpdate) {
					updateManager.onUpdateReady(function(res2) {
						uni.showModal({
							title: '更新提示',
							content: '发现新版本，是否重启应用?',
							success(res2) {
								if (res2.confirm) {
									// 新的版本已经下载好，调用 applyUpdate 应用新版本并重启
									updateManager.applyUpdate();
								}
							}
						});
					});
				}
			});

			updateManager.onUpdateFailed(function(res) {
				// 新的版本下载失败
				uni.showModal({
					title: '提示',
					content: '检查到有新版本，但下载失败，请检查网络设置',
					success(res) {
						if (res.confirm) {
							// 新的版本已经下载好，调用 applyUpdate 应用新版本并重启
							updateManager.applyUpdate();
						}
					}
				});
			});
			// #endif
		},
		onShow: function() {},
		onHide: function() {
			// console.log('App Hide')
		},
		methods: {}
	}
</script>
<style lang="scss">
	/* 注意要写在第一行，同时给style标签加入lang="scss"属性 */
	@import "uview-ui/index.scss";
</style>