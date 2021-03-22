<template>
	<view class="statusBars">
		<view :style="{height: statusBarHeight + 'px'}"></view>
		<view class="topbar-container flex up-center">
			<view class="btn-box">
				恒昌大学
			</view>
		</view>
	</view>
</template>

<script>
	const app = getApp()
	export default {
		props: {
			title: {
				type: String
			}
		},
		data() {
			return {
				loading: true, // 是否显示骨架屏组件
				statusBarHeight: app.globalData.statusBarHeight
			}
		},
		mounted() {

		},
		methods: {
			search() {
				uni.navigateTo({
					url: `/pagesA/search/search`
				})
			},
			back() {
				uni.navigateBack()
			},
			goHomeHandler() {
				uni.navigateTo({
					url: "/pages/douyin/index"
				});
			},
		}
	}
</script>

<style>
	.statusBars {
		width: 100%;
	}
	.topbar-container {
		height: 80rpx;
	}
	.btn-box {
		font-size: 32rpx;
		width: 100%;
		text-align: center;
		color: #FFFFFF;
	}
</style>
