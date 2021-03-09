<template>
	<view class="container">
		<headerbar></headerbar>
		<heightTop></heightTop>
		<view class="home" v-if="indexType">
			<!-- 首页1 -->
			<view v-if="current === 0">
				hollow word，yp-uni-app
				<index1 ref="page"></index1>
			</view>
			<view v-if="current === 1">
				2
			</view>
			<view v-if="current === 2">
				3
			</view>
		</view>
		<view class="home" v-else>
			<!-- 首页2 -->
			<view v-if="current === 0">
				<index2 ref="page"></index2>
			</view>
			<view v-if="current === 1">
				22
			</view>
			<view v-if="current === 2">
				33
			</view>
		</view>
		<tabBar @tabbarChange="switchTab" :propsCurrent="current" :identityType="indexType ? 1 : 0"></tabBar>
	</view>
</template>

<script>
	import tabBar from '@/components/tabBar/tabBar.vue' // 底部导航
	import headerbar from "@/components/headerbar/headerbar";
	import heightTop from "@/components/headerbar/heightTop";

	import index1 from '@/components/index/index1.vue' // 首页1
	import index2 from '@/components/index/index2.vue' // 首页2

	export default {
		components: {
			headerbar,
			heightTop,
			index1,
			index2,
			tabBar
		},
		data() {
			return {
				indexType: 1, // 首页与tabar 类型
				current: 0, // 底部导航索引
			}
		},
		onShow() {
			this.onPageshow()
		},
		onLoad(options) {
			if(options.current && options.current !== 'undefined'){
				this.current = options.current
			}
			this.$nextTick(() => {
				this.$refs.page && this.$refs.page.onPagesLoad && this.$refs.page.onPagesLoad(options)
			})

		},
		onReachBottom(e) {
			this.$refs.page && this.$refs.page.onPagesReachBottom && this.$refs.page.onPagesReachBottom(e)
		},
		onPullDownRefresh() {
			this.$refs.page && this.$refs.page.onPagesPullDownRefresh && this.$refs.page.onPagesPullDownRefresh()
		},
		onPageScroll(t) {
			this.$refs.page && this.$refs.page.onStorePageScroll && this.$refs.page.onStorePageScroll(t)
		},

		methods: {
			onPageshow() { // 页面初始函数
				this.$nextTick(() => {
					this.$refs.page && this.$refs.page.onPageshow && this.$refs.page.onPageshow()
				})
			},
			switchTab(e) { // tabar页面跳转方式：this.$parent.switchTab(1)
				if (this.current !== e) { // 禁止重复刷新
					this.current = e
					this.onPageshow()
				}
			}
		}
	}
</script>

<style lang='scss' scoped>

</style>
