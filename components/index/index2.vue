<template>
	<view class="index2">
		首页2
	</view>
</template>

<script>

export default {
	data() {
		return {
			
			
		}
	},
	components:{
		
    },
	methods: {
		onPageshow() {
			console.log("调用了index2组件模拟的onPageshow事件")
		},
		onPagesLoad(){
			console.log("调用了index2组件模拟的onPagesLoad事件")
		},
	}
}
</script>

<style lang='scss' scoped>

</style>
