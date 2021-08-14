<template>
	<view class="container-list-item" @click="toChildren(item)">
		<label class="content">
			<!-- 单选框/多选框 -->
			<!-- <view class="checkbox" v-if="isCheck&&props.multiple" @click.stop="checkboxChange(item,index,item.bx,item.qx)">
				<span v-if="(newCheckList.findIndex(e=>{return item.id==e.id}))>-1&&!item.qx">
					<i v-if="item.bx&&props.multiple" class="iconfont icon-banxuanzhongshousuo1-shi icons"/>
					<i v-else class="iconfont icon-xuanzhong txt icon-selected"/>
				</span>
				<i v-else-if="props.multiple&&item.qx" class="iconfont icon-xuanzhong txt icon-selected"/>
				<i v-else-if="item.bx&&props.multiple" class="iconfont icon-banxuanzhongshousuo1-shi icons"/>
				<i style="color: #b8b8b8;" v-else class="iconfont icon-weixuanzhong txt"/>
			</view> -->
			<!-- <view class="checkbox" v-if="isCheck&&!props.multiple&&props.nodes&&item.user" @click.stop="checkbox(item,index)">
				<i v-if="newCheckList.length>0&&item.id == newCheckList[0].id" class="txt iconfont icon-selected"/>
				<i style="color: #b8b8b8;" v-else class="txt iconfont icon-weixuanzhong1"/>
			</view> -->
			<!-- <view class="checkbox" v-if="isCheck&&!props.multiple&&!props.nodes" @click.stop="checkbox(item,index)">
				<i v-if="newCheckList.length>0&&item.id == newCheckList[0].id" class="txt iconfont icon-selected"/>
				<i style="color: #b8b8b8;" v-else class="txt iconfont icon-weixuanzhong1"/>
			</view> -->
			<!-- 自定义的slot -->
			<view v-if="item.user" @click.stop="checkboxChange(item,index,item.bx,item.qx)">
				<slot v-bind:item="item" >
					<view class="word"> {{ item.name }}</view>
				</slot>
			</view>
			<slot v-else v-bind:item="item" >
				<view class="word">{{ item.name }}</view> 
			</slot>
			<view class="right"><i v-if="true" class="iconfont icon-z043"></i></view>
		</label>
	</view>
</template>

<script>
	/**
	 * 无限极树的单项 item
	 * @description无限级数的单项item
	 * @property {Object} item 单项的值（默认null） 
	 * 
	 */
	export default {
		name:'luyj-tree-item',
		props:{
			item :{
				type:Object,
				default: null
			}
		},
		components:{
			// 是否包含子级
			hasNext : function(){
				// !item.user&&item.children.length>0
				return true;
			}
		},
		methods:{
			/** 点击跳转下一级
			 * @param {Object} item
			 */
			toChildren : function (item){
				console.log("点击跳转下一级");
			}
		}
	}
</script>

<style lang="scss" scoped>
	@import  'luyj-tree-item.scss';
	@import "icon.css";
</style>
