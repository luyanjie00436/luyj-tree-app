<template>
	<view>
		<!-- 搜索框 -->
		<view class="header">
			<luyj-tree-search v-if="searchIf" ref="sea" @confirm="confirmSearch" />
			<view class="title">
				<scroll-view scroll-x style="width: 100%;white-space: nowrap;" :scroll-left="scrollLeft">
					<view v-for="(item,index) in tree_stack" class="inline-item" :key="index">
						<view class="inline-item" v-if="index==0" @click="backTree(item,-1)">
							<text v-if="index==tree_stack.length-1&&!isre" class="none">全部</text>
							<text v-else class="active">全部</text>
						</view>
						<view v-if="index==0&&isre" @click="backTree(item,-2)"
							:class="[index==tree_stack.length-1&&isre]?'none inline-item':'active inline-item'">
							<i class="iconfont icon-z043 iconclass" />
							搜索结果
						</view>
						<view class="inline-item" @click="backTree(item,index)" v-if="index!=0">
							<i v-if="index!=0" class="iconfont icon-z043 iconclass" />
							<text v-if="index==tree_stack.length-1" class="none inline-item">
								{{item[props.label]}}
							</text>
							<text v-else class="active">
								{{item[props.label]}}
							</text>
						</view>
					</view>
				</scroll-view>
			</view>
		</view>
		<!-- 列表 -->
		<view>
			<view class="container-list">
				<view class="common" v-for="(item, index) in tree" @click="toChildren(item)" :key="index">
					<label class="content">
						<view class="checkbox" v-if="isCheck&&props.multiple" @click.stop="checkboxChange(item,index,item.bx,item.qx)">
							<span v-if="(newCheckList.findIndex(e=>{return item.id==e.id}))>-1&&!item.qx">
								<i v-if="item.bx&&props.multiple" class="iconfont icon-banxuanzhongshousuo1-shi icons"/>
								<i v-else class="iconfont icon-xuanzhong txt icon-selected"/>
							</span>
							<i v-else-if="props.multiple&&item.qx" class="iconfont icon-xuanzhong txt icon-selected"/>
							<i v-else-if="item.bx&&props.multiple" class="iconfont icon-banxuanzhongshousuo1-shi icons"/>
							<i style="color: #b8b8b8;" v-else class="iconfont icon-weixuanzhong txt"/>
						</view>
						<view class="checkbox" v-if="isCheck&&!props.multiple&&props.nodes&&item.user" @click.stop="checkbox(item,index)">
							<i v-if="newCheckList.length>0&&item.id == newCheckList[0].id" class="txt iconfont icon-selected"/>
							<i style="color: #b8b8b8;" v-else class="txt iconfont icon-weixuanzhong1"/>
						</view>
						<view class="checkbox" v-if="isCheck&&!props.multiple&&!props.nodes" @click.stop="checkbox(item,index)">
							<i v-if="newCheckList.length>0&&item.id == newCheckList[0].id" class="txt iconfont icon-selected"/>
							<i style="color: #b8b8b8;" v-else class="txt iconfont icon-weixuanzhong1"/>
						</view>
						<view v-if="item.user" @click.stop="checkboxChange(item,index,item.bx,item.qx)"><slot v-bind:item="item"></slot></view>
						<slot v-else v-bind:item="item">
						</slot>
						<view class="right"><i v-if="!item.user&&item.children.length>0" class="iconfont icon-z043"></i></view>
					</label>
				</view>
			</view>
		</view>
		<!-- 确定按钮 -->
		<view class="btn box_sizing">
			<button class="sureBtn" type="primary" @click="backConfirm">确认</button>
		</view>
	</view>
</template>

