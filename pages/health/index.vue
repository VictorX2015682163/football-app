<template>
	<view class="health-container">
		<!-- 青少年运动营养指南 -->
		<view class="section">
			<view class="section-header">
				<text class="section-title">🥗 运动营养指南</text>
			</view>
			<view class="nutrition-list">
				<view class="nutrition-card" v-for="(item, index) in nutritionGuides" :key="index" @click="viewDetail(item)">
					<view class="card-image">
						<text class="card-emoji">{{ item.emoji }}</text>
					</view>
					<view class="card-content">
						<text class="card-title">{{ item.title }}</text>
						<text class="card-desc">{{ item.desc }}</text>
						<view class="card-footer">
							<text class="card-tag">{{ item.tag }}</text>
							<text class="card-favorite" @click.stop="toggleFavorite(index)">{{ item.favorite ? '❤️' : '🤍' }}</text>
						</view>
					</view>
				</view>
			</view>
		</view>

		<!-- 休息与睡眠提醒 -->
		<view class="section">
			<view class="section-header">
				<text class="section-title">😴 休息与睡眠</text>
			</view>
			<view class="sleep-card">
				<view class="sleep-header">
					<text class="sleep-title">今日睡眠建议</text>
					<text class="sleep-time">建议: 8-10小时</text>
				</view>
				<view class="sleep-progress">
					<view class="sleep-bar">
						<view class="sleep-fill" :style="{ width: sleepProgress + '%' }"></view>
					</view>
					<text class="sleep-text">已记录: {{ sleepHours }}小时</text>
				</view>
				<view class="sleep-tips">
					<text class="tip-item" v-for="(tip, index) in sleepTips" :key="index">• {{ tip }}</text>
				</view>
			</view>
		</view>

		<!-- 运动损伤预防与康复 -->
		<view class="section">
			<view class="section-header">
				<text class="section-title">🏥 损伤预防与康复</text>
			</view>
			<view class="injury-list">
				<view class="injury-card" v-for="(item, index) in injuryPrevention" :key="index" @click="viewDetail(item)">
					<view class="injury-icon">{{ item.icon }}</view>
					<view class="injury-content">
						<text class="injury-title">{{ item.title }}</text>
						<text class="injury-desc">{{ item.desc }}</text>
					</view>
					<text class="injury-arrow">→</text>
				</view>
			</view>
		</view>

		<!-- 健康监测 -->
		<view class="section">
			<view class="section-header">
				<text class="section-title">📊 健康监测</text>
				<text class="section-link" @click="connectDevice">连接设备</text>
			</view>
			<view class="monitor-grid">
				<view class="monitor-item" v-for="(item, index) in healthMonitor" :key="index">
					<text class="monitor-value">{{ item.value }}</text>
					<text class="monitor-label">{{ item.label }}</text>
					<text class="monitor-unit">{{ item.unit }}</text>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				nutritionGuides: [
					{ emoji: '🍎', title: '训练前饮食', desc: '训练前1-2小时补充碳水化合物', tag: '营养', favorite: false },
					{ emoji: '🥛', title: '训练后恢复', desc: '及时补充蛋白质和水分', tag: '恢复', favorite: true },
					{ emoji: '🥗', title: '日常营养', desc: '均衡饮食，多吃蔬菜水果', tag: '健康', favorite: false }
				],
				sleepHours: 7.5,
				sleepProgress: 75,
				sleepTips: [
					'保持规律的睡眠时间',
					'睡前1小时避免使用电子设备',
					'创造舒适的睡眠环境',
					'适量运动有助于提高睡眠质量'
				],
				injuryPrevention: [
					{ icon: '🦵', title: '热身运动', desc: '充分热身，预防运动损伤' },
					{ icon: '💪', title: '拉伸放松', desc: '训练后进行拉伸，促进恢复' },
					{ icon: '🩹', title: '常见损伤', desc: '了解常见损伤及处理方法' },
					{ icon: '🏥', title: '康复训练', desc: '受伤后的康复训练计划' }
				],
				healthMonitor: [
					{ value: '72', label: '心率', unit: 'bpm' },
					{ value: '8500', label: '步数', unit: '步' },
					{ value: '65', label: '体重', unit: 'kg' },
					{ value: '8.2', label: '睡眠', unit: '小时' }
				]
			}
		},
		methods: {
			viewDetail(item) {
				uni.showModal({
					title: item.title,
					content: item.desc,
					showCancel: false
				})
			},
			toggleFavorite(index) {
				this.nutritionGuides[index].favorite = !this.nutritionGuides[index].favorite
				uni.showToast({
					title: this.nutritionGuides[index].favorite ? '已收藏' : '已取消',
					icon: 'success'
				})
			},
			connectDevice() {
				uni.showModal({
					title: '连接设备',
					content: '支持连接智能手环、运动手表等设备，实时监测健康数据',
					confirmText: '去连接',
					success: (res) => {
						if (res.confirm) {
							uni.showToast({
								title: '功能开发中',
								icon: 'none'
							})
						}
					}
				})
			}
		}
	}
