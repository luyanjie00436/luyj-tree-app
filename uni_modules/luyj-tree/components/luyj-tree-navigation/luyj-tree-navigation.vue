<template>
	<view class="title">
		<scroll-view ref="sea" scroll-x style="width: 100%;white-space: nowrap;">
			<!-- 全部 -->
			<view class="inline-item" @click="clickItem(null,-1)">
				<text v-if="!isre && treeStack.length == 0" class="none">全部</text>
				<text v-else class="active">全部</text>
			</view>
			<!-- 全部 -->
			<!-- 搜索结果 -->
			<view v-if="isre" @click="clickItem(null,-2)"
				:class="activeSearch?'active inline-item':' none inline-item'">
				<i class="iconfont icon-z043 iconclass" />
				搜索结果
			</view>
			<!-- 搜索结果 -->
			<!-- 当前树的层级值 -->
			<view v-for="(item,index) in treeStack" class="inline-item" :key="index">
				<view class="inline-item" @click="clickItem(item,index)">
					<i class="iconfont icon-z043 iconclass" />
					<text v-if="index== treeStack.length-1" class="none inline-item">
						{{item[slabel]}}
					</text>
					<text v-else class="active">
						{{item[slabel]}}
					</text>
				</view>
			</view>
			<!-- 当前树的层级值 -->
		</scroll-view>
	</view>
</template>

<script>
	/**
	 * 无限级树-面包屑导航
	 * @description  无限级树的面包屑导航
	 * @property {String} slabel 显示的label值
	 * @return {Function} clickItem（item , index） 点击导航栏的索引
	 * 	@item 表示导航项对应的值
	 *  @index 表示导航项的层级别索引
	 *   @value -1 全部
	 *   @value -2 表示层级
	 *   @value 其他 （从最外层开始，依次0,1,2,3……）
	 * @return {Object} outF 导航条内部的方法
	 * 	@param {Function}  isIre 设置是否搜索状态
	 * 	@param {Function}  setTreeStack 设置导航树的值
	 * 	@param {Function}  pushTreeStack 为导航树添加项
	 * 	@param {Function}  clearTreeStack 清空导航树
	 */
	// scrollLeft : 暂时
	export default {
		name: "luyj-tree-navigation",
		props: {
			// 显示的label值
			slabel: {
				type: String,
				default: 'label'
			},

		},
		data() {
			return {
				isre: false, // 是否进行了搜索(返回是否进行了搜索)
				treeStack: [], // 当前搜索值
			}
		},
		computed: {
			// 是否可点击搜索结果
			activeSearch: function() {
				return this.treeStack.length > 0;
			}
		},
		created: function() {
			// 浅拷贝导航列表的每一个对象（为了不改变item值，也不复制过多的数据）
			this.treeStack.forEach(item => {
				var tempItem = Object.assign(item);
				this.treeStack.push(tempItem);
			});
			var obj = {
				setIsre: this.setIsre,
				getIsre : this.getIsre,
				setTreeStack: this.setTreeStack,
				concatTreeStack : this.concatTreeStack,
				pushTreeStack: this.pushTreeStack,
				clearTreeStack: this.clearTreeStack,
				getTreeStack : this.getTreeStack
			};
			this.$emit("inF", obj); //  导出的导航栏调用方法
		},
		methods: {
			// ================================== 初始化时导出方法（用于外部调用内部结果） =========================================================
			/** 设置isre值(是否搜索)
			 * @param {Boolean} isre 设置是否搜索
			 */
			setIsre: function(isre) {
				this.isre = isre;
			},
			/**
			 * 获取isr值（获取是否搜索中）
			 */
			getIsre: function(){
				return this.isre;
			},
			/** 设置导航树
			 * @param {Array} treeStack 导航树
			 */
			setTreeStack: function(treeStack) {
				this.treeStack = treeStack;
			},
			/** 拼接导航树
			 * @param {Object} treeStack 导航树
			 */
			concatTreeStack : function(treeStack){
				this.treeStack = this.treeStack.concat(treeStack);
			},
			/** 为导航树添加项
			 * @param {Object} item 待添加的对象
			 */
			pushTreeStack: function(item) {
				this.treeStack.push(item);
			},
			/**
			 * 获取当前导航条
			 */
			getTreeStack : function(){
				return this.treeStack;
			},
			/**
			 * 清空导航树
			 */
			clearTreeStack: function() {
				this.treeStack.splice(0);
			},
			// ================================== 监听事件 ===========================================================
			/** 点击导航栏索引
			 * @param {Object} item 当前层的值
			 * @param {Number} index 索引值
			 */
			clickItem(item, index) {
				if (index == -1) {
					// 点击全部
					this.isre = false;
					this.treeStack.splice(0);
				} else if (index == -2) {
					// 搜索结果
					if (this.activeSearch) {
						this.isre = true;
						this.treeStack.splice(0);
					}
				} else {
					// 点击某一层级树
					this.isre = false;
					if (this.treeStack.length - 1 > index) {
						this.treeStack.splice(index + 1);
					}
				}
				this.$emit("clickItem", item, index);
			},
		},
		// ============================================================================================================
	}
</script>

<style lang="scss" scoped>
	@import "luyj-tree-navigation.scss";
	@import "icon.css";
</style>
