<template>
	<view>
		<uni-ec-canvas class="uni-ec-canvas" id="line-chart" canvas-id="multi-charts-line" :ec="ec"></uni-ec-canvas>

		<view class="content">
			<text v-if="YGJJ-DWJZ>=0" style="color:#ff0000;">今日预估净值: {{YGJJ}} （{{predValuePrecent}}% ↑）

				<div class="content">
					估算涨幅:{{(YGJJ-DWJZ).toFixed(4)}}
				</div>
			</text>
			<text v-if="YGJJ-DWJZ<0" style="color:#090;">今日预估净值: {{YGJJ}} （ {{predValuePrecent}}% ↓）

				<div class="content">
					估算跌幅:{{(YGJJ-DWJZ).toFixed(4)}}
				</div>
			</text>

			<view class="content">

				<text> 根据统计预估买入金额: {{payMoney}}</text>
			</view>
		</view>

		<view class="content">
			<navigator :url="'../fundPredictPage/fundPredictPage?fundCode='+ encodeURIComponent(fundCode)" open-type="navigate"
			 class="uploader-text"><text style="color: #007AFF;">基金涨幅预览</text></navigator>
		</view>
		<view class="content_data">
			<text class="content">单位净值平均值</text>
			<uni-list>
				<template v-for="(item,index) in listData">
					<uni-list-item :title="item.title" :rightText="item.rightText"></uni-list-item>
				</template>
			</uni-list>
		</view>
		<view class="content_data">
			<text class="content">平均净值收益率</text>

			<uni-list>
				<template v-for="(item,index) in listDataYtn">
					<uni-list-item :title="item.title" :rightText="item.rightText"></uni-list-item>
				</template>
			</uni-list>

		</view>
		<view class="content">

			<text>收益率统计: {{sumV}}</text>
		</view>

	</view>
</template>

