<template>
	<view class="index1">
		首页1
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
			console.log("调用了index1组件模拟的onPageshow事件")
		},
		onPagesLoad(){
			console.log("调用了index1组件模拟的onPagesLoad事件")
		},
	}
}
</script>

<style lang='scss' scoped>

</style>
