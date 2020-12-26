<template>
	<!--pages/addFunction/addFunction.wxml-->
	<view class="container">
		<view class="list">
			<view class="list-item" @tap="testFunction"><text>测试云函数</text></view>
			<view class="list-item"><text class="request-text">期望输出：{"sum":3}</text></view>
			<view class="list-item" v-if="result">
				<text class="request-text">调用结果：{{ result }}</text>
			</view>
		</view>
	</view>
</template>
<script>
export default {
	data() {
		return {
			result: '',
		};
	},
	onLoad: function(options) {
		console.log("addfunct");
	},
	methods: {
		
		testFunction() {
			wx.cloud.callFunction({
				name: 'login',
				data: {
					a: 333,
					b: 2
				},
				success: res => {
					console.log(res);
					wx.showToast({
						title: '调用成功'
					});
					this.result = res.result.openid
					// this.result= JSON.stringify(res.result)
		
				},
				fail: err => {
					wx.showToast({
						icon: 'none',
						title: '调用失败'
					});
					console.error('[云函数] [sum] 调用失败：', err);
				}
			});
		}
	}
};
</script>

<style>
@import '../../../style/guide.wxss';
</style>