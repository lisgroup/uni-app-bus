<template>
	<view class="content">
		<page-head title='苏州实时公交查询'></page-head>
		<view class="input-group">
			<view class="input-row border">
				<text class="title">线路名称：</text>
				<m-input class="m-input" type="text" clearable focus v-model="line_name" placeholder="请输入线路名称"></m-input>
				<button type="primary" class="primary" @tap="action">搜索</button>
			</view>
		</view>
		<view class="warp">
			<view class="box">
				<view class="text-line"></view>
				<t-table @change="change">
					<t-tr>
						<t-th>线路</t-th>
						<t-th>方向</t-th>
					</t-tr>
					<t-tr v-for="item in tableList" :key="item.id">
						<t-td>
							<!-- #ifdef H5 -->
							<text class="td-text" @click="navigateTo(item)" :data-item="item">{{ item.bus }}</text>
							<!-- #endif -->
							<!-- #ifndef H5 -->
							<text class="td-text" @tap="jumpTo" :data-item="item">{{ item.bus }}</text>
							<!-- #endif -->
						</t-td>
						<t-td>
							<!-- #ifdef H5 -->
							<text class="td-text" @click="navigateTo(item)">{{ item.FromTo }}</text>
							<!-- #endif -->
							<!-- #ifndef H5 -->
							<text class="td-text" @tap="jumpTo">{{ item.FromTo }}</text>
							<!-- #endif -->
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
		data() {
			return {
				line_name: '',
				tableList: []
			}
		},
		methods: {
			change(e) {
				e.detail
			},
			jumpTo(e) {
				const row = e.currentTarget.dataset.item
				const query = row.lineID ? 'lineID=' + row.lineID + '&to=' + row.LineInfo : 'href=APTSLine.aspx&' + row.link.replace(
					/APTSLine.aspx\?/, '')
				if (query) {
					uni.navigateTo({
						url: '../line/line?' + query
					})
				}
			},
			navigateTo(row) {
				// keep-alive 实现前进后退不刷新
				console.log(row)
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
					})
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
				uni.showLoading({
					title: 'loading'
				})
				uni.request({
					url: requestUrl,
					dataType: 'text',
					data: data,
					success: (res) => {
						uni.hideLoading()
						const obj = JSON.parse(res.data)
						// uni.showToast({
						// 	title: '请求成功',
						// 	icon: 'success',
						// 	mask: true,
						// 	duration: duration
						// })
						// this.res = '请求结果 : ' + JSON.stringify(res)
						this.tableList = obj.data
					},
					fail: (err) => {
						uni.hideLoading()
						console.log('request fail', err)
						uni.showModal({
							content: err.errMsg,
							showCancel: false
						})
					},
					complete: () => {
						this.loading = false
					}
				})
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
</style>
