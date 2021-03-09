<template>
	<view class="container">
		<view class="userinfo">
			<view class="userinfo-avatar">
				<open-data type="userAvatarUrl"></open-data>
			</view>
			<open-data type="userNickName"></open-data>
		</view>
		<view class='name'>
			<text>欢迎回来</text>
		</view>
		<view class='btn'>
			<button type='primary' open-type="getPhoneNumber" @getphonenumber="getPhoneNumber">
				微信一键登录
			</button>
		</view>
		<view class='btn btn1'>
			<button type='primary' @click="tellLogin">
				手机验证码登录
			</button>
		</view>
	</view>
</template>

<script>
	import config from '../../common/config';
	
	export default {
		name: "loginZh",
		props: {
			fromBase64: {
				type: String // Base64跳转路径
			},
			from: {
				type: String // 跳转路径
			},
			type: {
				type: String // 跳转方式
			}
		},
		data() {
			return {
				code: ''
			}
		},
		mounted() {
			this.wxLogin()
		},
		methods: {
			tellLogin() {
				uni.navigateTo({
					url: '/pages/telLogin/telLogin?loginType=2&from=' + this.fromBase64 + '&type=' + this.type
				});
			},
			wxLogin() {
				let _this = this;
				// wx登录
				uni.login({
					provider: 'weixin',
					success(res) {
						_this.code = res.code
						// console.log(_this.code)
					},
					fail() {
						uni.showToast({
							title: '微信授权登陆失败',
							icon: 'none'
						})
					}
				})
			},
			//第一授权获取用户信息===》按钮触发
			getPhoneNumber(e) {
				let _this = this;
				if (e.detail.errMsg.indexOf("getPhoneNumber:fail") != -1) {
					uni.showToast({
						title: '微信授权失败',
						icon: 'none'
					})
					return
				}
				//发起网络请求
				let data = {
					appId: config.appId,
					code: _this.code,
					extra: e.detail.iv,
					data: e.detail.encryptedData,
					social_type: 'WX_XCX'
				}
				uni.showLoading({
					title: '请稍等'
				})
				_this.$http('/auth/member/token', 'POST', data, 'application/x-www-form-urlencoded').then((res) => {
					if (!res.code) { // 一键登陆成功
						uni.setStorageSync('access_token', res.data.access_token)
						_this.$getUserInfo(() => {
							uni.hideLoading()
							if (uni.getStorageSync('userInfo').pickUpShopId) {
								uni[_this.type]({
									url: _this.from
								});
							} else {
								uni.reLaunch({
									url: '/pagesA/addressList/addressList?path=/pages/index/index'
								})
							}
						})
					} else {
						uni.hideLoading()
						uni.navigateTo({
							url: '/pages/telLogin/telLogin?loginType=1&from=' + _this.fromBase64 + '&type=' + _this.type + '&phone=' + res.data
						});
					}
				})
			}
		},

	}
</script>

<style lang='scss'>
	page {
		background: #fff;
	}

	.userinfo {
		position: relative;
		color: #000;
		display: flex;
		flex-direction: column;
		align-items: center;
	}

	.userinfo-avatar {
		overflow: hidden;
		display: block;
		width: 200rpx;
		height: 200rpx;
		margin: 20rpx;
		margin-top: 50rpx;
		border-radius: 50%;
	}

	.userinfo {
		font-size: 14px;
		border-radius: 40%;
	}

	.name {
		text-align: center;
		font-size: 26rpx;
		margin-top: 5rpx;
		margin-bottom: 80rpx;
	}

	.btn {
		padding: 20rpx 50rpx;
	}

	.btn button {
		background: #1aad19;
	}

	.btn .button-hover {
		background: #25bb24;
	}

	.btn1 button,
	.btn1 .button-hover {
		background: none;
		color: #76a7de;
		border: 1px solid #76a7de;
	}

	.btn1 button::after {
		border: none
	}
</style>
