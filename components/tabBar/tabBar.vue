<template>
	<view class="tabbar">
		<u-tabbar v-model="current" :class="[platform === 'android' ? 'android' : '']" :list="tabbarListAll[identityType]"
		 @change="tabbarChange"></u-tabbar>
	</view>
</template>

<script>
	export default {
		props: {
			identityType: {
				type: Number
			},
			propsCurrent: {
				type: Number
			},
		},
		watch: {
			propsCurrent: {
				handler(newV, oldV) {
					this.current = newV
					// console.log(newV)
				},
				// deep: true // 需要深度监听
			}
		},
		data() {
			return {
				platform: uni.getSystemInfoSync().platform,
				tabbarListAll: [
					[{
							iconPath: "/static/icon_index_normal.png",
							selectedIconPath: "/static/icon_index_press.png",
							text: '首页',
							customIcon: false,
						},
						{
							iconPath: "/static/icon_yg_normal.png",
							selectedIconPath: "/static/icon_yg_press.png",
							text: '员工',
							customIcon: false,
						},
						{
							iconPath: "/static/icon_my_normal.png",
							selectedIconPath: "/static/icon_my_press.png",
							text: '我的',
							customIcon: false,
						}
					],
					[{
							iconPath: "/static/icon_index_normal.png",
							selectedIconPath: "/static/icon_index_press.png",
							text: '首页',
							customIcon: false,
						},
						{
							iconPath: "/static/icon_hy_normal.png",
							selectedIconPath: "/static/icon_hy_press.png",
							text: '会员',
							customIcon: false,
						},
						{
							iconPath: "/static/icon_my_normal.png",
							selectedIconPath: "/static/icon_my_press.png",
							text: '我的',
							customIcon: false,
						}
					]
				],
				current: 0
			}
		},

		mounted() {},
		methods: {
			tabbarChange(e) {
				this.$emit('tabbarChange', e)
			}
		},
	}
</script>
<style lang="scss">
	.tabbar {
		height: 100rpx;

		.u-line-1 {
			font-size: 24rpx !important;
			margin-top: 2rpx !important;
		}
	}
</style>
