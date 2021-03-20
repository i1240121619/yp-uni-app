<template>
	<view class="tabbarAll">
		<view class="tabbarAll1">
			<view class="tabbar tabbar1 flex">
				<view class="tabbar-item" @click="tabbarChange('index')">
					<view class="tabbar-item-pic animated" :style="{backgroundImage:'url(' + require('@/static/sy.png') + ')'}"
						:class="[currentPageName === 'index' ? 'active bounce':'']"></view>
					<view class="tabbar-item-txt">首页</view>
				</view>
				<view class="tabbar-item" @click="tabbarChange('all')">
					<view class="tabbar-item-pic animated" :style="{backgroundImage:'url(' + require('@/static/qb.png') + ')'}"
						:class="[currentPageName === 'all' ? 'active bounce':'']"></view>
					<view class="tabbar-item-txt">全部</view>
				</view>
				<view class="tabbar-item" @click="tabbarChange('enterprise')" v-if="identityType">
					<view class="tabbar-item-pic animated" :style="{backgroundImage:'url(' + require('@/static/qy.png') + ')'}"
						:class="[currentPageName === 'enterprise' ? 'active bounce':'']"></view>
					<view class="tabbar-item-txt">企业</view>
				</view>
				<view class="tabbar-item" @click="tabbarChange('study')">
					<view class="tabbar-item-pic animated" :style="{backgroundImage:'url(' + require('@/static/xx.png') + ')'}"
						:class="[currentPageName === 'study' ? 'active bounce':'']"></view>
					<view class="tabbar-item-txt">学习</view>
				</view>
				<view class="tabbar-item" @click="tabbarChange('user')">
					<view class="tabbar-item-pic animated" :style="{backgroundImage:'url(' + require('@/static/md.png') + ')'}"
						:class="[currentPageName === 'user' ? 'active bounce':'']"></view>
					<view class="tabbar-item-txt">我的</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	// currentPageName: index:首页/all:全部/study:学习/enterprise:企业/user:我的
	// userInfo.company = identityType true:企业/false:个人
	export default {
		props: {
			identityType: {
				type: Number
			},
			propsCurrentPageName: {
				type: String
			},
		},
		watch: {
			propsCurrentPageName: {
				handler(newV, oldV) {
					this.currentPageName = newV
					// console.log(newV)
				},
				// deep: true // 需要深度监听
			}
		},
		data() {
			return {
				currentPageName: this.propsCurrentPageName // 当前页面名称ID
			}
		},

		mounted() {},
		methods: {
			tabbarChange(e) {
				this.currentPageName = e
				this.$emit('tabbarChange', this.currentPageName)
			}
		},
	}
</script>
<style lang="scss" scoped>
	.tabbarAll {
		width: 100%;
		height: 100rpx;
		position: fixed;
		left: 0;
		bottom: 0;

		.tabbarAll1 {
			height: 100%;

			.tabbar {
				height: 100%;

				&.tabbar1 {
					background: #FFFFFF;
					box-shadow: inset 0px 1px 0px #EEEEEE;

					.tabbar-item {
						width: 25%;
					}
				}

				&.tabbar2 {
					background: #FFFFFF;
					box-shadow: inset 0px 1px 0px #EEEEEE;

					.tabbar-item {
						width: 20%;
					}
				}

				.tabbar-item {
					height: 100%;
					padding-top: 16rpx;
					text-align: center;

					.tabbar-item-pic {
						width: 46rpx;
						height: 46rpx;
						margin: 0 auto;

						background-size: cover;
						background-position: center 0;
						background-repeat: no-repeat;

					}

					.tabbar-item-pic.active {
						background-position: center -46rpx;
					}

					.tabbar-item-txt {
						font-size: 24rpx
					}

				}
			}
		}
	}
</style>
