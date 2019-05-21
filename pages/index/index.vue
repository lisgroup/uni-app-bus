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
						<t-td>{{ item.bus }}</t-td>
						<t-td>{{ item.FromTo }}</t-td>
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
			}
		}
	}
</script>

<style>
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
</style>
