<template>
	<view class="container">
		<view class='header'>
			<image src='../../static/logo.png'></image>
		</view>
		<view class='header-text'>
			<text>六谷大药房</text>
		</view>
		<view class='content'>
			<view class="tel">
				<view class="tel-1">
					<input v-model="phone" maxlength="11" placeholder="请输入手机号码" />
				</view>
				<view class="del iconfont" @click="del" v-if="phone"></view>
			</view>
			<view class="telcodeall flex">
				<view class="telcode">
					<view class="tel-1">
						<input v-model="phoneCode" placeholder="请输入手机验证码" />
					</view>
				</view>
				<view class="verification-code">
					<view v-show="show" class="form-item-right"><button class="getUserInfo" open-type="getUserInfo" @getuserinfo="userInfo"><text
						v-show="show1">获取验证码</text><text v-show="!show1">重发验证码</text></button></view>
					<view v-show="!show" class="form-item-right">{{count}}s</view>
				</view>
			</view>
		</view>
		<view class='btn'>
			<button type='primary' @click="wxLogin">
				确定
			</button>
		</view>
		<view class='xy'>
			<view>点击确定，即表示已阅读并同意<span class="xy1" @click="serviceTerms">《注册会员服务条款》</span><span class="xy1" @click="payTerms">《支付协议》</span></view>
		</view>
	</view>
</template>

<script>
	import config from '../../common/config';
	import reg from '../../common/reg';

	export default {
		data() {
			return {
				fromBase64: '', // Base64跳转路径
				loginType: '', // 登陆方式
				from: '', // 跳转路径
				type: '', // 跳转方式
				phone: '',
				phoneCode: '',
				code: '',
				count: '',
				timer: '',
				show: true,
				show1: true,
				userAll: '',
				shopId: ''
			}
		},
		onLoad(option) {
			this.loginType = option.loginType // 1:一键登陆/2:手机登陆
			this.fromBase64 = option.from ? option.from : this.$Base64.encodeURI('/pages/index/index')
			this.from = option.from ? this.$Base64.decode(option.from) : '/pages/index/index'
			this.type = option.type || 'reLaunch'
			this.phone = option.phone === 'null' ? '' : option.phone
			if (option.q) {
				let url = decodeURIComponent(option.q)
				this.shopId = this.$getQueryString(url, 'shopId') || ''
				this.loginType = '2'
				// uni.showToast({
				// 	title: this.shopId,
				// 	icon: 'none',
				// 	duration: 100000
				// })
			}
		},
		methods: {
			userInfo() {
				let _this = this;
				uni.getUserInfo({
					provider: 'weixin',
					success: (e) => {
						_this.userAll = e
						if (!_this.phone || !reg.tel_reg.test(_this.phone)) {
							uni.showToast({
								title: '手机号码不能为空或不正确',
								icon: 'none'
							})
							return
						}
						_this.getCodeAjax()
					},
					fail: (err) => {
						uni.showToast({
							title: '微信授权登陆失败',
							icon: 'none'
						})
					}
				});
			},
			wxLogin() {
				let _this = this;
				// wx登录
				uni.login({
					provider: 'weixin',
					success(res) {
						_this.code = res.code
						_this.submit()
						// console.log(res)
						console.log(res)
					},
					fail(err) {
						uni.showToast({
							title: '微信授权登陆失败',
							icon: 'none'
						})
					}
				})
			},
			chooseStore() {
				let _this = this;
				let data = {
					shopId: _this.shopId
				};
				_this.$http('/custom/mallShop/getPickUpShop', 'GET', data).then((res) => {
					if (!res.code) { // 成功
						_this.$getUserInfo(() => {
							uni.hideLoading()
							uni.reLaunch({
								url: '/pages/index/index'
							})
						})
					} else {
						uni.hideLoading()
						uni.showToast({
							title: res.msg,
							icon: 'none'
						})
					}
				})
			},
			submit() {
				let _this = this;
				if (!_this.phone) {
					uni.showToast({
						title: '手机号码不能为空',
						icon: 'none'
					})
					return
				}
				if (!reg.tel_reg.test(_this.phone)) {
					uni.showToast({
						title: '手机号码格式不正确',
						icon: 'none'
					})
					return
				}
				if (!_this.phoneCode) {
					uni.showToast({
						title: '短信验证码不能为空',
						icon: 'none'
					})
					return
				}
				uni.showLoading({
					title: '请稍等'
				})
				let data = {
					appId: config.appId,
					extra: _this.loginType === '1' ? _this.userAll.iv : '',
					phone: _this.phone,
					phoneCode: _this.phoneCode,
					code: _this.code,
					data: _this.loginType === '1' ? _this.userAll.encryptedData : '',
					social_type: _this.loginType === '1' ? 'WX_XCX_BIND' : 'WX_XCX_PHONE' // WX_XCX_BIND：一键登录/WX_XCX_PHONE：手机号码登陆
				};
				_this.$http('/auth/member/token', 'POST', data, 'application/x-www-form-urlencoded')
					.then((res) => {
						if (!res.code) {
							uni.setStorageSync('access_token', res.data.access_token)
							_this.$getUserInfo(() => {
								uni.hideLoading()
								uni[_this.type]({
									url: _this.from
								});
							})
						} else {
							uni.hideLoading()
							uni.showToast({
								title: res.msg,
								icon: 'none'
							})
						}
					})
			},
			serviceTerms() {
				uni.navigateTo({
					url: '/pagesA/terms/serviceTerms'
				});
			},
			payTerms() {
				uni.navigateTo({
					url: '/pagesA/terms/payTerms'
				});
			},
			del() {
				this.phone = ''
			},
			getCode() {
				const TIME_COUNT = 60;
				if (!this.timer) {
					this.count = TIME_COUNT;
					this.show = false;
					this.timer = setInterval(() => {
						if (this.count > 1 && this.count <= TIME_COUNT) {
							this.count--;
						} else {
							this.show = true;
							this.show1 = false;
							clearInterval(this.timer);
							this.timer = null;
						}
					}, 1000)
				}
			},
			getCodeAjax() {
				uni.showLoading({
					title: '请稍等'
				})
				this.$http(`/custom/check_code/phone/open/member/login/${this.phone}?appId=${config.appId}`, 'GET', {})
					.then((res) => {
						uni.hideLoading()
						if (!res.code) {
							uni.showToast({
								title: '短信验证码已发送',
								icon: 'none'
							})
							this.getCode()
						} else {
							uni.showToast({
								title: res.msg,
								icon: 'none'
							})
						}
					});
			}
		}
	}
