<template>
	<view class="tmp-box">
		<!-- 搜索插件 -->
		<view class='filterBox'>
			<view class='filter-input'>
				<text style="color: #b8b8b8;"  class="iconfont icon-sousuo filterImg"/>
				<input class="text" type='text' v-model="inputVal" confirm-type="搜索" @confirm='handleFllter' :placeholder='placeholder'></input>
			</view>
		</view>
	</view>
</template>

<script>
	/**
	 * 树形图的搜索框样式
	 * @property {String} placeholder 搜索框提示内容 
	 */
	export default {
		name:'luyj-tree-search',
		props:{
			placeholder :{
				type: String,
				default:'搜索'
			}
		},
		data() {
			return {
				inputVal: "",			// 输入内容
			};
		},
		methods: {
			handleFllter(e) {
				this.$emit("confirm", e.detail.value)
			},
			clears(){
				console.log(this.inputVal)
				this.inputVal=""
			}
		},
	}
</script>

<style lang="scss" scoped>
	.filterBox {
		padding: 15rpx 32rpx;
		background-color: #fff;
		.filter-input {
			height: 80rpx;
			background-color: #eeeff0;
			border-radius: 40rpx;
			display: flex;
			align-items: center;
			
			padding-left: 40rpx;
			.filterImg {
				width: 32rpx;
				height: 32rpx;
				margin-right: 20rpx;
				margin-bottom: 5rpx;
			}
			.filterImgs {
				width: 32rpx;
				height: 32rpx;
				
			}
			.text {
				width: 84%;
				background-color: #eeeff0;
				font-size: 32rpx;
				color: #000;
			}
		}
	}
	@import url("icon.css");
</style>
