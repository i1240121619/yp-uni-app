<template>
	<view class="statusBars">
		<view class="topbar-container" :style="{marginTop: statusBarHeight + 'px',height: (topbarHeight-statusBarHeight) + 'px'}">
			<view class="topbar-icons">
				<view class="topbar-back" @click="back">
					<image class="back-image" src="https://gw.alicdn.com/tfs/TB1H1hKNUz1gK0jSZLeXXb9kVXa-102-102.png"></image>
				</view>
				<view class="topbar-home" @click="goHomeHandler">
					<image class="home-image" src="https://gw.alicdn.com/tfs/TB1LnFgOAL0gK0jSZFtXXXQCXXa-102-102.png"></image>
				</view>
			</view>
			<view class="btn-box">
				<view class="search" @click="search">
					<view class="search-box">
						<view class="text">{{defaultKeyword}}</view>
					</view>
				</view>
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
				defaultKeyword: "搜名称或关键词",
				customTitle: "unapp脚手架",
				statusBarHeight: app.globalData.statusBarHeight,
				topbarHeight: app.globalData.topbarHeight
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
		position: fixed;
		top: 0;
		left: 0;
		z-index: 999;
		background-color: #fff;
	    background-image: url('https://gw.alicdn.com/tfs/TB1lrelQxz1gK0jSZSgXXavwpXa-1125-252.png');
		background-repeat: no-repeat;
	    background-size: 100% 100%;
	}
	.topbar-icons {
		border-radius: 62rpx;
		display: flex;
		align-items: center;
		height: 64rpx;
		margin-left: 18rpx;
		border: 2rpx solid #f6f6f6;
		box-sizing: border-box;
		position: absolute;
		z-index: 2;
	}

	.topbar-back {
		border-right: 2rpx solid #f6f6f6;
	}

	.topbar-container {
		display: flex;
		align-items: center;
		width: 100%;
		height: 100rpx;
		position: relative;
	}

	.topbar-home,
	.topbar-back {
		width: 84rpx;
		display: flex;
		align-items: center;
		justify-content: center;
	}

	.back-image {
		width: 34rpx;
		height: 34rpx;
	}

	.home-image {
		width: 34rpx;
		height: 34rpx;
	}

	.btn-box {
		display: flex;
		align-items: center;
		justify-content: center;
		position: absolute;
		left: 0;
		right: 0;
		top: 0;
		bottom: 0;
		margin: auto;
		z-index: 1;
	}

	.btn-box .txt {
		color: #0D0D0D;
	}

	.top-play-icon {
		width: 48rpx;
		height: 48rpx;
		margin-right: 6rpx;
		margin-top: 2rpx;
	}
	
	.search-box {
	    height: 60rpx;
	    background: rgba(255,255,255,0.12);
	    border-radius: 30rpx;
	    overflow: hidden;
	    margin-left: 0rpx;
	    width: 320rpx;
	    display: flex;
	    align-items: center;
	    justify-content: center;
	    background-image: linear-gradient(90deg,rgba(52,207,255,0.2) 0%,rgba(155,123,255,0.2) 51%,rgba(255,122,161,0.2) 100%);
	}
	.search-box .text {
	    font-size: 26rpx;
	    color: #666;
	    text-align: center;
	    flex: 1;
	    width: 0;
	    margin-left: 20rpx;
	    white-space: nowrap;
	    text-overflow: ellipsis;
	}
	.search-box .icon {
	    width: 32rpx;
	    height: 32rpx;
	    margin-right: 20rpx;
	}
</style>