</script>

<style lang='scss'>
	page {
		background: #fff;
	}

	.container {
		position: relative;
		width: 100vw;
		height: 100vh;
		overflow: hidden;
		padding: 0 50rpx;
	}

	.content {
		margin-top: 90rpx;
	}

	.header {
		margin-top: 80rpx;
		text-align: center;
	}

	.header image {
		width: 150rpx;
		height: 150rpx;
		border-radius: 50%
	}

	.header-text {
		text-align: center;
		margin-top: 20rpx;
		color: #333333;
	}

	.btn {
		margin-top: 60rpx;
	}

	.btn button {
		border-radius: 65rpx;
	}

	.tel,
	.telcode {
		background: rgba(246, 246, 246, 1);
		padding: 15rpx 0;
		position: relative;
		border-radius: 34rpx;
	}

	.telcodeall {
		margin-top: 60rpx;
		position: relative;
	}

	.del {
		position: absolute;
		top: 20rpx;
		right: 30rpx;
	}

	.del:before {
		content: "\e617";
		color: #999999;
		font-size: 32rpx;
	}

	.getUserInfo {
		padding: 0;
		background: none;
		line-height: normal;
	}

	.getUserInfo:after {
		border: none;

	}

	.verification-code {
		width: 200rpx;
		position: absolute;
		top: 16rpx;
		right: 0;
		text-align: center;
	}

	.form-item-right button.getUserInfo text {
		cursor: pointer;
		text-align: center;
		color: $button-color-primary;
		font-size: 32rpx;
		padding: 10rpx 15rpx;
	}

	.form-item-right {
		color: $button-color-primary;
	}

	.tel-1 {
		padding-left: 40rpx;
		padding-right: 80rpx;
	}

	.telcode {
		width: 450rpx;
	}

	.xy {
		color: #666666;
		padding: 20rpx 20rpx;
		font-size: 24rpx;
	}

	.xy1 {
		color: #61A4EE;
	}
</style>