<script>
	import uniEcCanvas from "../../../uni-ec-canvas/uni-ec-canvas.vue";
	export default {
		data() {
			return {
				fundCode: '',
				ec: {
					lazyLoad: false,
					option: {},
				},
				listData: [],
				listDataYtn: [],
				sumV: '',
				payMoney: 0,
				YGJJ: 0,
				DWJZ: 0,
				predValuePrecent: 0

			}
		},
		onReady() {},
		components: {
			uniEcCanvas
		},
		onLoad: function(option) {
			this.fundCode = option.fundCode;
			this.getPredict();
			this.init();
		},
		mounted() {},
		methods: {
			init() {
				let sltTimeRange = '3n'
				let urls =
					`https://fundmobapi.eastmoney.com/FundMApi/FundNetDiagram.ashx?FCODE=${
				          this.fundCode
				        }&RANGE=${
				          sltTimeRange
				        }&deviceid=Wap&plat=Wap&product=EFund&version=2.0.0&_=${new Date().getTime()}`;
				uni.request({
					url: urls,
					success: (res) => {

						this.ec.option = this.getOptiont(res.data.Datas);
					}
				});
			},
			getOptiont(dataList) {
				let chazhi = 0;
				let san = this.avg(dataList.slice(dataList.length - 30 - chazhi, dataList.length - chazhi));

				let liu = this.avg(dataList.slice(dataList.length - 60 - chazhi, dataList.length - chazhi));

				let jiu = this.avg(dataList.slice(dataList.length - 90 - chazhi, dataList.length - chazhi));

				let yibaiba = this.avg(dataList.slice(dataList.length - 180 - chazhi, dataList.length - chazhi));

				let sanbailiu = this.avg(dataList.slice(dataList.length - 365 - chazhi, dataList.length - chazhi));

				let sanliujiu = ((parseFloat(san) + parseFloat(liu) + parseFloat(jiu)) / 3).toFixed(4);

				let sanliuqiu180 = ((parseFloat(san) + parseFloat(liu) + parseFloat(jiu) + parseFloat(yibaiba)) / 4).toFixed(4);

				let sanliuqiu180365 = ((parseFloat(san) + parseFloat(liu) + parseFloat(jiu) + parseFloat(yibaiba) + parseFloat(
					sanbailiu)) / 5).toFixed(4);

				let all = ((parseFloat(san) + parseFloat(liu) + parseFloat(jiu) + parseFloat(yibaiba) + parseFloat(sanbailiu) +
					parseFloat(sanliujiu) + parseFloat(sanliuqiu180) + parseFloat(sanliuqiu180365)) / 8).toFixed(4);

				this.listData = [];
				this.listData.push({
					title: "近30天",
					rightText: san
				});
				this.listData.push({
					title: "近60天",
					rightText: liu
				});
				this.listData.push({
					title: "近90天",
					rightText: jiu
				});
				this.listData.push({
					title: "近180天",
					rightText: yibaiba
				});
				this.listData.push({
					title: "近365天",
					rightText: sanbailiu
				});
				this.listData.push({
					title: "近30、60、90天平均",
					rightText: sanliujiu
				});
				this.listData.push({
					title: "近30、60、90、180天平均",
					rightText: sanliuqiu180
				});
				this.listData.push({
					title: "近30、60、90、180、365天平均",
					rightText: sanliuqiu180365
				});
				this.listData.push({
					title: "以上所有时间平均",
					rightText: all
				});



				let value = this.YGJJ;

				let sanYtn = ((parseFloat(value) - parseFloat(san)) / parseFloat(value)) * 100
				let liuYtn = ((parseFloat(value) - parseFloat(liu)) / parseFloat(value)) * 100
				let jiuYtn = ((parseFloat(value) - parseFloat(jiu)) / parseFloat(value)) * 100
				let yibaibaYtn = ((parseFloat(value) - parseFloat(yibaiba)) / parseFloat(value)) * 100
				let sanbailiuYtn = ((parseFloat(value) - parseFloat(sanbailiu)) / parseFloat(value)) * 100
				let sanliujiuYtn = ((parseFloat(value) - parseFloat(sanliujiu)) / parseFloat(value)) * 100
				let sanliuqiu180Ytn = ((parseFloat(value) - parseFloat(sanliuqiu180)) / parseFloat(value)) * 100
				let sanliuqiu180365Ytn = ((parseFloat(value) - parseFloat(sanliuqiu180365)) / parseFloat(value)) * 100
				let allYtn = ((parseFloat(value) - parseFloat(all)) / parseFloat(value)) * 100

				this.listDataYtn = [];
				this.listDataYtn.push({
					title: "近30天",
					rightText: sanYtn.toFixed(2) + '%'
				});
				this.listDataYtn.push({
					title: "近60天",
					rightText: liuYtn.toFixed(2) + '%'
				});
				this.listDataYtn.push({
					title: "近90天",
					rightText: jiuYtn.toFixed(2) + '%'
				});
				this.listDataYtn.push({
					title: "近180天",
					rightText: yibaibaYtn.toFixed(2) + '%'
				});
				this.listDataYtn.push({
					title: "近365天",
					rightText: sanbailiuYtn.toFixed(2) + '%'
				});
				this.listDataYtn.push({
					title: "近30、60、90天平均",
					rightText: sanliujiuYtn.toFixed(2) + '%'
				});
				this.listDataYtn.push({
					title: "近30、60、90、180天平均",
					rightText: sanliuqiu180Ytn.toFixed(2) + '%'
				});
				this.listDataYtn.push({
					title: "近30、60、90、180、365天平均",
					rightText: sanliuqiu180365Ytn.toFixed(2) + '%'
				});
				this.listDataYtn.push({
					title: "以上所有时间平均",
					rightText: allYtn.toFixed(2) + '%'
				});

				let sum = (sanYtn + liuYtn + jiuYtn + yibaibaYtn + sanbailiuYtn + sanliujiuYtn + sanliuqiu180Ytn +
					sanliuqiu180365Ytn + allYtn);
				this.sumV = sum.toFixed(2) + '%';

				this.payMoney = this.getMoney(sum / 100);

				let chartTypeList = {
					DWJZ: {
						name: "单位净值",
					},
					LJJZ: {
						name: "累计净值",
					},
				};
				var option = {
					tooltip: {
						trigger: 'axis'
					},
					dataZoom: [{
						show: true,
						realtime: true,
						type: "inside",
						start: "95",
					}],
					grid: {
						containLabel: true
					},
					legend: {
						show: true,

					},
					xAxis: {
						type: "category",
						data: dataList.map((item) => item.FSRQ),
						axisLabel: {},
					},

					yAxis: {
						type: "value",
						scale: true,
						axisLabel: {
							formatter: (val) => {
								return val.toFixed(2) + "%";
							},
						},
						splitLine: {
							show: true,
							lineStyle: {
								type: "dashed",
							},
						},
						data: [],
					},

					series: [{
							type: "line",
							name: "单位净值",
							data: dataList.map((item) => +item.DWJZ),
							markLine: {
								silent: true,
								data: [{
									yAxis: sanbailiu
								}, {
									yAxis: yibaiba
								}, {
									yAxis: jiu
								}, {
									yAxis: liu
								}, {
									yAxis: san
								}]
							}
						},
						{
							type: "line",
							name: "累计净值",
							data: dataList.map((item) => +item.LJJZ),
						},
					]
				};

				return option;

			},

			avg(dataList) {
				let avg = 0.000;
				for (let index = 0; index < dataList.length; index++) {
					const element = dataList[index];
					avg += parseFloat(element.DWJZ);
				}
				return (avg / dataList.length).toFixed(4);
			},
			getMoney(sumV) {
				let Money = 100;
				if (sumV < -2) {
					return Money * 10;
				} else if (sumV < -1.5) {
					return Money * 8;
				} else if (sumV < -1) {
					return Money * 5;
				} else if (sumV < -0.5) {
					return Money * 3;
				} else if (sumV < 0) {
					return Money * 2;
				} else if (sumV < 0.1) {
					return Money * 1.5;
				} else if (sumV < 0.4) {
					return Money * 1;
				} else if (sumV < 0.6) {
					return Money * 0.5;
				} else if (sumV < 0.8) {
					return Money * 0.2;
				} else if (sumV < 1) {
					return Money * 0.1;
				} else {
					return Money * 0;
				}
			},
			getPredict() {
				let url =
					`https://fundmobapi.eastmoney.com/FundMApi/FundVarietieValuationDetail.ashx?FCODE=${
				        this.fundCode
				      }&deviceid=Wap&plat=Wap&product=EFund&version=2.0.0&_=${new Date().getTime()}`;
				uni.request({
					url: url,
					success: (res) => {
						let dataList = res.data.Datas.map((item) => item.split(","));
						// console.log(dataList)
						let predValue = dataList[dataList.length - 1];
						this.predValuePrecent = parseFloat(predValue[2]).toFixed(2);
						this.DWJZ = res.data.Expansion.DWJZ;
						this.YGJJ = (this.DWJZ * (1 + 0.01 * predValue[2])).toFixed(4)
					}
				});
			},
		},
	}
</script>

<style scoped>
	.uni-ec-canvas {
		width: 750upx;
		height: 750upx;
		display: block;
	}

	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		padding-bottom: 10px;
	}

	.content_data {
		display: flex;
		flex-direction: column;
		padding-bottom: 10px;
	}
</style>
