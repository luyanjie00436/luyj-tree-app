<template>
	<view class="container-list-item" @click="clickItem(item)">
		<label class="content">
			<!-- 复选框 -->
			<view class="checkbox" v-if="isCheckBox" @click.stop="clickBox">
				<i v-if="curChecked " :style="{'color' :checkActiveColor}"  class="iconfont icon-xuanzhong txt icon-selected"/>
				<i v-else :style="{'color': checkNoneColor}" class="iconfont icon-weixuanzhong txt"/>
			</view>
			<!-- 复选框 -->
			<!-- 单选框 -->
			<view class="checkbox" v-else-if ="isRadio" @click.stop="clickBox">
				<i v-if="curChecked" :style="{'color' :checkActiveColor}"  class="txt iconfont icon-selected"/>
				<i v-else :style="{'color': checkNoneColor}"  class="txt iconfont icon-weixuanzhong1"/>
			</view>
			<!-- 单选框 -->
			<!-- 自定义插槽body -->
			<slot name="body"  >
				<view class="word" >{{ item[sLabel] }}</view> 
			</slot>
			<!-- 自定义插槽body -->
			<view v-if="hasNext" class="right"><i  class="iconfont icon-z043"></i></view>
		</label>
	</view>
</template>

<script>
	/**
	 * 无限极树的单项 item
	 * @description无限级数的单项item
	 * @property {Object} item 单项的值（默认null） 
	 * @property {Boolean} isCheck 判断是否可选 (默认false)
	 * @property {Boolean} 判断是否是否多选（默认false， isCheck为true时有效）。
	 * @property {String} checkActiveColor 选中状态下单选框/复选框的颜色 (默认#00AAFF)
	 * @property {String} checkNoneColor 未选中状态下单选框/复选框的颜色（默认#B8B8B8) 
	 * @property {Object} comparison 属性名称对照表 
	 * 	@param {String} value 选中值对应列名称(默认value)
	 *  @param {String}  label 显示值对应列名称（默认label）
	 *  @param {String} hasChilren 是否包含子级列对应名称（hasChilren）
	 *  @param {String}  children 子级列对应名称（默认children）
	 * @return {Function} 点击当前项的执行方法
	 * 	@param  {Object} item 当前项的值
	 *  @param {Boolean}  hasChildren 当前项是否包含子级(null 和长度为0 时为false)
	 * @return {Function} change 选中值变化时执行方法（event.detail = {value: 是否选中}）
	 */
	export default {
		name:'luyj-tree-item',
		props:{
			// 传入的数值
			item :{
				type:Object,
				default: null
			},
			// 判断是否可选
			isCheck : {
				type: Boolean,
				default: false
			},
			// 是否多选
			multiple:{
				type: Boolean,
				default: false
			},
			// 是否选中状态
			checked : {
				type:Boolean,
				default:false
			},
			// 是否只能选择叶子结点
			nodes : {
				type: false,
				default:true
			},
			// 选中状态下单选框/复选框的颜色
			checkActiveColor : {
				type:String,
				default:'#00AAFF'
			},
			// 未选中状态下单选框的颜色
			checkNoneColor : {
				type:String,
				default:'#B8B8B8'
			},
			// 列名称对照表
			comparison :{
				type:Object,
				default: () => {
					return {
						value : 'value',			// 选中值
						label : 'label',			// 显示名称
						hasChilren:'hasChilren',		// 是否包含子集
						children:'children',	// 子集名称
					};
				}
			}
		},
		data() {
			return {
				sValue : this.comparison.label? this.comparison.value :'value' ,					// value值名称
				sLabel: this.comparison.label?  this.comparison.label :'label',						// label值名称
				sHasChildren:this.comparison.hasChilren ? this.comparison.hasChilren : 'hasChilren',		// 是否包含children值名称
				sChildren: this.comparison.children ? this.comparison.children : [],			// children值名称
				curChecked : this.checked,	//是否选中状态
			}
		},
		computed:{
			/**
			 * 是否包含子级
			 */
			hasNext : function(){
				var realhasNext = Boolean(this.item[this.sChildren]) ? this.item[this.sChildren].length > 0  : false;
				return !this.item[this.sHasChildren] && realhasNext;
			},
			/**
			 * 是否单选
			 */
			isRadio : function(){
				return this.isCheck && !this.multiple && (!this.nodes || !this.hasNext);
			},
			/**
			 * 是否多选
			 */
			isCheckBox :function(){
				return this.isCheck && this.multiple && (!this.nodes || !this.hasNext);
			},
			
		},
		watch:{
			// 监听列名对照表变化
			comparison :{
				handler: function(val){
					this.sValue = this.comparison.value? this.comparison.value :'value';
					this.sLabel =  this.comparison.label?  this.comparison.label :'label';
					this.sHasChildren = this.comparison.hasChilren ? this.comparison.hasChilren : 'hasChilren';
					this.sChildren  = this.comparison.sChildren ? this.comparison.sChildren : [];
				},
				deep: true
			},
			// item:{
			// 	handler : function(val , oldVal){
			// 		// console.log("item值变化=>" ,val , oldVal);
			// 	}
			// }
			// 是否选中状态
			checked : function(val){
				this.curChecked = val;
			}
		},
		methods:{
			/** 点击当前项的执行方法
			 * @param {Object} item
			 */
			clickItem : function (item){
				this.$emit("clickItem" , item , this.hasNext);
			},
			/**
			 * 点击单选框或复选框
			 */
			clickBox : function(e){
				this.curChecked = !this.curChecked;
				e.detail.value = this.curChecked;
				this.$emit("change" , e);			// 切换单选框或复选框
			},
			
		}
	}
</script>

<style lang="scss" scoped>
	@import  'luyj-tree-item.scss';
	@import "icon.css";
</style>
