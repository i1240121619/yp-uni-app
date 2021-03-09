<template>
	<view class="container">
		<loginsh ref="login" v-if="WX_VERSION === '2'" :fromBase64='fromBase64' :from='from' :type='type'></loginsh>
		<loginzs ref="login" v-if="WX_VERSION === '1'" :fromBase64='fromBase64' :from='from' :type='type'></loginzs>
	</view>
</template>

<script>
	/**
	 * vuex管理登陆状态，具体可以参考官方登陆模板示例
	 */
	import {
		mapMutations
	} from 'vuex';
	import loginsh from "@/components/login/login-sh"
	import loginzs from "@/components/login/login-zs"

	export default {
		components: {
			loginsh,
			loginzs
		},
		data() {
			return {
				WX_VERSION: '1', // 1：正式版本/2：审核版本
				fromBase64: '', // Base64跳转路径
				from: '', // 跳转路径
				type: '' // 跳转方式
			}
		},
		onShow() {
			this.$refs.login.wxLogin()
		},
		onLoad(option) {
			// uni.showLoading({
			// 	title: '请稍等'
			// })
			// this.getVerison((res) => {
			// 	uni.hideLoading()
			// 	if (!res.code) {
			// 		this.WX_VERSION = res.data.value
			// 	} else {
			// 		uni.showToast({
			// 			title: res.msg,
			// 			icon: 'none'
			// 		})
			// 	}
			// })
			this.fromBase64 = option.from ? option.from : this.$Base64.encodeURI('/pages/index/index')
			this.from = option.from ? this.$Base64.decode(option.from) : '/pages/index/index'
			// console.log(this.from)
			this.type = option.type || 'reLaunch'
		},
		methods: {
			...mapMutations(['getVerison']),
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
		border-radius: 6rpx;
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
		padding: 30rpx;
		color: #949494;
		text-align: center;
		/*超出父盒子高度可滚动*/
	}

	.mask_layer_all .input_show1 {
		margin: 0 auto;
		width: 80%;
		margin-left: 10%;
		font-size: 32rpx;
		text-align: center;
	}

	.btn_all .btn {
		padding: 10rpx 0;
		flex: 1;
	}

	.btn_l {
		border: 1px solid #f5f5f5;
	}

	.btn_all .btn button.mask_layer_btn {
		color: #43b031;
		background: none;
	}

	.btn_all .btn_l button.mask_layer_btn {
		color: #333;
	}

	.btn button.mask_layer_btn::after {
		border: none;
	}

	.btn_all {
		border-top: 1px solid #f5f5f5;
	}
</style>
