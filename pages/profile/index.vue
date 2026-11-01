<template>
	<view class="profile-container">
		<!-- 个人头像和信息卡 -->
		<view class="profile-header">
			<view class="avatar-section" @click="goToSettings">
				<image class="avatar" :src="userInfo.avatar" mode="aspectFill"></image>
				<text class="avatar-edit">✎</text>
			</view>
			<view class="user-info">
				<text class="user-name">{{ userInfo.name }}</text>
				<text class="user-desc">{{ userInfo.desc }}</text>
				<view class="user-stats">
					<view class="stat-item">
						<text class="stat-value">{{ userInfo.stats.training }}</text>
						<text class="stat-label">训练次数</text>
					</view>
					<view class="stat-item">
						<text class="stat-value">{{ userInfo.stats.matches }}</text>
						<text class="stat-label">比赛场次</text>
					</view>
					<view class="stat-item">
						<text class="stat-value">{{ userInfo.stats.score }}</text>
						<text class="stat-label">总积分</text>
					</view>
				</view>
			</view>
		</view>

		<!-- 成长记录时间轴 -->
		<view class="section">
			<view class="section-header">
				<text class="section-title">成长记录</text>
				<text class="section-more" @click="viewAllRecords">查看全部 ></text>
			</view>
			<view class="timeline">
				<view class="timeline-item" v-for="(record, index) in growthRecords" :key="index" @click="viewRecord(record)">
					<view class="timeline-dot"></view>
					<view class="timeline-content">
						<text class="record-title">{{ record.title }}</text>
						<text class="record-date">{{ record.date }}</text>
						<text class="record-desc">{{ record.desc }}</text>
					</view>
				</view>
			</view>
		</view>

		<!-- 数据统计 -->
		<view class="section">
			<view class="section-header">
				<text class="section-title">数据统计</text>
			</view>
			<view class="chart-section">
				<view class="chart-tabs">
					<view class="chart-tab" :class="{ active: currentChart === 0 }" @click="switchChart(0)">
						<text>训练时长</text>
					</view>
					<view class="chart-tab" :class="{ active: currentChart === 1 }" @click="switchChart(1)">
						<text>比赛数据</text>
					</view>
				</view>
				<view class="chart-container">
					<view class="chart-placeholder">
						<text class="chart-title">{{ chartData.title }}</text>
						<view class="chart-bars">
							<view class="chart-bar" v-for="(item, index) in chartData.data" :key="index">
								<view class="bar-fill" :style="{ height: (item.value / chartData.max * 100) + '%' }"></view>
								<text class="bar-label">{{ item.label }}</text>
								<text class="bar-value">{{ item.value }}</text>
							</view>
						</view>
					</view>
				</view>
			</view>
		</view>

		<!-- 数字档案 -->
		<view class="section">
			<view class="section-header">
				<text class="section-title">数字档案</text>
			</view>
			<view class="archive-list">
				<view class="archive-item" v-for="(item, index) in archiveItems" :key="index" @click="handleArchive(item)">
					<text class="archive-icon">{{ item.icon }}</text>
					<text class="archive-text">{{ item.text }}</text>
					<text class="archive-arrow">→</text>
				</view>
			</view>
		</view>

		<!-- 设置入口 -->
		<view class="section">
			<view class="settings-section">
				<view class="setting-item" @click="goToSettings">
					<text class="setting-icon">⚙️</text>
					<text class="setting-text">设置</text>
					<text class="setting-arrow">→</text>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				userInfo: {
					name: '张三',
					desc: '足球爱好者 | 前锋',
					avatar: '/static/logo.png',
					stats: {
						training: 128,
						matches: 45,
						score: 2560
					}
				},
				growthRecords: [
					{ title: '完成高级训练计划', date: '2024-03-10', desc: '成功完成30天进阶训练' },
					{ title: '参加春季联赛', date: '2024-03-05', desc: '获得最佳射手称号' },
					{ title: '突破个人记录', date: '2024-02-28', desc: '单场进球数达到3个' }
				],
				currentChart: 0,
				chartData: {
					title: '本周训练时长',
					max: 10,
					data: [
						{ label: '周一', value: 8 },
						{ label: '周二', value: 6 },
						{ label: '周三', value: 9 },
						{ label: '周四', value: 7 },
						{ label: '周五', value: 5 },
						{ label: '周六', value: 10 },
						{ label: '周日', value: 4 }
					]
				},
				trainingChartData: {
					title: '本周训练时长',
					max: 10,
					data: [
						{ label: '周一', value: 8 },
						{ label: '周二', value: 6 },
						{ label: '周三', value: 9 },
						{ label: '周四', value: 7 },
						{ label: '周五', value: 5 },
						{ label: '周六', value: 10 },
						{ label: '周日', value: 4 }
					]
				},
				matchChartData: {
					title: '本月比赛数据',
					max: 5,
					data: [
						{ label: '进球', value: 12 },
						{ label: '助攻', value: 8 },
						{ label: '抢断', value: 15 },
						{ label: '传球', value: 45 }
					]
				},
				archiveItems: [
					{ icon: '📊', text: '导出数据', action: 'export' },
					{ icon: '📤', text: '分享档案', action: 'share' },
					{ icon: '📝', text: '生成报告', action: 'report' }
				]
			}
		},
		methods: {
			goToSettings() {
				uni.showModal({
					title: '设置',
					content: '个人设置功能',
					showCancel: false
				})
			},
			viewAllRecords() {
				uni.showModal({
					title: '成长记录',
					content: '查看全部成长记录',
					showCancel: false
				})
			},
			viewRecord(record) {
				uni.showModal({
					title: record.title,
					content: `${record.desc}\n\n日期: ${record.date}`,
					showCancel: false
				})
			},
			switchChart(index) {
				this.currentChart = index
				if (index === 0) {
					this.chartData = this.trainingChartData
				} else {
					this.chartData = this.matchChartData
				}
			},
			handleArchive(item) {
				if (item.action === 'export') {
					uni.showToast({
						title: '导出中...',
						icon: 'loading'
					})
					setTimeout(() => {
						uni.showToast({
							title: '导出成功',
							icon: 'success'
						})
					}, 1500)
				} else if (item.action === 'share') {
					uni.showToast({
						title: '分享功能',
						icon: 'none'
					})
				} else if (item.action === 'report') {
					uni.showToast({
						title: '生成报告中...',
						icon: 'loading'
					})
					setTimeout(() => {
						uni.showToast({
							title: '报告生成成功',
							icon: 'success'
						})
					}, 1500)
				}
			}
		}
	}
