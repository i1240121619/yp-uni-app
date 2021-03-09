<template>
	<view class="userContainer">
		<view class="user-section">
			<image class="bg" src="/static/xiangqingBg.png"></image>
			<view class="user-info-box">
				<view class="portrait-box" v-if="token" @click="navTo(`/pagesA/set/set?avatar=${userInfo.avatar}&nickName=${userInfo.nickName}&sex=${userInfo.sex}&phone=${userInfo.mobile}`)">
					<image class="portrait" :src="userInfo.avatar || '/static/img_logo.png'"></image>
				</view>
				<view class="portrait-box" v-else @click="navTo('/pages/login/login', 1)">
					<image class="portrait" :src="'/static/img_logo.png'"></image>
				</view>
				<view class="info-box flex">
					<view class="username" v-if="token">
						<view class="username-nc">{{ userInfo.name || phone }}</view>
						<!-- <view class="username-jf">当前积分：{{userInfo.integralInfoVo.integral}}</view> -->
						<view class="username-jf">
							<view class="userLevel">
								<view class="super iconfont">&#xe647;</view>
								<view class="levelText">{{userInfo.po.name}}</view>
							</view>
							<view class="userIntegral">
								<view class="integralImg">
									<view class="integralIC iconfont">&#xe64d;</view>
									<view class="integralText">积分</view>
								</view>
								<view class="integralNum">{{ token?userInfo.integralInfoVo.integral:0 }}</view>
							</view>
						</view>
					</view>
					<view class="username" @click="navTo('/pages/login/login', 1)" v-else>
						<text>点击登录 ></text>
					</view>

				</view>
			</view>
		</view>
		<view class="cover-container">
			<!-- 订单 -->
			<view class="order-section">
				<view class="order-item" @click="navTo('/pagesA/order/orderList?state=1')" hover-class="common-hover"
				 :hover-stay-time="50">
						<view class="yticon iconfont">&#xe64e;</view>
						<text style="font-size:28upx; color: #666666;">待付款</text>
						<br>
						<text v-if="num1!=0" class="a" style="font-size:22upx">{{num1 || '0'}}</text>
				</view>
				<view class="order-item" @click="navTo('/pagesA/order/orderList?state=2')" hover-class="common-hover"
				 :hover-stay-time="50">
						<view class="yticon iconfont">&#xe649;</view>
						<text style="font-size:28upx; color: #666666;">待提货</text>
						<br>
						<text v-if="num2!=0" class="a" style="font-size:22upx">{{num2 || '0'}}</text>
				</view>
				<view class="order-item" @click="navTo('/pagesA/order/orderList?state=3')" hover-class="common-hover"
				 :hover-stay-time="50">
						<view class="yticon iconfont">&#xe65b;</view>
						<text style="font-size:28upx; color: #666666;">已完成</text>
						<br>
				</view>
				<view class="order-item" @click="navTo('/pagesA/order/orderList?state=4')" hover-class="common-hover"
				 :hover-stay-time="50">
						<view class="yticon iconfont">&#xe659;</view>
						<text style="font-size:28upx; color: #666666;">售后</text>
						<br>
						<text v-if="num4!=0" class="a" style="font-size:22upx">{{num4 || '0'}}</text>
				</view>
				<view class="order-item" @click="navTo('/pagesA/order/orderList?state=0')" hover-class="common-hover"
				 :hover-stay-time="50">
						<view class="yticon iconfont">&#xe65c;</view>
						<text style="font-size:28upx; color: #666666;">全部订单</text>
				</view>
			</view>
			<!-- 浏览历史 -->
			<view class="history-section icon">
				<list-cell icon="iconicon_tihuodian iconfont" v-if="token" contentstyle="content" iconColor="#65C5CA" title="我的提货点" :tips="userInfo.pickUpShopName"
				 @eventClick="navTo(`/pagesA/addressList/addressList?shopId=${userInfo.shopId}`)"></list-cell>
				<list-cell icon="iconicon_tihuodian iconfont" v-else contentstyle="content" iconColor="#65C5CA" title="我的提货点" :tips="storeInfo.pickUpShopName"
				 @eventClick="toLogin(`/pagesA/addressList/addressList?shopId=${userInfo.shopId}`)"></list-cell>
				<list-cell icon="iconicon_mingxi iconfont" :contentstyle="token?'content':'noboder'" iconColor="#65C5CA" title="积分明细" tips="查看积分明细" @eventClick="navTo('/pagesA/user/integral')"></list-cell>
				<list-cell icon="iconicon_kefu iconfont" contentstyle="noboder" iconColor="#65C5CA" v-if="token" title="门店电话" @eventClick="phoneTo()"></list-cell>
			</view>
			<view class="history-section icon">
				<list-cell icon="iconicon_qrcode iconfont" contentstyle="noboder" iconColor="#65C5CA" title="关于六谷大药房" @eventClick="navTo('/pagesA/user/about')"></list-cell>
			</view>
		</view>
	</view>
