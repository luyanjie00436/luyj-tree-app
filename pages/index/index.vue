<template>
	<view>
		<uni-section title="非选择模式" type="line"></uni-section>
		<button class="btn bg-blue" @click="toChoose(query,-1 ,false,'展示')" type="primary">展示</button>
		<uni-section title="选择模式" type="line"></uni-section>
		<view class="margin">
			<!-- 用户选中的值 -->
			<view class="children padding" style="border: 1upx solid rgba(0, 0, 0, 0.1);">
				<view v-for="(item,index) in query" style="margin: 2px;display: inline-block;" :key="index">
					<text>{{item.name}};</text>
				</view>
				<view v-if="query.length === 0" style="margin: 2px;display: inline-block;" >
					<text>显示用户选中的值</text>
				</view>
			</view>
			<!-- 跳转到参数页 -->
			<button class="btn bg-blue" @click="toChoose(query,0,true,'多选模式（选择任意一项）')" type="primary">多选模式（选择任意一项）</button>
			<button class="btn bg-blue" @click="toChoose(query,1,true,'多选模式（关联下级）')" type="primary">多选模式（关联下级）</button>
			<button class="btn bg-blue" @click="toChoose(query,2,true,'单选模式（任意一项）')" type="primary">单选模式(任意一项)</button>
			<button class="btn bg-blue" @click="toChoose(query,3 , true,'单选（只选user）')" type="primary">单选（只选user）</button>
			<button class="btn " @click="clear()" type="default">清空选择</button>
		</view>

	</view>

</template>

<script>
	/**
	 * 初始的引导页
	 */
	export default {
		data() {
			return {
				query: [],
				parent_list: [],
				detail: {
					userIds: [],
					organizeIds: []
				},
				sendObj: [],
				uoData: [],
				isChoose: []
			}
		},
		onShow() {
			//兼容小程序,获取选中的值
			//#ifdef MP-WEIXIN||MP-QQ
			let pages = getCurrentPages();
			let currPage = pages[pages.length - 1];
			this.query = currPage.data.query;
			// #endif
		},
		methods: {
			/** 选择数的内容
			 * @param {Object} item 选中值
			 * @param {Object} type 类型 0：多选模式（任意选一项），1：多选模式：关联下级 ： 2:单选模式（任意一项）：3：单选（只选User）
			 * @param {Boolean} isCheck 是否选择模式
			 * @param {String} title 标题
			 */
			toChoose(item, type, isCheck = false ,title = '') {
				// #ifdef H5
				let items = encodeURIComponent(JSON.stringify(this.query));
				// #endif
				// #ifdef MP-QQ||MP-WEIXIN
				let items = JSON.stringify(this.query);
				// #endif
				uni.navigateTo({
					url: `../chooseUser/chooseUser?arr=${items}&type=${type}&isCheck=${isCheck}&title=${title}`
				})
			},
			clear() {
				this.query = [];
				this.isChoose = []
			}
		}
	}
</script>

<style scoped lang="scss">
	.margin{
		margin: 30upx;
	}
	.padding{
		padding: 30upx;
	}
	.children {
		color: #BBB2B2;
	}

	.parent {
		margin-top: 30px;
	}

	.color {
		color: #bbb2b2;
	}

	.btn {
		margin: 10px auto;
	}
	.bg-blue{
		background-color: #007AFF;
	}
</style>
