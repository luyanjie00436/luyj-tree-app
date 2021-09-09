<template>
	<view>
		<!-- 搜索框 -->
		<view class="header">
			<!-- 搜索栏 -->
			<luyj-tree-search v-if="searchIf" :backgroundColor="searchBackgroundColor"
				:inputBackgroundColor="searchInputBackgroundColor" :radius="searchRadius" :iconColor="searchIconColor"
				:placeholder="searchPlaceholder" :placeholderStyle="searchPlaceholderStyle" :maxlength="searchMaxlength"
				:clearable="searchClearable" @confirm="confirmSearch"></luyj-tree-search>
			<!-- 面包屑导航 -->
			<luyj-tree-navigation :slabel="props.label" @inF="navigationInt" @clickItem="backTree">
			</luyj-tree-navigation>
			<!-- 面包屑导航 -->
		</view>
		<!-- 列表 -->
		<view>
			<view class="container-list" :style="{'padding-bottom' : isCheck ? '160rpx' : 0 , 'padding-top' :searchIf ? '200rpx' :'90rpx' }">
				<luyj-tree-item v-for="(item , index) in tree" :key="index" :item="item" :isCheck="isCheck"
					:checkActiveColor="checkActiveColor" :checkNoneColor="checkNoneColor" :multiple="props.multiple" :checked="newCheckList.includes(item)"
					 :nodes="props.nodes" :comparison="comparison" @clickItem="toChildren"
					@change="checkbox($event , item , index)">
					<template slot="body">
						<slot v-bind:item="item" v-bind:slotObj="slotObj">
							<view class="word">{{ item[props.label] }}</view>
						</slot>
					</template>
				</luyj-tree-item>
			</view>
		</view>
		<!-- 确定按钮 -->
		<view v-if="isCheck" class="btn box_sizing">
			<button class="sureBtn" type="primary" @click="backConfirm">确认</button>
		</view>
	</view>
</template>