</script>

<style scoped>
	.health-container {
		min-height: 100vh;
		background-color: #F0F8F5;
		padding-bottom: 40rpx;
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
		color: #2E8B57;
	}

	.section-link {
		font-size: 26rpx;
		color: #3CB371;
	}

	.nutrition-list {
		display: flex;
		flex-direction: column;
		gap: 20rpx;
	}

	.nutrition-card {
		display: flex;
		align-items: center;
		padding: 25rpx;
		background-color: #F8F8F8;
		border-radius: 15rpx;
		transition: transform 0.2s;
	}

	.nutrition-card:active {
		transform: scale(0.98);
	}

	.card-image {
		width: 100rpx;
		height: 100rpx;
		background: linear-gradient(135deg, #E8F5E9 0%, #C8E6C9 100%);
		border-radius: 15rpx;
		display: flex;
		align-items: center;
		justify-content: center;
		margin-right: 20rpx;
	}

	.card-emoji {
		font-size: 50rpx;
	}

	.card-content {
		flex: 1;
		display: flex;
		flex-direction: column;
		gap: 8rpx;
	}

	.card-title {
		font-size: 28rpx;
		font-weight: 600;
		color: #333;
	}

	.card-desc {
		font-size: 24rpx;
		color: #666;
		line-height: 1.4;
	}

	.card-footer {
		display: flex;
		justify-content: space-between;
		align-items: center;
		margin-top: 5rpx;
	}

	.card-tag {
		font-size: 22rpx;
		padding: 4rpx 12rpx;
		background-color: #E8F5E9;
		color: #2E8B57;
		border-radius: 12rpx;
	}

	.card-favorite {
		font-size: 32rpx;
	}

	.sleep-card {
		padding: 30rpx;
		background: linear-gradient(135deg, #E8F5E9 0%, #C8E6C9 100%);
		border-radius: 15rpx;
	}

	.sleep-header {
		display: flex;
		justify-content: space-between;
		align-items: center;
		margin-bottom: 20rpx;
	}

	.sleep-title {
		font-size: 30rpx;
		font-weight: 600;
		color: #2E8B57;
	}

	.sleep-time {
		font-size: 24rpx;
		color: #666;
	}

	.sleep-progress {
		margin-bottom: 25rpx;
	}

	.sleep-bar {
		width: 100%;
		height: 12rpx;
		background-color: rgba(255, 255, 255, 0.5);
		border-radius: 6rpx;
		overflow: hidden;
		margin-bottom: 10rpx;
	}

	.sleep-fill {
		height: 100%;
		background-color: #3CB371;
		border-radius: 6rpx;
		transition: width 0.3s;
	}

	.sleep-text {
		font-size: 24rpx;
		color: #666;
	}

	.sleep-tips {
		display: flex;
		flex-direction: column;
		gap: 10rpx;
	}

	.tip-item {
		font-size: 24rpx;
		color: #666;
		line-height: 1.6;
	}

	.injury-list {
		display: flex;
		flex-direction: column;
		gap: 15rpx;
	}

	.injury-card {
		display: flex;
		align-items: center;
		padding: 25rpx;
		background-color: #F8F8F8;
		border-radius: 15rpx;
		transition: transform 0.2s;
	}

	.injury-card:active {
		transform: scale(0.98);
	}

	.injury-icon {
		font-size: 50rpx;
		margin-right: 20rpx;
	}

	.injury-content {
		flex: 1;
		display: flex;
		flex-direction: column;
		gap: 8rpx;
	}

	.injury-title {
		font-size: 28rpx;
		font-weight: 600;
		color: #333;
	}

	.injury-desc {
		font-size: 24rpx;
		color: #666;
	}

	.injury-arrow {
		font-size: 32rpx;
		color: #999;
	}

	.monitor-grid {
		display: grid;
		grid-template-columns: repeat(2, 1fr);
		gap: 20rpx;
	}

	.monitor-item {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		padding: 30rpx 20rpx;
		background: linear-gradient(135deg, #E8F5E9 0%, #C8E6C9 100%);
		border-radius: 15rpx;
	}

	.monitor-value {
		font-size: 48rpx;
		font-weight: 600;
		color: #2E8B57;
		margin-bottom: 5rpx;
	}

	.monitor-label {
		font-size: 24rpx;
		color: #666;
		margin-bottom: 5rpx;
	}

	.monitor-unit {
		font-size: 22rpx;
		color: #999;
	}
</style>

