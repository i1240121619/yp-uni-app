<template>
	<view class="heightTop" :style="{height: headerHeightAll + 'px'}"></view>
</template>

<script>
	const app = getApp()
	export default {
		data() {
			return {
				headerHeightAll : 0
			}
		},
		mounted() {
			this.headerHeightAll = app.globalData.topbarHeight
		},
		methods: {
		}
	}
</script>

<style>
</style>