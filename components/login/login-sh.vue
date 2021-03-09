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
			<button type='primary' @click="checkLogin">
				微信一键登录
			</button>
		</view>
		<view class='btn btn1'>
			<button type='primary' @click="tellLogin">
				手机验证码登录
			</button>
		</view>
		<view class='mask_layer_all' v-if="ifShowMaskCheckLogin">
			<view class='mask_layer' bindtap='modal_click_Hidden' />
			<view class='modal_box'>
				<view class='content'>
					<view class='header'>
						<image src='../../static/logo.png'></image>
					</view>
					<view class='header-text'>
						<text>六谷大药房</text>
					</view>
				</view>
				<view class='content1'>
					<view class='content1-t'>
						该程序将获取一下授权：
					</view>
					<view class='content1-n'>
						· 获得您的公开信息（昵称，头像等）
					</view>
				</view>
				<view class='btn_all flex'>
					<view class='btn btn_l'>
						<button class='mask_layer_btn' @click="cancel">取消</button>
					</view>
					<view class='btn'>
						<button class='mask_layer_btn' open-type="getPhoneNumber" @getphonenumber="getPhoneNumber">允许</button>
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	import config from '../../common/config';
	
	export default {
		name: "loginSh",
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
				code: '',
				ifShowMaskCheckLogin: 0
			}
		},
		onShow() {
			this.wxLogin()
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
			cancel() {
				this.ifShowMaskCheckLogin = 0
			},
			checkLogin() {
				this.ifShowMaskCheckLogin = 1
			},
			tellLoginuserInfo(e) {
				let _this = this
				if (e.detail.errMsg.indexOf("getUserInfo:fail") != -1) {
					uni.showToast({
						title: '授权后才能登陆',
						icon: 'none'
					})
					return
				}
				_this.detail = e.detail
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
			//第一授权获取手机号码===》按钮触发
			getPhoneNumber(e) {
				let _this = this
				_this.ifShowMaskCheckLogin = 0
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
					uni.hideLoading()
					if (!res.code) { // 一键登陆成功
						uni.setStorageSync('access_token', res.data.access_token)
						_this.$getUserInfo(() => {
							uni[_this.type]({
								url: _this.from
							});
						})
					} else {
						if (res.msg === 'User account is locked' || res.msg === '用户帐号已被锁定') {
							uni.showToast({
								title: '用户帐号已被锁定',
								icon: 'none'
							})
						} else if (res.msg === 'Bad credentials') {
							uni.showToast({
								title: '账号异常，稍后再试！',
								icon: 'none'
							})
						} else {
							uni.navigateTo({
								url: '/pages/telLogin/telLogin?loginType=1&from=' + _this.fromBase64 + '&type=' + _this.type + '&phone=' +
									res.data
							});
						}
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

	.mask_layer_all .mask_layer {
		width: 100%;
		height: 100%;
		position: fixed;
		z-index: 999;
		left: 0;
		top: 0;
		background: #000;
		opacity: 0.5;
		overflow: hidden;
	}

	.mask_layer_all .modal_box {
		width: 76%;
		overflow: hidden;
		position: fixed;
		top: 50%;
		left: 0%;
		z-index: 1001;
		background: #fff;
		margin: -280rpx 12% 0 12%;
		border-radius: 10rpx;
	}

	.mask_layer_all .title {
		padding: 28rpx 0rpx;
		text-align: center;
		font-size: 36rpx;
		background-color: gazure;
		border-bottom: 1px solid #f5f5f5;
	}

	.mask_layer_all .content {
		overflow-y: scroll;
		padding: 40rpx;
		color: #333333;
		text-align: center;
		line-height: 48rpx;
		/*超出父盒子高度可滚动*/
	}

	.mask_layer_all .input_show1 {
		margin: 0 auto;
		width: 80%;
		margin-left: 10%;
		font-size: 32rpx;
		text-align: center;
	}

	.mask_layer_all .btn_all .btn {
		padding: 10rpx 0;
		flex: 1;
	}

	.mask_layer_all .btn_l {
		border-right: 1px solid #f5f5f5;
		border-top: none;
	}

	.mask_layer_all .btn_all .btn button.mask_layer_btn {
		color: #43b031;
		background: none;
	}

	.mask_layer_all .btn_all .btn_l button.mask_layer_btn {
		color: #333;
	}

	.mask_layer_all .btn button.mask_layer_btn::after {
		border: none;
	}

	.mask_layer_all .btn_all {
		border-top: 1px solid #f5f5f5;
	}
	.mask_layer_all .header {
		text-align: center;
	}

	.mask_layer_all .header image {
		width: 100rpx;
		height: 100rpx;
		border-radius: 50%
	}

	.mask_layer_all .header-text {
		text-align: center;
		margin-top: 0rpx;
		color: #333333;
	}
	.mask_layer_all .content1{
		width: 80%;
		margin: 0 auto;
		border-top: 1px #f5f5f5 solid;
		padding: 20rpx 0;
		line-height: 48rpx;
	}
	.mask_layer_all .content1-t{
		color: #333333;
		font-size: 28rpx;
	}
	.mask_layer_all .content1-n{
		font-size: 24rpx;
		color: #999999;
	}
</style>