<script>
	/**
	 * luyj-tree 无限树形结构组支持搜索选择。
	 * @description  无限树形结构组件。支持搜索、选择(包括单选、多选)。面包屑类型导航。原插件地址：https://ext.dcloud.net.cn/plugin?id=2423。
	 * @tutorial url 
	 * @property {Array}  trees 传入的树形结构，每个对象必须包含唯一的id值(默认值【】)
	 * @property {Boolean} isCheck 是否开启选择操作（默认值false）
	 * @property {Boolean} searchIf 是否开启搜索 （默认值true）
	 * @property {Number} max 最大的level层数（默认值-1）
	 * @property {Array} checkList 选中列表
	 * @property {Boolean} parent 当子级全选时,是否选中父级数据(prop.checkStrictly为true时生效)(默认值false)
	 * @property {Array} parentList 父级列表
	 * @property {Object} props 参数配置。
	 * 	@param {String}  label 指定选项标签为选项对象的某个属性值(默认值:name)
	 *  @param {String} children   指定选项的子选项为选项对象的某个属性值(默认值：children)
	 *  @param {Boolean} multiple 值为true时为多选，为false时是单选(默认值true)
	 *  @param {Boolean} checkStrictly 需要在多选模式下才传该值，checkStrictly为false时，可让父子节点取消关联，选择任意一级选项。为true时关联子级，可以全选(默认值为false)
	 *  @param {Boolean} nodes  在单选模式下，nodes为false时，可以选择任意一级选项，nodes为true时，只能选择叶子节点(默认值为true) 
	 * 
	 */
	export default {
		name: "luyj-tree",
		props: {
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
			// 是否开启搜索
			searchIf: {
				type: Boolean,
				default: () => true
			},
			// 最大level层数
			max: {
				type: Number,
				default: () => {
					return -1;
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
			// 树的属性参数
			props: {
				type: Object,
				default: () => {
					return {
						label: 'name',
						children: 'children',
						multiple: false,
						checkStrictly: false, //不关联
					}
				}
			}
		},
		data() {
			return {
				isre: false,
				tree: this.trees,
				newNum: 0,
				oldNum: 0,
				allData: this.trees,
				tree_stack: [1],
				parent_data: this.parentList || [], //选择父辈
				searchResult: [],
				tochild: false,
				newCheckList: this.checkList,
				scrollLeft: 999,
				nodePathArray: []
			}
		},
		// 实例被挂载后调用
		mounted() {
			if(this.props.multiple&&this.props.checkStrictly){
				if(this.newCheckList.length!=0){
					 this.checkAllChoose();
					 return
				}
				for(let i = 0; i<this.tree.length;i++){
					this.$set(this.tree[i],'bx',0)
					this.$set(this.tree[i],'qx',0)
				}
			}
			if(!this.props.multiple&&this.newCheckList.length>0) {
				this.getNodeRoute(this.allData,this.newCheckList[0].id)
				let arr = this.nodePathArray.reverse()
				if(arr.length == 0)return
				this.tree_stack = this.tree_stack.concat(arr);
				this.tree = this.tree_stack[this.tree_stack.length-1].children;
			}
		},
		methods: {
			//多选
			async checkboxChange(item, index, bx, qx) {
				var that = this;
				if (!this.props.multiple) return;
				let findIdex = that.newCheckList.findIndex(e => {
					return item.id == e.id
				});
				if (findIdex > -1) { //反选
					if (that.props.checkStrictly) { //关联子级
						if (item.user) { //用户
							that.newCheckList.splice(findIdex, 1)
						} else { //非用户，取消所有下一级
							that.getIdBydelete(item.children)
						}
					} else {
						that.newCheckList.splice(findIdex, 1)
					}
				} else { //选中
					if (!item.user && that.props.checkStrictly) { //选中下一级
						if (qx || bx) { //取消下级
							await that.getIdBydelete(item.children);
							item.qx = 0;
							item.bx = 0;
						} else {
							item.qx = 1;
							item.bx = 0;
							await that.chooseChild(item.children, item.id);
						}
						that.$emit('sendValue', that.newCheckList);
						that.$forceUpdate()
						return
					}
					// if(item.user&&this.props.checkStrictly) this.getNodeRoute(this.allData,item.id);
					that.newCheckList.push({
						...item
					});
				}
				that.$emit('sendValue', that.newCheckList)
			},
			// 取消下一级的选中
			getIdBydelete(arr) {
				arr.forEach(e => {
					if (e.user) {
						for (var i = 0; i < this.newCheckList.length; i++) {
							if (e.id == this.newCheckList[i].id) {
								this.newCheckList.splice(i, 1)
								break;
							}
						}
					}
					if (!e.user) {
						this.getIdBydelete(e.children)
					}
				})
			},
			//取消父级
			// deleteParent(id){
			// 	for(var i = 0; i<this.parent_data.length;i++){
			// 		if(id == this.parent_data[i].id) {
			// 			this.parent_data.splice(i,1)
			// 			break;
			// 		}
			// 	}
			// },

			// 关联下一级,选中
			chooseChild(arr, pid) {
				let that = this;
				for (var i = 0, len = arr.length; i < len; i++) {
					let item = arr[i];
					if (item.user) {
						that.newCheckList.push({
							...item,
							tree_stackId: pid
						})
					}
					if (!item.user) {
						this.chooseChild(item.children, item.id)
					}
				}
			},

			// (tree为目标树，targetId为目标节点id) 
			getNodeRoute(tree, targetId) {
				for (let index = 0; index < tree.length; index++) {
					if (tree[index].children) {
						let endRecursiveLoop = this.getNodeRoute(tree[index].children, targetId)
						if (endRecursiveLoop) {
							this.nodePathArray.push(tree[index])
							return true
						}
					}
					if (tree[index].id === targetId) {
						return true
					}
				}
			},

			//单选
			checkbox(item, index) {
				var that = this;
				that.newCheckList = []
				that.newCheckList.push(that.tree[index])
				that.$emit('sendValue', that.newCheckList)
			},
			//到下一级
			toChildren(item) {
				console.log(this.tree_stack)
				if (item.user) return
				var that = this;
				this.tochild = true;
				let children = that.props.children;
				if (!item.user && item[children].length > 0) {
					that.tree = item[children];
					if (that.tree_stack[0].id == item.id) {} else {
						that.tree_stack.push(item);
					}
				}
				this.$nextTick(() => {
					this.scrollLeft += 200;
				})
				if (this.props.checkStrictly) this.checkAllChoose();
				this.$forceUpdate()
			},
			//搜索
			confirmSearch(val) {
				this.searchResult = []
				this.search(this.allData, val)
				this.isre = true
				this.tree_stack.splice(1, 6666)
				uni.showLoading({
					title: '正在查找'
				})
				setTimeout(() => {
					this.tree = this.searchResult
					uni.hideLoading()
				}, 300)
			},
			search(data, keyword) {
				var that = this
				let children = that.props.children
				for (var i = 0, len = data.length; i < len; i++) {
					if (data[i].name.indexOf(keyword) >= 0) {
						that.searchResult.push(data[i])
					}
					if (!data[i].user && data[i][children].length > 0) {
						that.search(data[i][children], keyword)
					}
				}
			},

			checkAllChoose() {
				let o = false,
					t = true;
				this.tree.forEach((e, i) => {
					if (!e.user) {
						e.qx = o;
						e.bx = o;
						let num2 = this.computAllNumber(e.children);
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
						this.$forceUpdate()
						this.newNum = 0
						this.oldNum = 0
					}
				})
			},

			computAllNumber(arr) {
				for (let j = 0; j < arr.length; j++) {
					var e = arr[j];
					if (arr[j].user) {
						this.newNum++;
					}
					this.checkSum(e.id)
					if (!e.user) {
						this.computAllNumber(e.children)
					}
				}
			},

			checkSum(id) {
				for (let i = 0; i < this.newCheckList.length; i++) {
					if (id == this.newCheckList[i].id) {
						this.oldNum++;
						break
					}
				}
			},

			//返回其它层
			backTree(item, index) {
				let that = this,
					max = 66666;
				if (index == -1) {
					that.tree = that.allData
					that.tree_stack.splice(1, max)
					that.isre = false
					that.$refs.sea.clears()
				} else if (index == -2) {
					that.tree = that.searchResult
					that.tree_stack.splice(1, max)
				} else {
					if (that.tree_stack.length - index > 2) {
						that.tree_stack.forEach((item, i) => {
							if (i > index) {
								that.tree_stack.splice(i, max)
							}
						})
					} else if (index == that.tree_stack.length - 1) {

					} else {
						that.tree_stack.splice(that.tree_stack.length - 1, 1)
					}
					that.tree = item[that.props.children]
				}
				if (this.props.checkStrictly) this.checkAllChoose();
				this.$forceUpdate()
			},
			backConfirm() {
				this.$emit('sendValue', this.newCheckList, 'back')
			}
		}
	}
</script>
<style lang="scss" scoped>
	@import "luyj-tree.scss";
	@import "icon.css";
</style>