<script>
	/**
	 * luyj-tree 无限树形结构树、支持搜索选择。
	 * @description  无限树形结构组件。支持搜索、选择(包括单选、多选)。面包屑类型导航。原插件地址：https://ext.dcloud.net.cn/plugin?id=2423。
	 * @tutorial url https://ext.dcloud.net.cn/plugin?name=luyj-tree
	 * @property {Boolean} searchIf 是否开启搜索 （默认值true）
	 * @property {String} searchBackgroundColor 搜索框背景色(默认#FFFFFF)
	 * @property {String} searchInputBackgroundColor 搜索框的输入框背景色(默认#EEEFF0)
	 * @property {Number} searchRadius 搜索框的圆角值，单位rpx（默认40）
	 * @property {String} searchPlaceholder 搜索框的内容物空时提示内容
	 * @property {String} searchPlaceholderStyle 搜索框的placehoder的样式
	 * @property {Number} searchMaxlength 搜索框的最大输入长度 ,设置为 -1 的时候不限制最大长度
	 * @property {String} searchIconColor 搜索框的图标颜色（默认#B8B8B8）
	 * @property {Boolean} searchClearable 搜索框是否显示清除按钮
	 * @property {Array}  trees 传入的树形结构，每个对象必须包含唯一的id值(默认值【】)
	 * @property {Boolean} isCheck 是否开启选择操作（默认值false）
	 * @property {Object} slotObj 传入插槽的参数（因为插槽进行了循环，不能直接引用页面的参数，需要传递） 
	 * @property {Array} checkList 选中列表
	 * @property {Boolean} parent 当子级全选时,是否选中父级数据(prop.checkStrictly为true时生效)(默认值false)
	 * @property {Array} parentList 父级列表
	 * @property {String} checkActiveColor 选中时单选框的颜色 (默认#00AAFF)
	 * @property {String} checkNoneColor 未选中时单选框的颜色（默认#B8B8B8) 
	 * @property {Object} props 参数配置。
	 *  @property {String} id id列的属性名称 
	 * 	@param {String}  label 指定选项标签为选项对象的某个属性值(默认值:name)
	 *  @param {String} children   指定选项的子选项为选项对象的某个属性名(默认值：children)
	 *  @param {Boolean} hasChildren   是否包含下一级的属性名（默认user）
	 *  @param {Boolean} multiple 值为true时为多选，为false时是单选(默认值true)
	 *  @param {Boolean} checkStrictly(废弃) 需要在多选模式下才传该值，checkStrictly为false时，可让父子节点取消关联，选择任意一级选项。为true时关联子级，可以全选(默认值为false)
	 *  @param {Boolean} nodes  在单选模式下，nodes为false时，可以选择任意一级选项，nodes为true时，只能选择叶子节点(默认值为true) 
	 * @property {Boolean} stepReload 是否“分页加载”数据
	 * @property {Number} pageSize 分步加载生效时（当条数过大时，反应时间很长）
	 * @return {Function} clickItem 点击导航栏事件
	 * 	@value item 当前选中的item值
	 *  @value 	realHasChildren 是否包含子级
	 * @event {Function()} change 改变选择值时的方法
	 * @event {Function()} sendValue 提交选择的方法
	 * @event {Function()} backTree	选中导航栏时，返回其他层
	 */
	export default {
		name: "luyj-tree",
		props: {
			// 是否开启搜索
			searchIf: {
				type: Boolean,
				default: () => true
			},
			// 搜索框背景色
			searchBackgroundColor: {
				type: String,
				default: '#FFFFFF'
			},
			// 搜索框的输入框内背景颜色
			searchInputBackgroundColor: {
				type: String,
				default: '#EEEFF0'
			},
			// 搜索框的图标的颜色
			searchIconColor: {
				type: String,
				default: '#B8B8B8'
			},
			// 搜索框的圆角值，单位rpx
			searchRadius: {
				type: Number,
				default: 40
			},
			// 搜索框的提示placeholder内容
			searchPlaceholder: {
				type: String,
				default: '搜索'
			},
			// 搜索框的placeholder的样式
			searchPlaceholderStyle: {
				type: String,
				default: ''
			},
			// 搜索框最大输入长度 ,设置为 -1 的时候不限制最大长度
			searchMaxlength: {
				type: Number,
				default: 140
			},
			// 搜索框是否显示清除按钮
			searchClearable: {
				type: Boolean,
				default: true
			},
			// 传入的树形结构数据，每个对象必须包含唯一的id值
			trees: {
				type: Array,
				default: () => {
					return []
				}
			},
			//是否开启选择操作，值为false时仅展示，无操作
			isCheck: {
				type: Boolean,
				default: () => {
					return false
				}
			},
			// 传入插槽的其他参数
			slotObj: {
				type: Object,
				default :() =>{
					return null;
				}
			},
			// 选中列表
			checkList: {
				type: Array,
				default: () => []
			},
			// 当子级全选时,是否选中父级数据(prop.checkStrictly为true时生效)
			parent: {
				type: Boolean,
				default: () => {
					return false
				}
			},
			// 父级列表
			parentList: {
				type: Array,
				default: () => []
			},
			// 选中时单选框的颜色
			checkActiveColor: {
				type: String,
				default: '#00AAFF'
			},
			// 未选中时单选框的颜色
			checkNoneColor: {
				type: String,
				default: '#B8B8B8'
			},
			// 树的属性参数
			props: {
				type: Object,
				default: () => {
					return {
						id: 'id',
						label: 'name',
						children: 'children',
						hasChilren: 'user',
						multiple: false,
						checkStrictly: false, //不关联
						nodes: false, // nodes为false时，可以选择任意一级选项；nodes为true时只能选择叶子节点
					}
				}
			},
			/**
			 * 是否懒加载树的值
			 */
			stepReload : {
				type:Boolean,
				default:false
			},
			// 每次循环加载的item的数据量
			pageSize : {
				type : Number,
				default:50
			}
		},
		data() {
			return {
				// 导航条
				setIsre: null, // 导航条 - 设置是否搜索中方法
				getIsre : null,	// 获取是否搜索中
				setTreeStack: null, // 导航条 - 设置导航
				concatTreeStack: null, // 导航条 - 拼接当前导航对象
				clearTreeStack: null, // 导航条- 清空导航条
				getTreeStack: null, // 导航条 - 获取导航条
				
				itemsLoading : false,	// item是否在循环渲染中
				itemsStop : false,		// 是否终止其他渲染
				tree: [],			// 默认数组
				newNum: 0,
				oldNum: 0,
				allData: this.trees,
				parent_data: this.parentList || [], //选择父辈
				searchResult: [],
				newCheckList: this.checkList,
				nodePathArray: [],
				// item名称对照表
				comparison: {
					value: this.props.id ? this.props.id : 'id', // 选中值名称
					label: this.props.label ? this.props.label : 'name', // 显示名称
					hasChilren: this.props.hasChilren ? this.props.hasChilren : 'user', // 是否包含子集
					children: this.props.children ? this.props.children : 'children', // 子集名称
				}
			}
		},
		watch: {
			// 监听数据值的变化
			trees: function(val, oldval) {
				if (val != oldval) {
					var tree_stack = this.getTreeStack();
					this.allData = val;				// 重新加载所有树
					// 重新加载当前树
					if(!Array.isArray(tree_stack)){
						this.loadTree(val);
						return;
					}
					var length = tree_stack.length;
					if( length === 0){
						if(typeof(this.getIsre)  === "function"){
							if(this.getIsre()){
								return;
							}
						}
						this.loadTree(val);
					}else{
						let tempArray = val;		// 存储当前值
						let children = this.props.children;
						for(var i = 0 ; i < length ; i ++){
							var tempObject = tempArray.find(item=>{
								return tree_stack[i].Value == item.Value;
							});
							if(Boolean(tempObject)){
								tempArray = tempObject[children];
							}else{
								// 跳转到全部
								break;
							}
							if(i == length -1){
								this.loadTree(tempArray);
							}
						}
					}
				}
			},
			// 树的属性对照参数
			props: {
				handler: function(val) {
					this.comparison.value = this.props.id ? this.props.id : 'id';
					this.comparison.label = this.props.label ? this.props.label : 'name';
					this.comparison.hasChilren = this.props.hasChilren ? this.props.hasChilren : 'user';
					this.comparison.children = this.props.children ? this.props.children : [];
				},
				deep: true
			}
		},
		created:function(){
			this.loadTree(this.trees);
		},
		// 实例被挂载后调用
		mounted() {
			// 关联子级的验证，暂时不使用
			// if (this.props.multiple && this.props.checkStrictly) {
			// 	if (this.newCheckList.length != 0) {
			// 		this.checkAllChoose();
			// 		return;
			// 	}
			// 	for (let i = 0; i < this.tree.length; i++) {
			// 		this.$set(this.tree[i], 'bx', 0)
			// 		this.$set(this.tree[i], 'qx', 0)
			// 	}
			// }
			// 初始化选中项
			if (!this.props.multiple && this.newCheckList.length > 0) {
				this.getNodeRoute(this.allData, this.newCheckList[0][this.props.id])
				let arr = this.nodePathArray.reverse()
				if (arr.length == 0) {
					return;
				}
				this.concatTreeStack(arr);
				var tree_stack = this.getTreeStack();
				var data = Boolean(tree_stack[tree_stack.length - 1][props.children]) ? tree_stack[tree_stack.length - 1][
					props.children
				] : [];
				this.loadTree(data);
			}
		},
		methods: {
			// =========================================== 初始化方法 ===================================================================
			/** 初始化导航条的方法
			 * @param {Object} e
			 */
			navigationInt: function(e) {
				this.setIsre = e.setIsre;
				this.getIsre = e.getIsre;
				this.concatTreeStack = e.concatTreeStack;
				this.pushTreeStack = e.pushTreeStack;
				this.clearTreeStack = e.clearTreeStack;
				this.getTreeStack = e.getTreeStack;
			},
			// =========================================== 监听事件 =====================================================================
			/** 选中当前的值
			 * @param {Object} e
			 * @param {Object} item 当前项
			 * @param {Object} index 低昂去索引
			 */
			checkbox(e, item, index) {
				var func = this.props.multiple ?  this.checkboxChange : this.radioChange;
				func(e,item ,index);			// 执行选择操作
			},
			/**单选
			 * @param {Object} e 点击事件
			 * @param {Object} item 当前项的值
			 * @param {Object} index 索引
			 */
			radioChange :function( e,item ,index){
				var that = this;
				if(e.detail.value){
					// 选中当前对象
					that.newCheckList = [];
					that.newCheckList.push(that.tree[index]);
				}else{
					// 移除其他对象
					var nIndex = that.newCheckList.indexOf(item);
					that.newCheckList.splice(nIndex , 1);
				}
				that.$emit('change', that.newCheckList);
			},
			/**异步检查复选框值的改变
			 * @param {Object} item
			 * @param {Object} index
			 * @param {Object} bx
			 * @param {Object} qx
			 */
			 async checkboxChange (e,item, index, bx, qx) {
				let that = this;
				let findIdex = that.newCheckList.indexOf(item);
				if(e.detail.value){
					// 点击选中
					if(findIdex == -1){
						that.newCheckList.push(that.tree[index]);
					}
				}else{
					// 点击不选
					that.newCheckList.splice(findIdex , 1);
				}
				that.$emit('change', that.newCheckList);
				
				// if (findIdex > -1) { //反选
				// 	if (that.props.checkStrictly) { //关联子级
				// 		if (item[props.hasChilren]) { //用户
				// 			that.newCheckList.splice(findIdex, 1)
				// 		} else { //非用户，取消所有下一级
				// 			if (Boolean(item[props.children])) {
				// 				that.getIdBydelete(item[props.children]);
				// 			}
				// 		}
				// 	} else {
				// 		that.newCheckList.splice(findIdex, 1)
				// 	}
				// } else { //选中
				// 	if (!item[this.props.hasChilren] && that.props.checkStrictly) { //选中下一级
				// 		if (qx || bx) { //取消下级
				// 			if (Boolean(item[props.children])) {
				// 				await that.getIdBydelete(item[props.children]);
				// 			}
				// 			item.qx = 0;
				// 			item.bx = 0;
				// 		} else {
				// 			item.qx = 1;
				// 			item.bx = 0;
				// 			if (Boolean(item[props.children])) {
				// 				await that.chooseChild(item[props.children], item[this.props.id]);
				// 			}
				// 		}
				// 		that.$emit('change', that.newCheckList);
				// 		// that.$forceUpdate()
				// 		return;
				// 	}
				// 	// if(item[this.props.hasChilren]&&this.props.checkStrictly) this.getNodeRoute(this.allData,item[this.props.id]);
				// 	that.newCheckList.push({
				// 		...item
				// 	});
				// }
				// that.$emit('change', that.newCheckList)
			},
			// 取消下一级的选中
			getIdBydelete(arr) {
				arr.forEach(e => {
					if (e[this.props.hasChilren]) {
						for (var i = 0; i < this.newCheckList.length; i++) {
							if (e[this.props.id] == this.newCheckList[i][this.props.id]) {
								this.newCheckList.splice(i, 1)
								break;
							}
						}
					}
					if (!e[this.props.hasChilren]) {
						if (Boolean(item[props.children])) {
							this.getIdBydelete(e[props.children]);
						}
					}
				})
			},
			// 关联下一级,选中
			chooseChild(arr, pid) {
				let that = this;
				for (var i = 0, len = arr.length; i < len; i++) {
					let item = arr[i];
					if (item[that.props.hasChilren]) {
						that.newCheckList.push({
							...item,
							tree_stackId: pid
						})
					}
					if (!item[that.props.hasChilren]) {
						if (Boolean(item[props.children])) {
							this.chooseChild(item[props.children], item[this.props.id])
						}
					}
				}
			},

			// (tree为目标树，targetId为目标节点id) 
			getNodeRoute(tree, targetId) {
				for (let index = 0; index < tree.length; index++) {
					if (Boolean(item[props.children])) {
						if (tree[index][props.children]) {
							let endRecursiveLoop = this.getNodeRoute(tree[index][props.children], targetId)
							if (endRecursiveLoop) {
								this.nodePathArray.push(tree[index])
								return true
							}
						}
					}
					if (tree[index][this.props.id] === targetId) {
						return true;
					}
				}
			},
			
			/**跳转到子级
			 * @param {Object} item 选中的项
			 * @param {Boolean} realHasChildren 是否包含子级
			 */
			toChildren(item, realHasChildren) {
				this.$emit("clickItem" , item , realHasChildren);		// 点击导航栏事件
				// 不包含子级,不执行任何操作
				if (!realHasChildren) {
					return;
				}
				// 点击跳转下一级
				let id = this.props.id;
				let hasChildren = this.props.hasChilren; // 是否包含子级名称
				let children = this.props.children; // 子级名称

				// 将当前item加入到导航列表
				if (!item[hasChildren] && item[children].length > 0) {
					this.loadTree(item[children]);
					this.pushTreeStack(item); // 添加导航
				}
				// 关联数据 - 暂时不使用
				// if (this.props.checkStrictly) {
				// 	this.checkAllChoose();
				// }
			},
			/** 搜索提交方法
			 * @param {Object} e
			 */
			confirmSearch(e) {
				var val = e.detail.value;
				this.searchResult = [];
				// 查找
				uni.showLoading({
					title: '正在查找'
				});
				this.search(this.allData, val);
				// 返回搜索结果
				uni.hideLoading();
				this.setIsre(true); // 设置导航条为搜索状态
				this.clearTreeStack(); // 清空导航条
				this.loadTree(this.searchResult);
			},
			/**搜索方法
			 * @param {Object} data 搜索数据
			 * @param {Object} keyword 搜索关键字
			 */
			search(data, keyword) {
				var that = this;
				let children = that.props.children;
				for (var i = 0, len = data.length; i < len; i++) {
					// try-catch(try-catch) - 没有label列，跳过继续执行
					try{
						if (data[i][that.props.label].indexOf(keyword) >= 0) {
							that.searchResult.push(data[i]);
						}
						if (Boolean(data[i][children])) {
							if (!data[i][that.props.hasChilren] && data[i][children].length > 0) {
								that.search(data[i][children], keyword);
							}
						}
					}catch(e){
						console.warn(e);
					}
					
				}
			},
			/**
			 * 检查所有的选项
			 */
			checkAllChoose() {
				let o = false,
					t = true;
				this.tree.forEach((e, i) => {
					if (!e[this.props.hasChilren]) {
						e.qx = o;
						e.bx = o;
						let num2 = this.computAllNumber(e[props.children]);
						// console.log(this.newNum,this.oldNum)
						if (this.newNum != 0 && this.oldNum != 0) {
							if (this.newNum == this.oldNum) {
								e.qx = t;
								e.bx = o;
							} else {
								e.qx = o;
								e.bx = t;
							}
						}
						if (this.newNum != 0 && this.oldNum == 0) {
							this.$set(this.tree[i], 'bx', o);
							this.$set(this.tree[i], 'qx', o);
						}
						// this.$forceUpdate()
						this.newNum = 0
						this.oldNum = 0
					}
				})
			},

			// 选中所选值
			computAllNumber(arr) {
				for (let j = 0; j < arr.length; j++) {
					var e = arr[j];
					if (arr[j][that.props.hasChilren]) {
						this.newNum++;
					}
					this.checkSum(e.id)
					if (!e[thata.props.hasChilren]) {
						this.computAllNumber(e[props.children])
					}
				}
			},

			// 选中事件累计
			checkSum(id) {
				for (let i = 0; i < this.newCheckList.length; i++) {
					if (id == this.newCheckList[i].id) {
						this.oldNum++;
						break
					}
				}
			},

			/** 返回到其他树层
			 * @param {Object} item 当前item值
			 * @param {Object} index 返回到其他索引
			 */
			backTree(item, index) {
				this.$emit("backTree", item, index);
				let that = this;
				if (index == -1) {
					// 全部
					that.loadTree(that.allData);
				} else if (index == -2) {
					// 搜索
					that.loadTree(that.searchResult);		// 搜索结果
				} else {
					// 其他层级
					that.loadTree(item[that.props.children]);	// tree的其他层级
				}
				// 关联数据
				// if (this.props.checkStrictly) {
				// 	this.checkAllChoose();
				// }
			},
			/**
			 * 点击确认按钮执行事件
			 */
			backConfirm() {
				this.$emit('sendValue', this.newCheckList, 'back')
			},
			// ======================================== 公共方法 ===============================================================
			/**加载Tree值
			 * @param {Array} datas 待复制的数组
			 * @param {Number} start 起始位置
			 * @description 加载tree值。当数据量大时，子项加载时间很长。可以多次渲染加载
			 */
			loadTree : function(datas , start = 0 ){
				let that = this;
				if(!this.stepReload){
					// 不进行多次渲染加载
					that.tree = datas;
				}else{
					// datas为null, 不进行渲染
					if(!Array.isArray(datas)){
						that.tree = datas;
						return;
					}else if(datas.length === 0){
						that.tree = datas;
						return;
					}
					// 进行多次渲染加载
					if(start === 0){
						// 终止其他渲染
						if(that.itemsLoading){
							that.itemsStop = true;		//终止其他Item渲染
						}
						// 首次加载提醒
						uni.showLoading();
						that.tree = [];
						that.itemsLoading = true;
					}
					var length =  datas.length ;
					var end = Math.min(start + that.pageSize , length);
					var tempArray  = datas.slice(start , end);
					that.tree = that.tree.concat(tempArray);
					that.$nextTick(function(){
						if(start == 0){
							uni.hideLoading();
							that.itemsStop = false;
						}
						if(end < length && !that.itemsStop){
							that.loadTree(datas, end);
						}else{
							that.itemsLoading = false;
						}
					});
				}
			},
			// =================================================================================================================
			
		}
	}
</script>
<style lang="scss" scoped>
	@import "luyj-tree.scss";
	@import "icon.css";
</style>
