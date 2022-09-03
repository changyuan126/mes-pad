<template>
	<view class="commonBody" :style="{ 'height': (this.screenHeight -140) + 'px' }">
		<view class="button-bar">
			<u-grid :border="false" :col="7">
				<u-grid-item @click="">
					<view class="home-icon icon-color01">
						<i class="iconfont icon-qingjia"></i>
					</view>
					<view class="grid-text">来料检验</view>
				</u-grid-item>
				<u-grid-item @click="">
					<view class="shortcut-icon icon-color01">
						<img class="icon-button" :src="require('@/static/icons/png/pro.png')" alt="">
					</view>
					<view class="grid-text">首检</view>
				</u-grid-item>
				<u-grid-item @click="">
					<view class="shortcut-icon icon-color01">
						<img class="icon-button" :src="require('@/static/icons/png/pro.png')" alt="">
					</view>
					<view class="grid-text">末检</view>
				</u-grid-item>
				<u-grid-item @click="">
					<view class="shortcut-icon icon-color01">
						<img class="icon-button" :src="require('@/static/icons/png/pro.png')" alt="">
					</view>
					<view class="grid-text">自检</view>
				</u-grid-item>
				<u-grid-item @click="">
					<view class="shortcut-icon icon-color01">
						<img class="icon-button" :src="require('@/static/icons/png/pro.png')" alt="">
					</view>
					<view class="grid-text">巡检</view>
				</u-grid-item>
				<u-grid-item @click="">
					<view class="shortcut-icon icon-color01">
						<img class="icon-button" :src="require('@/static/icons/png/pro.png')" alt="">
					</view>
					<view class="grid-text">终检</view>
				</u-grid-item>
				<u-grid-item @click="">
					<view class="shortcut-icon icon-color01">
						<img class="icon-button" :src="require('@/static/icons/png/pro.png')" alt="">
					</view>
					<view class="grid-text">出货检验</view>
				</u-grid-item>
			</u-grid>
		</view>
		<view class="list-bar">
			
		</view>
	</view>
</template>

<script>
	export default{
		name: "QcContent",
		data(){
			return {
				screenHeight: 768,
			}
		},
		created() {
			//获取屏幕高度
			uni.getSystemInfo({
				success(res) {
					console.log('windowHeight:'+res.windowHeight);
					this.screenHeight = res.windowHeight;
				}
			})
		}
	}
</script>

<style>
	.icon-button {
		
	}
	.shortcut-icon {
	 	width: 48px;
	 	height: 48px;
		align-items: center;
	 	border-radius: 10px;
	 	box-sizing: border-box;
		display: flex;
		justify-content: center;
		color:#ffffff;
		font-size: 26px;
	 }
	 .shortcut-icon i{
		font-size: 26px;
	 }
</style>