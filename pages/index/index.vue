<template>


	<view>
		<view class="search-box">
			<view class="input-box">
				<input type="text" :adjust-position="true" @focus="inputChange" placeholder="添加基金" placeholder-class="placeholder-class"
				 confirm-type="search">
			</view>

		</view>

		<div class="content">
			<image class="logo" src="/static/b.jpg"></image>
		</div>
		<!-- 新建云函数 -->
		<!-- 		<view class="uploader">
			<navigator url="../demo/addFunction/addFunction" open-type="navigate" class="uploader-text"><text>测试SUM</text></navigator>
		</view> -->

		<div class="content" v-if="listMyData.length<=0">
			<button type="default" @click="inputChange">请先添加基金</button>
		</div>
		<div v-else>

			<uni-swipe-action>
				<template v-for="(item,index) in listMyData">


					<uni-swipe-action-item :right-options="options" @click="swipBindClick(item)">
						<view class="content-box">
							<navigator :url="`../fund/fundCalcPage/fundCalcPage?fundCode=`+item.fundCode" open-type="navigate">
								<text class="content-text" @click="onClick(item.fundName)">{{item.fundName }}</text>
							</navigator>
						</view>

					</uni-swipe-action-item>

				</template>

			</uni-swipe-action>
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
				result: '',
				listMyData: [],
				listMyData: [],
				options: [{
					text: '删除',
					style: {
						backgroundColor: '#dd524d'
					}
				}]
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
					this.init(res.result.openid);
				},
				fail: err => {
					console.error('调用失败：', err);
				},
			});
		},
		methods: {
			init(openid) {
				const db = wx.cloud.database() //初始化数据库
				db.collection('fundData').where({
					_openid: openid
				}).get({
					success: (res) => {
						this.listMyData = res.data;
					}
				});
			},
			swipBindClick(e) {
				var that = this;
				console.log(e)
				const db = wx.cloud.database() //初始化数据库
				db.collection('fundData').doc(e._id).remove().then(res => {
					uni.showToast({
						title: "删除成功！",
						duration: 2000
					});
					uni.reLaunch({
						url: 'index'
					});
				})
			},
			onClick(title) {
				uni.setNavigationBarTitle({
					title: title
				});
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
	.content-box {
		flex: 1;
		height: 44px;
		line-height: 44px;
		padding: 0 15px;
		position: relative;
		background-color: #fff;
		border-bottom-color: #f5f5f5;
		border-bottom-width: 1px;
		border-bottom-style: solid;
	}
	.content-text {
		font-size: 15px;
	}
</style>