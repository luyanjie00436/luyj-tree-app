<template>
	<view>
		<view class='filterBox' :style="{'background-color' : backgroundColor}">
			<view class='filter-input'
				:style="{'background-color' :inputBackgroundColor ,'border-radius':radius + 'rpx'}">
				<!-- 左侧搜索图标 -->
				<text :style="{'color':iconColor}" class="iconfont icon-sousuo filterImg"></text>
				<!-- 输入框内容 -->
				<input class="text" type='text' v-model="inputVal" confirm-type="搜索" :placeholder='placeholder'
					:placeholder-style="placeholderStyle" :maxlength="maxlength" @input="handleInput"
					@focus="handleFocus" @blur="handleBlur" @confirm='handleFllter'></input>
				<!-- 清除按钮 -->
				<view v-if="clearable" class="padding-left-sm" @click="clears">
					<text :style="{'color':iconColor}" class="iconfont icon-clear filterImg"></text>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	/**
	 * 无限级树的搜索框组件
	 * @description 无限级树的搜索框组件
	 * @property {String} backgroundColor 背景色(默认#FFFFFF)
	 * @property {String} inputBackgroundColor 输入框背景色(默认#EEEFF0)
	 * @property {Number} radius 输入框圆角，单位rpx(默认40)
	 * @property {String} placeholder 输入框为空时占位符(默认'搜索')
	 * @property {String} placeholderStyle placehoder的样式
	 * @property {Number} maxlength 最大输入长度 ,设置为 -1 的时候不限制最大长度(默认值140)
	 * @property {String} iconColor 图标颜色（默认#B8B8B8）
	 * @property {Boolean} clearable 是否显示清除按钮 是否显示清除按钮（默认true）
	 * @event {Function()} input 输入框内容编号时触发
	 * @event {Function()} focus 输入框获得焦点时触发
	 * @event {Function()} blur 输入框失去焦点时触发
	 * @event {Function()} confirm 提交输入框内容是触发
	 * @event {Function()} clear 清空输入框内容时触发
	 */
	export default {
		name: 'luyj-tree-search',
		props: {
			// 背景色
			backgroundColor: {
				type: String,
				default: '#FFFFFF'
			},
			// 输入框背景颜色
			inputBackgroundColor: {
				type: String,
				default: '#EEEFF0'
			},
			// 输入框圆角
			radius: {
				type: Number,
				default: 40
			},
			// 输入框为空时占位符
			placeholder: {
				type: String,
				default: '搜索'
			},
			// placeholder的样式
			placeholderStyle: {
				type: String,
				default: ''
			},
			// 最大输入长度 ,设置为 -1 的时候不限制最大长度
			maxlength: {
				type: Number,
				default: 140
			},
			// 图标的颜色
			iconColor: {
				type: String,
				default: '#B8B8B8'
			},
			// 是否显示清除按钮
			clearable: {
				type: Boolean,
				default: true
			}
		},
		data() {
			return {
				inputVal: "", // 输入内容
			};
		},
		components: {
			test: function() {
				return 120;
			}
		},
		methods: {
			/** 输入框变化时方法
			 * @param {Object} e
			 */
			handleInput: function(e) {
				this.$emit("input", e)
			},
			/** 输入框聚焦时触发
			 * @param {Object} e
			 */
			handleFocus: function(e) {
				this.$emit("focus", e)
			},
			/** 输入框失去焦点时触发
			 * @param {Object} e
			 */
			handleBlur: function(e) {
				this.$emit("blur", e)
			},
			/** 提交内容时触发
			 * @param {Object} e
			 */
			handleFllter: function(e) {
				this.$emit("confirm", e)
			},
			/**
			 * 清空输入框内容
			 */
			clears: function() {
				this.inputVal = "";
				this.$emit("clear", this.inputVal)
			}
		},
	}
</script>

<style lang="scss" scoped>
	.filterBox {
		padding: 15rpx 32rpx;

		.filter-input {
			height: 80rpx;
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
				width: 100%;
				font-size: 32rpx;
				color: #000;
			}
		}
	}

	// 添加左侧padding(用于扩大图标范围)
	.padding-left-sm {
		padding-left: 20rpx;
	}

	@import url("icon.css");
</style>
