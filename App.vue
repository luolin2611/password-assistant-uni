<script>
	export default {
		onLaunch: function() {
			console.log('App Launch')
			// 检查更新
			this.checkUpdate()
			// 设置状态栏高度
			const systemInfo = uni.getSystemInfoSync()
			
			// #ifdef MP-WEIXIN
			const menuButtonInfo = uni.getMenuButtonBoundingClientRect()
			// 设置自定义导航栏高度
			const navBarHeight = (menuButtonInfo.top - systemInfo.statusBarHeight) * 2 + menuButtonInfo.height
			// #endif
			
			// 设置全局变量
			getApp().globalData = {
				...getApp().globalData,
				statusBarHeight: systemInfo.statusBarHeight,
				// #ifdef MP-WEIXIN
				navBarHeight: navBarHeight,
				windowTop: systemInfo.statusBarHeight + navBarHeight
				// #endif
				// #ifdef H5
				navBarHeight: 44,
				windowTop: 44
				// #endif
			}
		},
		onShow: function() {
			console.log('App Show')
		},
		onHide: function() {
			console.log('App Hide')
		},
		methods: {
			// 检查应用更新
			checkUpdate() {
				// #ifdef MP-WEIXIN
				if (uni.canIUse('getUpdateManager')) {
					const updateManager = uni.getUpdateManager()
					updateManager.onCheckForUpdate(function(res) {
						if (res.hasUpdate) {
							updateManager.onUpdateReady(function() {
								uni.showModal({
									title: '更新提示',
									content: '新版本已经准备好，是否重启应用？',
									success: function(res) {
										if (res.confirm) {
											updateManager.applyUpdate()
										}
									}
								})
							})
						}
					})
				}
				// #endif
			}
		}
	}
</script>

<style>
	/* 全局样式 */
	page {
		font-size: 28rpx;
		color: #333;
		background-color: #f8f8f8;
	}

	/* 通用样式类 */
	.flex {
		display: flex;
	}

	.flex-column {
		flex-direction: column;
	}

	.justify-center {
		justify-content: center;
	}

	.align-center {
		align-items: center;
	}

	/* 边距类 */
	.mt-10 {
		margin-top: 10rpx;
	}

	.mb-10 {
		margin-bottom: 10rpx;
	}

	.ml-10 {
		margin-left: 10rpx;
	}

	.mr-10 {
		margin-right: 10rpx;
	}

	/* 文本溢出省略号 */
	.text-ellipsis {
		overflow: hidden;
		text-overflow: ellipsis;
		white-space: nowrap;
	}
</style>
