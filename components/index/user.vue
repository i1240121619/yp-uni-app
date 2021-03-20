<template>
	<view>
		<view class="user_box">
			<view class="center_box">
				<view v-if="userToken">
					登录
				</view>
				<view class="user_img" @tap="goLogin" v-else>
					未登录
				</view>
				<view class="text_center c_fff mgt10" style="height: 2000px;">
					<text class="font33">{{userInfo.nickname || userInfo.mobile}}</text>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				userToken: uni.getStorageSync('userToken'),
				userInfo: uni.getStorageSync('userInfo')
			};
		},
		methods: { 
			onPageshow() {
				console.log('user')
				if (!uni.getStorageSync('userToken')) {
					
				} else {

				}
			},
			goLogin() {
				// current: index:首页/course:课程/study:学习/enterprise:企业/user:我的
				if (!this.$checkLogin(this.$Base64.encodeURI('/pages/index/index?current=user'), 'navigateTo')) return // 判断有没有登陆，没有登陆跳转到登陆页面 一定写在onLoad生命周期函数里面
			}
		}
	};
</script>
<style lang="scss" scoped>
	.user_box {
		width: 100%;
		box-sizing: border-box;
		padding: 40rpx 20rpx;
		height: 277rpx;
		background: #2388EC;
		display: flex;

		justify-content: space-between;
	}

	.is_vip {
		height: 247rpx;
	}

	.user_img {
		display: block;
		width: 158rpx;
		height: 158rpx;
		line-height: 158rpx;
		text-align: center;
		border-radius: 50%;
		color: #999;
		margin: 0 auto;
		background-color: #fff;
	}
</style>
