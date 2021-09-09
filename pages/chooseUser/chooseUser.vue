<template>
	<view>
		<luyj-tree  v-slot:default="{item}" :checkList="checkList"   :props="prop" @change="change" @sendValue="confirm"  :parent="true" :isCheck="isCheck" :trees="tree">
			<!-- 内容插槽 -->
			<view>
				<view class="content-item">
					<view class="word">{{item.name}}</view>
				</view>
			</view>
		</luyj-tree>
	</view>
</template>

<script>
	import dataList from '@/common/data2.js'; // 引用数据
	
	var _self = this;
	export default {
		data() {
			return {
				title: '', // 标题名称
				tree: null,
				checkList: [],
				backList: this.checkList,
				max: 5,
				isCheck: {
					type: Boolean,
					default: true
				}, // 是否开启选择
				prop: {},
				aprop: {
					label: 'name',
					children: 'children',
					multiple: true
				},
				bprop: {
					label: 'name',
					children: 'children',
					multiple: false,
					checkStrictly: true
				},
				cprop: { //单选模式(任意一项)
					label: 'name',
					children: 'children',
					multiple: false,
					nodes: false
				},
				dprop: { //单选模式选user
					label: 'name',
					children: 'children',
					multiple: false,
					nodes: true
				}
			}
		},
		onLoad(option) {
			_self = this;
			this.getParams(option);
		},
		methods: {
			// =================================== 初始化 ========================================================================
			/** 获取初始化值
			 * @param {Object} options 参数
			 */
			getParams: function(option) {
				// 获取导航栏标题
				this.title = Boolean(option.title) ? option.title : '';
				uni.setNavigationBarTitle({
					title: this.title
				});
				// 获取列表有关参数
				try {
					// #ifdef H5
					let obj = option.arr.replace("\"([^\"]*)\"", "$1");
					let checkList = JSON.parse(obj);
					// #endif
					// #ifdef MP-QQ||MP-WEIXIN||APP-NVUE||APP-PLUS
					let checkList = JSON.parse(option.arr);
					// #endif
					console.log("checkList=>", checkList);

					this.checkList = []
					this.checkList = checkList;
				} catch (e) {
					// console.log("获取数组报错" , e);
				}

				if (option.type == 0) {
					this.prop = this.aprop; //多选
				} else if (option.type == 1) {
					this.prop = this.bprop; //多选
				} else if (option.type == 2) {
					this.prop = this.cprop; //单选任意一项
				} else {
					this.prop = this.dprop; //单选user
				}
				this.getTree(); 		// 获取树的数据
				this.isCheck = option.isCheck != 'false' ? Boolean(option.isCheck) : false;
			},
			/** 获取树形图的数据
			 * 模拟实际获取数据，设定时间为3s
			 */
			getTree : function(){
				uni.showLoading({
					title:'数据获取中……'
				});
				setTimeout(function(){
					uni.hideLoading();
					_self.tree = dataList;
				},3000);
			},
			// =================================== 监听事件  =====================================================================
			/**选项改变时执行方法
			 * @param {Object} val
			 */
			change : function(val){
				console.log('选项改变时执行方法',val);
			},
			/** 点击确认按钮
			 * @param {Object} val
			 */
			confirm(val) {
				console.log('点击确认按钮=>' , val);
				this.checkList = val;
				this.backConfirm(val);
				this.backList = val;
			},
			/** 向上一页返回参数
			 * @param {Object} val
			 */
			backConfirm(val) {
				var pages = getCurrentPages();
				if(pages.length > 2){
					var currPage = pages[pages.length - 1]; //当前页面
					var prevPage = pages[pages.length - 2]; //上一个页面
					// #ifdef H5
					prevPage.$vm.query = val //h5写法
					// #endif
					let check = val;
					//#ifdef MP-WEIXIN||MP-QQ
					prevPage.setData({
						query: check,
					}) //小程序写法
					// #endif
					
					uni.navigateBack();
				}
			}
		}
	}
</script>

<style lang="scss">
	.box_sizing {
		-webkit-box-sizing: border-box;
		-moz-box-sizing: border-box;
		box-sizing: border-box;
	}

	.btn {
		position: fixed;
		bottom: 0;
		padding: 10px;
		background-color: #fff;
		width: 100%;

		.sureBtn {
			background-color: #0095F2;
			color: #fff;
		}
	}

	.content-item {
		display: flex;
		position: relative;
		align-items: center;

		.person {
			height: 64rpx;
			min-width: 64rpx;
			border-radius: 50%;
			border: 1rpx solid rgba(0, 149, 235, 0.15);
			background-color: rgba(0, 149, 235, 0.1);
			margin-left: 0px;
			color: #0095F2;
			line-height: 64rpx;
			font-size: 22rpx;
			text-align: center;
			margin-left: 20rpx;
		}

		.word {
			margin-left: 16rpx;
			font-size: 30rpx;
			color: #5b5757;
			width: 500rpx;
			word-break: break-all;
		}
	}
</style>