</script>

<style scoped>
	.profile-container {
		min-height: 100vh;
		background-color: #F5F5F5;
		padding-bottom: 40rpx;
	}

	.profile-header {
		background: linear-gradient(135deg, #3CB371 0%, #2E8B57 100%);
		padding: 60rpx 30rpx 40rpx;
		color: #FFFFFF;
	}

	.avatar-section {
		position: relative;
		width: 150rpx;
		height: 150rpx;
		margin: 0 auto 30rpx;
	}

	.avatar {
		width: 150rpx;
		height: 150rpx;
		border-radius: 50%;
		border: 4rpx solid rgba(255, 255, 255, 0.3);
	}

	.avatar-edit {
		position: absolute;
		bottom: 0;
		right: 0;
		width: 50rpx;
		height: 50rpx;
		background-color: #FFFFFF;
		color: #3CB371;
		border-radius: 50%;
		display: flex;
		align-items: center;
		justify-content: center;
		font-size: 28rpx;
		border: 2rpx solid #3CB371;
	}

	.user-info {
		text-align: center;
	}

	.user-name {
		display: block;
		font-size: 40rpx;
		font-weight: 600;
		margin-bottom: 10rpx;
	}

	.user-desc {
		display: block;
		font-size: 26rpx;
		opacity: 0.9;
		margin-bottom: 30rpx;
	}

	.user-stats {
		display: flex;
		justify-content: space-around;
		padding: 30rpx 0;
		border-top: 1rpx solid rgba(255, 255, 255, 0.2);
	}

	.stat-item {
		display: flex;
		flex-direction: column;
		align-items: center;
		gap: 8rpx;
	}

	.stat-value {
		font-size: 36rpx;
		font-weight: 600;
	}

	.stat-label {
		font-size: 24rpx;
		opacity: 0.8;
	}

	.section {
		background-color: #FFFFFF;
		border-radius: 20rpx;
		padding: 30rpx;
		margin: 20rpx;
		box-shadow: 0 2rpx 10rpx rgba(0, 0, 0, 0.05);
	}

	.section-header {
		display: flex;
		justify-content: space-between;
		align-items: center;
		margin-bottom: 20rpx;
	}

	.section-title {
		font-size: 32rpx;
		font-weight: 600;
		color: #333;
	}

	.section-more {
		font-size: 26rpx;
		color: #3CB371;
	}

	.timeline {
		position: relative;
		padding-left: 30rpx;
	}

	.timeline::before {
		content: '';
		position: absolute;
		left: 15rpx;
		top: 0;
		bottom: 0;
		width: 2rpx;
		background-color: #E5E5E5;
	}

	.timeline-item {
		position: relative;
		padding-bottom: 30rpx;
		display: flex;
		align-items: flex-start;
	}

	.timeline-dot {
		position: absolute;
		left: -23rpx;
		top: 5rpx;
		width: 16rpx;
		height: 16rpx;
		background-color: #3CB371;
		border-radius: 50%;
		border: 3rpx solid #FFFFFF;
		box-shadow: 0 0 0 2rpx #3CB371;
	}

	.timeline-content {
		flex: 1;
		display: flex;
		flex-direction: column;
		gap: 8rpx;
	}

	.record-title {
		font-size: 28rpx;
		font-weight: 600;
		color: #333;
	}

	.record-date {
		font-size: 24rpx;
		color: #999;
	}

	.record-desc {
		font-size: 26rpx;
		color: #666;
		line-height: 1.5;
	}

	.chart-section {
		margin-top: 20rpx;
	}

	.chart-tabs {
		display: flex;
		background-color: #F8F8F8;
		border-radius: 15rpx;
		padding: 5rpx;
		margin-bottom: 30rpx;
	}

	.chart-tab {
		flex: 1;
		text-align: center;
		padding: 15rpx 0;
		font-size: 26rpx;
		color: #666;
		border-radius: 10rpx;
		transition: all 0.2s;
	}

	.chart-tab.active {
		background-color: #3CB371;
		color: #FFFFFF;
	}

	.chart-container {
		height: 400rpx;
		display: flex;
		align-items: center;
		justify-content: center;
	}

	.chart-placeholder {
		width: 100%;
		height: 100%;
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}

	.chart-title {
		font-size: 28rpx;
		color: #333;
		margin-bottom: 30rpx;
	}

	.chart-bars {
		display: flex;
		align-items: flex-end;
		justify-content: space-around;
		width: 100%;
		height: 300rpx;
		padding: 0 20rpx;
	}

	.chart-bar {
		flex: 1;
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: flex-end;
		height: 100%;
		margin: 0 5rpx;
	}

	.bar-fill {
		width: 100%;
		background: linear-gradient(180deg, #3CB371 0%, #2E8B57 100%);
		border-radius: 8rpx 8rpx 0 0;
		min-height: 20rpx;
		transition: height 0.3s;
	}

	.bar-label {
		font-size: 20rpx;
		color: #666;
		margin-top: 10rpx;
	}

	.bar-value {
		font-size: 22rpx;
		color: #3CB371;
		font-weight: 600;
		margin-top: 5rpx;
	}

	.archive-list {
		display: flex;
		flex-direction: column;
		gap: 15rpx;
	}

	.archive-item {
		display: flex;
		align-items: center;
		padding: 25rpx;
		background-color: #F8F8F8;
		border-radius: 15rpx;
		transition: transform 0.2s;
	}

	.archive-item:active {
		transform: scale(0.98);
	}

	.archive-icon {
		font-size: 40rpx;
		margin-right: 20rpx;
	}

	.archive-text {
		flex: 1;
		font-size: 28rpx;
		color: #333;
	}

	.archive-arrow {
		font-size: 32rpx;
		color: #999;
	}

	.settings-section {
		margin-top: 10rpx;
	}

	.setting-item {
		display: flex;
		align-items: center;
		padding: 25rpx;
		background-color: #F8F8F8;
		border-radius: 15rpx;
		transition: transform 0.2s;
	}

	.setting-item:active {
		transform: scale(0.98);
	}

	.setting-icon {
		font-size: 40rpx;
		margin-right: 20rpx;
	}

	.setting-text {
		flex: 1;
		font-size: 28rpx;
		color: #333;
	}

	.setting-arrow {
		font-size: 32rpx;
		color: #999;
	}
</style>

