<template>
	<view class="content">
		<page-head :title='title'></page-head>
		<view class="input-group">
			<view class="input-row border">
				<text class="title">线路名称：</text>
				<m-input class="m-input" type="text" clearable focus v-model="line_name" placeholder="请输入线路名称"></m-input>
				<button type="primary" class="primary" @tap="action">搜索</button>
			</view>
		</view>

		<view class="msg">
			<text class="msg-text">{{ to }}</text>
			<button class="mini-btn" type="primary" size="mini" @click="reloadAction">刷新</button>
		</view>
		<view class="warp">
			<view class="box">
				<view class="text-line"></view>
				<t-table @change="change">
					<t-tr>
						<t-th>站台</t-th>
						<t-th>编号</t-th>
						<t-th>车牌</t-th>
						<t-th>进站时间</t-th>
					</t-tr>
					<t-tr v-for="item in tableList" :key="item.id">
						<t-td>
							<text class="td-text">{{ item.stationName }}</text>
						</t-td>
						<t-td>
							<text class="td-text">{{ item.stationCode }}</text>
						</t-td>
						<t-td>
							<text class="td-text">{{ item.carCode }}</text>
						</t-td>
						<t-td>
							<text class="td-text">{{ item.ArrivalTime }}</text>
						</t-td>
					</t-tr>
				</t-table>
			</view>
		</view>
	</view>
</template>

<script>
	const duration = 2000
	import mInput from '../../components/m-input.vue'
	import tTable from '@/components/t-table/t-table.vue'
	import tTh from '@/components/t-table/t-th.vue'
	import tTr from '@/components/t-table/t-tr.vue'
	import tTd from '@/components/t-table/t-td.vue'

	export default {
		components: {
			mInput,
			tTable,
			tTh,
			tTr,
			tTd
		},
		onLoad: function(option) {
			// option为object类型，会序列化上个页面传递的参数
			this.option = option
			this.reloadAction(option)
		},
		data() {
			return {
				title: '苏州实时公交查询',
				line_name: '',
				to: '',
				tableList: [],
				option: {}
			}
		},
		methods: {
			change(e) {
				e.detail
			},
			navigateTo(row) {
				// keep-alive 实现前进后退不刷新
				const query = row.lineID ? 'lineID=' + row.lineID + '&to=' + row.LineInfo : 'href=APTSLine.aspx&' + row.link.replace(
					/APTSLine.aspx\?/, '')
				// console.log(query)
				uni.navigateTo({
					url: '../line/line?' + query
				})
			},
			action() {
				/**
				 * 参数简单校验
				 */
				if (this.line_name.length < 1) {
					uni.showToast({
						icon: 'none',
						title: '请输入线路名称'
					});
					return
				}
				const line_name = this.line_name
				console.log(line_name)
				// 发送请求获取数据
				const url = 'https://www.guke1.com/api/getList'
				this._request(url, {
					linename: line_name
				})
			},
			/**
			 * @param {Object} requestUrl
			 * @param {Object} data
			 */
			_request(requestUrl, data) {
				uni.request({
					url: requestUrl,
					dataType: 'text',
					data: data,
					success: (res) => {
						const obj = JSON.parse(res.data)
						// uni.showToast({
						// 	title: '请求成功',
						// 	icon: 'success',
						// 	mask: true,
						// 	duration: duration
						// });
						// this.res = '请求结果 : ' + JSON.stringify(res)
						this.tableList = obj.data
					},
					fail: (err) => {
						console.log('request fail', err);
						uni.showModal({
							content: err.errMsg,
							showCancel: false
						});
					},
					complete: () => {
						this.loading = false;
					}
				});
			},
			/**
			 * reload 重新加载
			 */
			reloadAction() {
				uni.showLoading({
					title: 'loading'
				})

				const option = this.option
				console.log(option)
				const lineID = option.lineID
				const href = option.href
				if (lineID || href) {
					// 发送 POST 请求
					uni.request({
						url: 'https://www.guke1.com/api/busLine',
						data: option,
						method: 'POST',
						success: (res) => {
							uni.hideLoading()
							if (res.data.code === 200) {
								const data = res.data.data
								this.title = '' + data.to
								this.line_name = this.to = data.to
								this.tableList = data.line
							}

						}
					})
				}
				setTimeout(function() {
					uni.hideLoading()
				}, 3000)
			}
		}
	}
</script>

<style>
	.m-input {
		width: 55%;
		/* height: 100upx; */
		text-align: center;
	}

	.action-row {
		display: flex;
		flex-direction: row;
		justify-content: center;
	}

	.action-row navigator {
		color: #007aff;
		padding: 0 20upx;
	}

	.text-line {
		margin: 80upx 0 0upx 0;
		line-height: 20upx;
	}

	.td-text {
		/* background-color: #E9EEF3; */
		/* align-content: center; */
		/* display: block; */
		text-align: center;
		width: 100%;
		height: 100%;
	}

	.msg {
		margin-top: 20upx;
		/* text-align: center; */
		color: green;
	}

	.msg-text {
		margin-right: 120upx;
		font-size: 48upx;
	}

	.mini-btn {
		position: absolute;
		margin-top: 16upx;
		margin-left: 100upx;
		width: 20%;
		height: 5%;
	}
</style>
