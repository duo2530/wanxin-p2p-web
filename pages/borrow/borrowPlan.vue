<template>
	<view class="content plan">
		<topBar title='还款计划'/>
		<view class="title" v-if="plans.length > 0">
			每期等额还款，共计 {{plans.length}}期，还款日将按期自动扣款
		</view>
		<view class="title" v-else>{{loading ? '加载中...' : '暂无还款计划'}}</view>
		<view class="items" v-for="(it,ind) in plans" :key="it.id || ind">
			<view class="tit">
				<view>第{{it.numberOfPeriods}}期 <text class="pri">{{it.amount}}</text> 元</view>
				<view>{{formatDate(it.shouldRepaymentDate)}}</view>
			</view>
			<view class="des">
				<text>本金{{it.principal}} 元，利息{{it.interest}} 元</text>
				<text :class="it.repaymentStatus == '1' ? 'done' : 'wait'">{{it.repaymentStatusName}}</text>
			</view>
		</view>
	</view>
</template>
<script>
	import topBar from '../../components/TopBar.vue'
	
	export default {
		components: {
			topBar
		},
		data() {
			return {
				projectId: '',
				loading: true,
				plans: []
			}
		},
		onLoad(options) {
			// projectId 可选, 不传则显示当前用户全部还款计划
			this.projectId = (options && options.projectId) || ''
			this.getPlans()
		},
		methods: {
			goPath(url) {
				uni.switchTab(url);
			},
			// yyyy-MM-dd -> yyyy年MM月dd日
			formatDate(d) {
				if (!d) return ''
				const p = String(d).split('-')
				return p.length == 3 ? `${p[0]}年${p[1]}月${p[2]}日` : d
			},
			getPlans() {
				let url = 'repayment/my/repayment-plans'
				if (this.projectId) {
					url += `?projectId=${encodeURIComponent(this.projectId)}`
				}
				this.request({url}).then(res => {
					this.plans = res.data.result || []
					this.loading = false
				}).catch(err => {
					this.loading = false
					uni.showToast({
						icon: 'none',
						title: (err && err.data && err.data.msg) || '加载失败'
					});
				})
			}
		}
	}
</script>

<style scoped lang="scss">
	.plan{
		.title{
			color:$uni-text-color-grey;
			line-height: 100upx;
			font-size: 28upx;
			padding:0 30upx;
		}
		.items{
			box-shadow: 0 0 4upx 0 $uni-border-color;
			padding:30upx;
			font-size: 28upx;
			.tit{
				color:$uni-text-color-greyb;
				display: flex;
				justify-content: space-between;
				.pri{
					font-size: 40upx;
				}
			}
			.des{
				color:$uni-text-color-grey;
				line-height: 60upx;
				display: flex;
				justify-content: space-between;
				.done{
					color: #999;
				}
				.wait{
					color: #f0883a;
				}
			}
		}
	}
</style>

