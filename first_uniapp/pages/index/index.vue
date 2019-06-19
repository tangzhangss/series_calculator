<template>
	<view class="container">
        <image src="../../static/back.jpg" class="backimg"></image>
		
		<view class="one" @tap="jumpPage('simple')">
			<image class="back" src="../../static/simple_back.jpg"></image>
			<text>普通计算器</text>
		</view>
		
		<view class="one" @tap="jumpPage('subject')">
			<image class="back" src="../../static/subject_back.jpg"></image>
			<text>科学计算器</text>
		</view>
		
		<view class="one" @tap="jumpPage('radix')">
			<image class="back" src="../../static/radix_back.jpg"></image>
			<text>进制转换器</text>
		</view>
		
		<view class="one" @tap="jumpPage('social')">
			<image class="back" src="../../static/social_back.jpg"></image>
			<text>社保计算器</text>
		</view>
		
	</view>
</template>

<style>
	.container{
		display: flex;
		justify-content: center;
		align-items: center;
		flex-direction: column;
		padding-top: 20px;
	}
	.container .backimg{
		width: 100vw;
		height: 100vh;
		position: fixed;
		top: 0;
		left: 0;
	    z-index: -1;
	}
	.container .one{
		position: relative;
		height: 100px;
		width: 80vw;
		margin:20px auto;
		display: flex;
		align-items: center;
		justify-content: center;
		border-radius: 10px;
		overflow: hidden;
	}
	.container .one .back{
		position: absolute;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		opacity: 0.6;
		z-index: 0;
	}
	.container .one text{
		color: white;
		font-size: 25px;
		z-index: 1;
	}

</style>
<script>
	export default {
		data() {
			return {
				title: '系列计算器'
			}
		},
		onLoad() {

		},
		methods: {
			jumpPage:function(str){
				uni.navigateTo({
					url:"../"+str+"/"+str
				})
			}
		}
	}
</script>