</template>
<script>
	import listCell from '@/components/mix-list-cell'
	import cmdProgress from '@/components/cmd-progress/cmd-progress.vue'
	let startY = 0,
		moveY = 0,
		pageAtTop = true
	export default {
		components: {
			listCell,
			cmdProgress
		},
		data() {
			return {
				userInfo: uni.getStorageSync('userInfo'),
				storeInfo: uni.getStorageSync('storeInfo'),
				coverTransform: 'translateY(0px)',
				coverTransition: '0s',
				moving: false,
				percent: '',
				num1: "",
				num2: "",
				num3: "",
				num4: "",
				token: uni.getStorageSync('access_token'),
				phone: ''
			}
		},
		onLoad(option) {

		},
		onPullDownRefresh: function() {
			this.getnewsList()
		},
		onShow() {
			this.storeInfo = uni.getStorageSync('storeInfo')
			uni.showLoading({
				title: '请稍等',
				mask: true,
				duration: 2000
			})
			this.$getUserInfo(() => {
				this.phone = this.userInfo.mobile.substr(0, 3) + "****" + this.userInfo.mobile.substr(7)
				this.percent = (this.userInfo.integralInfoVo.integral / this.userInfo.po.upgradeRule *
					100).toFixed(0)
				uni.hideLoading()
			})
			this.$getCartNumber()
			if (this.token) {
				let codeArr = [0, 201, 301, 401]
				this.$http('/mall/api/order/getOrderNumber', 'post').then(res => {
					let data = JSON.parse(res.data)
					if (!res.code) {
						this.num1 = data['0']
						this.num2 = data['201']
						this.num3 = data['301']
						this.num4 = data['401']
					}
				})
			} else {
				uni.hideLoading()
			}
		},
		methods: {
			pickUpShop() {
				if (!this.userInfo.pickUpShopId) {
					uni.navigateTo({
						url: `/pagesA/addressList/addressList?path=/pages/user/user`
					})
				} else {
					let data = {
						shopId: this.shopId
					};
					this.$http('/custom/mallShop/getPickUpShop', 'GET', data).then((res) => {
						if (!res.code) { // 成功
							let dataList = res.data
							wx.openLocation({
								//当前经纬度
								latitude: dataList.lat,
								longitude: dataList.lon,
								//缩放级别默认18,缩放比例为5-18
								scale: 18,
								//位置名
								name: dataList.name,
								//详细地址
								address: dataList.address,
								//成功打印信息
								success: function(res) {

								},
								//失败打印信息
								fail: function(err) {
									wx.showToast({
										title: '调用地图失败，请返回重试',
									})
								},
							})


						} else {
							uni.showToast({
								title: res.msg,
								icon: 'none'
							})
						}
					})
					// uni.navigateTo({
					// 	url: '/pagesA/storeDetails/storeDetails?id=' + this.userInfo.pickUpShopId
					// })
				}
			},
			// 下拉刷新
			getnewsList: function() {
				if (this.userInfo) {
					uni.showNavigationBarLoading()
					uni.showLoading({
						title: '请稍等'
					})
					if (!this.$checkLogin(this.$Base64.encodeURI('/pages/user/user'), 'reLaunch')) return // 判断有没有登陆，没有登陆跳转到登陆页面 一定写在onLoad生命周期函数里面
					if (this.token) {
						this.$getUserInfo(() => {
							this.phone = this.userInfo.mobile.substr(0, 3) + "****" + this.userInfo.mobile.substr(7)
							this.percent = (this.userInfo.integralInfoVo.integral / this.userInfo.po.upgradeRule *
								100).toFixed(0)
						})
					}
					let codeArr = [0, 201, 301, '']
					this.$http('/mall/api/order/getOrderNumber', 'post').then(res => {
						let data = JSON.parse(res.data)
						uni.hideNavigationBarLoading();
						uni.stopPullDownRefresh();
						uni.hideLoading()
						if (!res.code) {
							this.num1 = data['0']
							this.num2 = data['201']
							this.num3 = data['301']
						}
					})
				} else {
					uni.stopPullDownRefresh();
				}
			},
			navTo(url, code) {
				if (code === 1) {
					uni.reLaunch({
						url
					})
				} else {
					uni.navigateTo({
						url
					})
				}
			},
			toLogin() {
				if (!this.$checkLogin(this.$Base64.encodeURI('/pages/user/user'), 'reLaunch')) return
			},
			phoneTo() {

				uni.makePhoneCall({
					// 手机号
					phoneNumber: this.storeInfo.phone,
					// 成功回调
					success: (res) => {
						console.log('调用成功!')
					},
					// 失败回调
					fail: (res) => {
						console.log('调用失败!')
					}
				});
			}
		}
	}
