<template>
	<view class="page">
		<page-head :title="title"></page-head>
		<view class="uni-padding-wrap">
			<view class="uni-helllo-text">
				页面开发中。。。
			</view>
			<view class="uni-btn-v">
				<button type="default" @click="setText">改变标题栏文字</button>
				<!-- #ifndef MP-TOUTIAO -->
				<button type="primary" @click="setBg">改变标题栏颜色</button>
				<!-- #endif -->
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				title: '本地生活',
				hasSetText:false,
				hasSetBg:true
			}
		},
		methods: {
			setText() {
				this.hasSetText = !this.hasSetText;
				uni.setNavigationBarTitle({
					title: this.hasSetText ? "Hello APP" : "本地生活"
				})
			},
			setBg() {
				this.hasSetBg = !this.hasSetBg;
				uni.setNavigationBarColor({
					frontColor: this.hasSetBg ? "#ffffff" : "#000000",
					backgroundColor: this.hasSetBg ? "#0FAEFF" : "#F8F8F8"
				})
			}
		}
	}
</script>

<style>
	.uni-helllo-text {
		text-align: center;
	}
</style>
