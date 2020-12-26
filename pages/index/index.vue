<template>


	<view>
		<view class="search-box">
			<!-- mSearch组件 如果使用原样式，删除组件元素-->
			<!-- <mSearch class="mSearch-input-box" :mode="2" button="inside" :placeholder="defaultKeyword" @search="doSearch(false)" @input="inputChange" @confirm="doSearch(false)" v-model="keyword"></mSearch> -->
			<!-- 原样式 如果使用原样式，恢复下方注销代码 -->

			<view class="input-box">
				<input type="text" :adjust-position="true" @focus="inputChange" placeholder="添加基金" placeholder-class="placeholder-class"
				 confirm-type="search">
			</view>
			<!-- <view class="search-btn" @tap="inputChange">搜索</view> -->

			<!-- 原样式 end -->
		</view>

		<div class="content">
			<image class="logo" src="/static/b.jpg"></image>
			<!-- <uni-ec-canvas class="uni-ec-canvas" id="line-chart" ref="canvas" canvas-id="lazy-load-chart" :ec="ec"></uni-ec-canvas> -->
		</div>
		<!-- 新建云函数 -->
<!-- 		<view class="uploader">
			<navigator url="../demo/addFunction/addFunction" open-type="navigate" class="uploader-text"><text>测试SUM</text></navigator>
		</view> -->
		
		<div class="content" v-if="listMyData.length>0">
			<input class="uni-input" type="digit" @input="onKeyInput" placeholder="临时输入估算净值" />
		</div>


		<div class="content" v-if="listMyData.length<=0">
			<button type="default" @click="inputChange">请先添加基金</button>
		</div>
		<div v-else>
			<uni-list>
				<template v-for="(item,index) in listMyData">
					<uni-list-item :title="item.fundName" :to="`../fund/fundCalcPage/fundCalcPage?fundCode=`+item.fundCode+`&value=`+ encodeURIComponent(inputValue)"
					 rightText="详情" @click="onClick(item.fundName)" />
				</template>
			</uni-list>
		</div>
			
	</view>

</template>

<script>
	import uniEcCanvas from "../../uni-ec-canvas/uni-ec-canvas.vue";
	import uniSearchBar from '../../components/uni-search-bar/uni-search-bar.vue'
	import mSearch from '../../components/mehaotian-search-revision/mehaotian-search-revision.vue';
	export default {

		data() {
			return {
				inputValue: 3.0000,
				result: '',
				listMyData: 1
			}
		},
		onReady() {

		},
		components: {
			uniEcCanvas,
			uniSearchBar,
			mSearch
		},
		onLoad() {
			wx.cloud.callFunction({
				name: 'login',
				success: res => {
					const db = wx.cloud.database() //初始化数据库
					db.collection('fundData').where({
						_openid: res.result.openid
					}).get({
						success: (res) => {
							this.listMyData = res.data;
						}
					})
				},
				fail: err => {
					console.error('调用失败：', err);
				}
			});

		},
		methods: {
			onClick(title) {
				// console.log('执行click事件', e)
				uni.setNavigationBarTitle({
					title: title
				});
				const db = wx.cloud.database() //初始化数据库	
				const _ = db.command
				db.collection('fund').where(_.or([{
						F_NAME: db.RegExp({
							regexp: '.*' + this.inputValue + '.*',
							option: 'i'
						})
					},
					{
						F_CODE: db.RegExp({
							regexp: '.*' + this.inputValue + '.*',
							option: 'i'
						})
					},
				])).get({
					success: function(res) {
						console.log(res.data)
					}
				})
			},
			onKeyInput: function(event) {
				this.inputValue = event.target.value
				console.log(this.inputValue)
			},
			search: function(event) {

				console.log(event)
			},
			inputChange: function(event) {
				console.log(event)
				uni.redirectTo({
					url: '../fund/fundSearchPage/fundSearchPage',
					success: res => {},
					fail: () => {},
					complete: () => {}
				});

			},
		}
	}
</script>

<style>
	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}

	.logo {
		height: 200rpx;
		width: 200rpx;
		margin-top: 20rpx;
		margin-left: auto;
		margin-right: auto;
		margin-bottom: 50rpx;
	}

	.uni-ec-canvas {
		width: 750upx;
		height: 750upx;
		display: block;
	}

	view {
		display: block;
	}

	.search-box {
		width: 100%;
		background-color: rgb(242, 242, 242);
		padding: 15upx 2.5%;
		display: flex;
		justify-content: space-between;
		position: sticky;
		top: 0;
	}

	.search-box .mSearch-input-box {
		width: 100%;
	}

	.search-box .input-box {
		width: 95%;
		flex-shrink: 1;
		display: flex;
		justify-content: center;
		align-items: center;
	}

	.search-box .search-btn {
		width: 15%;
		margin: 0 0 0 2%;
		display: flex;
		justify-content: center;
		align-items: center;
		flex-shrink: 0;
		font-size: 28upx;
		color: #fff;
		background: linear-gradient(to right, #ff9801, #ff570a);
		border-radius: 60upx;
	}

	.search-box .input-box>input {
		width: 100%;
		height: 60upx;
		font-size: 32upx;
		border: 0;
		border-radius: 60upx;
		-webkit-appearance: none;
		-moz-appearance: none;
		appearance: none;
		padding: 0 3%;
		margin: 0;
		background-color: #ffffff;
	}

	.placeholder-class {
		text-align: center;
		color: #9e9e9e;
	}

	.search-keyword {
		width: 100%;
		background-color: rgb(242, 242, 242);
	}
</style>