</script>
<style lang="scss">
	page {
		background: $page-color-base;
	}

	%flex-center {
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
	}

	%section {
		display: flex;
		justify-content: space-around;
		align-content: center;
		background: #fff;
		border-radius: 10upx;
	}

	.userContainer {
		height: 100%;
		background-color: #f5f5f5;

		.notice {
			width: 100%;
			height: 68upx;
			background: rgba(102, 102, 102, 1);
			position: relative;
			display: flex;
			padding: 0 16upx;
			justify-content: space-between;
			line-height: 68upx;

			.noticeLeft {
				.img {
					width: 32upx;
					height: 32upx;
					transform: translateY(4upx);
				}

				.noticeText {
					font-size: 28upx;
					color: #ffffff;
					margin-left: 16upx;
				}
			}

			.noticeRight {
				font-size: 28upx;
				color: #FFCF4A;
			}
		}
	}

	.user-section {
		height: 280upx;
		padding: 20upx 30upx 0;
		position: relative;

		.iconfont {
			color: #FFCD90;
			font-size: 24px;
		}

		.bookshelf-surpo {
			width: 100%;
			height: 66upx;
			line-height: 66upx;
			text-align: center;
			font-size: 28upx;
			display: block
		}

		.bookshelf {
			position: relative;
			margin-top: 1upx;

			.card-sp {
				display: flex;
				flex-direction: row;
				justify-content: start;
				align-items: center;
				padding: 0 20upx;
				position: absolute;
				top: 14upx;
				right: 30upz;
				;
				width: 686upx;
				height: 128upx;
				line-height: 128upx;
				background: linear-gradient(137deg, rgba(253, 229, 193, 1) 0%, rgba(255, 205, 144, 1) 100%);
				border-radius: 8px;

				.images {
					display: flex;
					flex-direction: row;
					justify-content: center;
					align-items: center;
					width: 48upx;
					height: 48upx;
					background: #FFFFFF;
					border-radius: 24upx;
				}
			}

			.card-bg {
				// padding: 0 20upx;
				position: absolute;
				top: 4upx;
				right: 30upz;
				;
				width: 690upx;
				height: 175upx;
				border-radius: 8px;
			}

			// 超级会员
			.card-cj {
				background: linear-gradient(136deg, rgba(253, 229, 193, 1) 0%, rgba(255, 205, 144, 1) 100%);
				color: #333333;
			}

			// 内部会员
			.card-nb {
				background: linear-gradient(135deg, rgba(62, 62, 62, 1) 0%, rgba(12, 12, 12, 1) 100%);
				color: #FDD6A7;
			}

			.bookshelf-content::-webkit-scrollbar {
				display: none;
				width: 0px;
				/*高宽分别对应横竖滚动条的尺寸*/
				height: 0px;
			}

			.bookshelf-content {
				white-space: nowrap; // 滚动必须加的属性
				width: 100%;
				border-radius: 8px;
				overflow-x: scroll;
				margin: 0 auto;

				.item {
					width: 20%;
					margin-right: 20upx;
					display: inline-block;
					vertical-align: top;

					.img {
						display: flex;
						flex-direction: row;
						justify-content: center;
					}

					.item-title {
						display: block; // 让字体换行
						width: 100%;
						height: 24px;
						font-size: 12px;
						font-family: MicrosoftYaHei;
						// color:rgba(51,51,51,1);
						line-height: 24px;
						text-align: center;
					}
				}
			}
		}

		.bg {
			position: absolute;
			left: 0;
			top: 0;
			width: 100%;
			height: 174upx;
		}
	}

	.vipEquity {
		z-index: 2;
		position: absolute;
		right: 0;
		top: 40upx;
		width: 130upx;
		height: 40upx;
		background: rgba(51, 51, 51, 1);
		border-radius: 200upx 0px 0px 200upx;
		color: #fff;
		font-size: 24upx;
		line-height: 45upx;
		opacity: 0.64;
		text-align: center;
	}

	.user-info-box {
		height: 130upx;
		display: flex;
		align-items: center;
		position: relative;
		z-index: 1;

		.portrait {
			width: 96upx;
			height: 96upx;
			border-radius: 50%;
			overflow: hidden;
			display: block;
		}

		.info-box {
			position: relative;
			margin-bottom: 10upx;
		}

		.phone-box {
			position: absolute;
			left: 113upx;
			top: 80upx;
			font-size: 24upx;
			color: #ffffff;
		}

		.username {
			overflow: hidden;
			display: inline-block;
			transform: translateY(5upx);
			font-size: 32upx;
			color: #ffffff;
			padding: 0 10upx 0 20upx;
			z-index: 2;
		}

		.username-jf {
			font-size: 28rpx;
			color: #FFFFFF;
			margin-top: 5rpx;
			display: flex;
			.userLevel {
				display: flex;
				width: 140rpx;
				height: 40rpx;
				line-height: 40rpx;
				border-radius: 20rpx;
				background-color: #FFF472;
				margin-top: 5rpx;
				padding: 0 4rpx;
				margin-right: 12rpx;
				.super {
					color: #333333;
					font-size: 32upx;
				}
				.levelText {
					color: #333333;
					font-size: 22rpx;
				}
			}

			.userIntegral {
				display: flex;
				height: 44rpx;
				line-height: 44rpx;
				.integralImg {
					margin-top: 5rpx;
					display: flex;
					width: 100rpx;
					border-radius: 20rpx;
					height: 40rpx;
					line-height: 40rpx;
					background-color: #F2F2F2;

					.integralIC {
						color: $base-color;
						font-size: 47rpx;
					}

					.integralText {
						color: $base-color;
						font-size: 23rpx;
					}
				}

				.integralNum {
					// transform: translateY(6rpx);
					color: #fff;
					font-weight: bold;
					font-size: 34rpx;
					font-weight: 600;
					margin-left: 12rpx;
				}
			}
		}

		.username-nc {
			font-weight: bold;

			image {
				margin-left: 15rpx;
				width: 30rpx;
				height: 30rpx;
				transform: translateY(5rpx);
			}
		}

		.cj {
			color: #F7B500;
			// background: linear-gradient(270deg, rgb(254, 245, 221) 0%, rgba(254, 252, 252, 1) 40%);
			margin-left: -114upx;
			height: 40upx;
			line-height: 40upx;
		}
	}

	.cover-container {
		margin-top: -110upx;
		position: relative;
		padding-bottom: 20upx;
	}

	.order-section {
		@extend %section;
		padding: 28upx 0;
		border-radius: 0upx;

		.order-item {
			@extend %flex-center;
			position: relative;
			width: 120upx;
			height: 120upx;
			border-radius: 10upx;
			font-size: $font-sm;
			color: $font-color-dark;

			.a {
				position: absolute;
				z-index: 10;
				right: 18upx;
				top: -2upx;
				color: #ffffff;
				background-color: #f43530;
				border-radius: 50%;
				min-width: 36upx;
				max-height: 36upx;
				z-index: 5;
				text-align: center;
				line-height: 36upx;
				border: 1px solid #f43530;
			}
		}

		.yticon {
			font-size: 62upx;
			margin-bottom: 10upx;
			color: $base-color;
		}

		.icon-shouhoutuikuan {
			font-size: 44upx;
		}
	}

	.history-section {
		margin-top: 20upx;
		background: #fff;
		border-radius: 10upx;
		padding: 0 16upx;
		&::last-child{
			.b-b:after, .b-t:after{
				border: none;
			}
		}

		.sec-header {
			display: flex;
			align-items: center;
			font-size: $font-base;
			color: $font-color-dark;
			line-height: 40upx;
			margin-left: 30upx;

			.yticon {
				font-size: 44upx;
				color: #5eba8f;
				margin-right: 16upx;
				line-height: 40upx;
			}
		}

		.h-list {
			white-space: nowrap;
			padding: 30upx 30upx 0;

			image {
				display: inline-block;
				width: 160upx;
				height: 160upx;
				margin-right: 20upx;
				border-radius: 10upx;
			}
		}
	}
</style>
