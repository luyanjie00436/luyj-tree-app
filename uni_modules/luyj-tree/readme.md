# luyj-tree
> 代码块 `luyj-tree` 、`luyj-search`

## 说明

本插件是基于[xiaolu-tree](https://ext.dcloud.net.cn/plugin?id=2423)进行了uni_modules模块化。并进行了一些修改。

### 安装方式

本组件符合[easycom](https://uniapp.dcloud.io/collocation/pages?id=easycom)规范，`HBuilderX 2.5.5`起，只需将本组件导入项目，在页面`template`中即可直接使用，无需在页面中`import`和注册`components`。

### 基本用法

在 ``template`` 中使用组件

```html
<!-- 基础用法 -->
<luyj-tree v-if="tree.length > 0" v-slot:default="{item}" :max="max" :trees="tree">
	<!-- 内容插槽 -->
	<view>
		<view class="content-item">
			<view class="word">{{item.name}}</view>
		</view>
	</view>
</luyj-tree>
```

``` javascript
import dataList from '@/common/data.js'; // 引用数据
export default {
	data() {
		return {
			tree: dataList,
			max: 5,
		}
	},
}
```
### 功能说明

1. 树形结构展示。
2. 包含搜索框。能够直接搜索树形图。子文件的内容。
3. 包含面包屑导航。
4. 可以仅仅展示或选择树形的项内容。
5. 单选状态可以回显选中位置路径。
6. 只需传checkList字段就可以回显默认选中。
7. 支持自定义显示内容（slot），自定义内容样式。
8. 动态展示数据。仅支持展示+单选

### 属性

属性名			|类型		|默认值		|	说明																									
:-:				|:-:		|:-:		|	:-:	
trees			|Array		|[]			|	trees 传入的树形结构，每个对象必须包含唯一的id值
isCheck			|Boolean	|false		|	是否开启选择操作
searchIf		|Boolean	|true		|	是否开启搜索
max				|Number		|-1			|	最大的level层数
checkList		|Array		|[]			|	选中的列表
parent			|Boolean	|false		|	当子级全选时,是否选中父级数据(prop.checkStrictly为true时生效)
parentList		|Array		|[]			|	父级列表
props			|Object		|{label:'name',children:'children'}	|	参数配置，详细见下表

#### props 参数说明
参数				|类型		|默认值		|	说明																									
:-:				|:-:		|:-:		|	:-:	
label			|String		|name		|	指定选项标签为选项对象的某个属性值
children		|String		|children	|	指定选项的子选项为选项对象的某个属性值
multiple		|Boolean	|true		|	值为true时为多选，为false时是单选
checkStrictly	|Boolean	|false		|	需要在多选模式下才传该值，checkStrictly为false时，可让父子节点取消关联，选择任意一级选项。为true时关联子级，可以全选
nodes			|Boolean	|true		|	在单选模式下，nodes为false时，可以选择任意一级选项，nodes为true时，只能选择叶子节点

### 事件

事件名			|说明		|返回值																								
:-:				|:-:		|:-:
@selectitem		|点击项目触发的事件	|参数（索引，具体项目）