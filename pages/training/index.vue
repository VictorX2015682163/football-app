<template>
	<view class="training-container">
		<!-- 顶部Tabs -->
		<view class="tabs-section">
			<view class="tab-item" :class="{ active: currentTab === 0 }" @click="switchTab(0)">
				<text>训练计划</text>
			</view>
			<view class="tab-item" :class="{ active: currentTab === 1 }" @click="switchTab(1)">
				<text>数据分析</text>
			</view>
			<view class="tab-item" :class="{ active: currentTab === 2 }" @click="switchTab(2)">
				<text>教练社区</text>
			</view>
		</view>

		<!-- 训练日历 -->
		<view class="calendar-section">
			<scroll-view class="calendar-scroll" scroll-x>
				<view class="calendar-item" v-for="(day, index) in calendarDays" :key="index" 
					:class="{ today: day.isToday, selected: day.isSelected }" @click="selectDay(index)">
					<text class="day-name">{{ day.name }}</text>
					<text class="day-date">{{ day.date }}</text>
				</view>
			</scroll-view>
		</view>

		<!-- 训练卡片 -->
		<view class="cards-section">
			<view class="training-card" v-for="(item, index) in trainingCards" :key="index">
				<view class="card-header">
					<text class="card-title">{{ item.title }}</text>
					<text class="card-status" :class="item.status">{{ item.statusText }}</text>
				</view>
				<text class="card-desc">{{ item.desc }}</text>
				<view class="card-footer">
					<text class="card-time">{{ item.time }}</text>
					<button class="check-btn" v-if="item.status === 'pending'" @click="completeTraining(index)">完成打卡</button>
				</view>
			</view>
		</view>

		<!-- 视频教程 -->
		<view class="section">
			<view class="section-header">
				<text class="section-title">视频教程</text>
				<text class="section-more" @click="goToAcademy">更多 ></text>
			</view>
			<view class="video-list">
				<view class="video-item" v-for="(video, index) in videos" :key="index" @click="playVideo(video)">
					<view class="video-cover">
						<text class="play-icon">▶</text>
					</view>
					<view class="video-info">
						<text class="video-title">{{ video.title }}</text>
						<text class="video-desc">{{ video.desc }}</text>
					</view>
				</view>
			</view>
		</view>

		<!-- 挑战任务 -->
		<view class="challenge-section">
			<view class="challenge-card" @click="openChallenge">
				<text class="challenge-title">每日挑战</text>
				<text class="challenge-desc">完成今日训练任务，获得积分奖励</text>
				<text class="challenge-btn">开始挑战 →</text>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				currentTab: 0,
				calendarDays: [
					{ name: '周一', date: '10', isToday: false, isSelected: false },
					{ name: '周二', date: '11', isToday: false, isSelected: false },
					{ name: '周三', date: '12', isToday: true, isSelected: true },
					{ name: '周四', date: '13', isToday: false, isSelected: false },
					{ name: '周五', date: '14', isToday: false, isSelected: false },
					{ name: '周六', date: '15', isToday: false, isSelected: false },
					{ name: '周日', date: '16', isToday: false, isSelected: false }
				],
				trainingCards: [
					{ title: '基础控球训练', desc: '30分钟控球练习，提升球感', time: '18:00-18:30', status: 'pending', statusText: '待完成' },
					{ title: '射门练习', desc: '定点射门训练，提高精准度', time: '19:00-19:30', status: 'completed', statusText: '已完成' },
					{ title: '体能训练', desc: '跑步与力量训练', time: '20:00-20:45', status: 'pending', statusText: '待完成' }
				],
				videos: [
					{ title: '传球技巧', desc: '学习精准传球' },
					{ title: '防守站位', desc: '提升防守能力' }
				]
			}
		},
		methods: {
			switchTab(index) {
				this.currentTab = index
				if (index === 2) {
					uni.navigateTo({
						url: '/pages/community/index'
					})
				}
			},
			selectDay(index) {
				this.calendarDays.forEach((day, i) => {
					day.isSelected = i === index
				})
			},
			completeTraining(index) {
				this.trainingCards[index].status = 'completed'
				this.trainingCards[index].statusText = '已完成'
				uni.showToast({
					title: '打卡成功！',
					icon: 'success'
				})
			},
			goToAcademy() {
				uni.navigateTo({
					url: '/pages/academy/index'
				})
			},
			playVideo(video) {
				uni.navigateTo({
					url: '/pages/academy/index'
				})
			},
			openChallenge() {
				uni.showModal({
					title: '每日挑战',
					content: '完成今日所有训练任务即可获得积分奖励！',
					showCancel: false
				})
			}
		}
	}
</script>

<style scoped>
	.training-container {
		min-height: 100vh;
		background-color: #F5F5F5;
		padding-bottom: 40rpx;
	}

	.tabs-section {
		display: flex;
		background-color: #FFFFFF;
		padding: 20rpx;
		border-bottom: 1rpx solid #E5E5E5;
	}

	.tab-item {
		flex: 1;
		text-align: center;
		padding: 20rpx 0;
		font-size: 28rpx;
		color: #666;
		position: relative;
	}

	.tab-item.active {
		color: #3CB371;
		font-weight: 600;
	}

	.tab-item.active::after {
		content: '';
		position: absolute;
		bottom: 0;
		left: 50%;
		transform: translateX(-50%);
		width: 60rpx;
		height: 4rpx;
		background-color: #3CB371;
		border-radius: 2rpx;
	}

	.calendar-section {
		background-color: #FFFFFF;
		padding: 20rpx 0;
		margin-bottom: 20rpx;
	}

	.calendar-scroll {
		white-space: nowrap;
		padding: 0 20rpx;
	}

	.calendar-item {
		display: inline-block;
		width: 100rpx;
		padding: 20rpx 10rpx;
		margin-right: 15rpx;
		text-align: center;
		border-radius: 15rpx;
		background-color: #F8F8F8;
		vertical-align: top;
	}

	.calendar-item.today {
		background-color: #E8F5E9;
	}

	.calendar-item.selected {
		background-color: #3CB371;
		color: #FFFFFF;
	}

	.day-name {
		display: block;
		font-size: 24rpx;
		margin-bottom: 5rpx;
	}

	.day-date {
		display: block;
		font-size: 32rpx;
		font-weight: 600;
	}

	.cards-section {
		padding: 0 20rpx;
	}

	.training-card {
		background-color: #FFFFFF;
		border-radius: 20rpx;
		padding: 30rpx;
		margin-bottom: 20rpx;
		box-shadow: 0 2rpx 10rpx rgba(0, 0, 0, 0.05);
		transition: transform 0.2s, box-shadow 0.2s;
	}

	.training-card:active {
		transform: translateY(-2rpx);
		box-shadow: 0 4rpx 15rpx rgba(0, 0, 0, 0.1);
	}

	.card-header {
		display: flex;
		justify-content: space-between;
		align-items: center;
		margin-bottom: 15rpx;
	}

	.card-title {
		font-size: 32rpx;
		font-weight: 600;
		color: #333;
	}

	.card-status {
		font-size: 24rpx;
		padding: 5rpx 15rpx;
		border-radius: 20rpx;
		background-color: #F0F0F0;
		color: #666;
	}

	.card-status.completed {
		background-color: #E8F5E9;
		color: #2E8B57;
	}

	.card-desc {
		display: block;
		font-size: 26rpx;
		color: #666;
		margin-bottom: 20rpx;
		line-height: 1.5;
	}

	.card-footer {
		display: flex;
		justify-content: space-between;
		align-items: center;
	}

	.card-time {
		font-size: 24rpx;
		color: #999;
	}

	.check-btn {
		background-color: #3CB371;
		color: #FFFFFF;
		font-size: 24rpx;
		padding: 10rpx 25rpx;
		border-radius: 25rpx;
		border: none;
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

	.video-list {
		display: flex;
		flex-direction: column;
		gap: 20rpx;
	}

	.video-item {
		display: flex;
		align-items: center;
		padding: 15rpx;
		background-color: #F8F8F8;
		border-radius: 15rpx;
	}

	.video-cover {
		width: 120rpx;
		height: 80rpx;
		background: linear-gradient(135deg, #3CB371 0%, #2E8B57 100%);
		border-radius: 10rpx;
		display: flex;
		align-items: center;
		justify-content: center;
		margin-right: 20rpx;
	}

	.play-icon {
		font-size: 30rpx;
		color: #FFFFFF;
	}

	.video-info {
		flex: 1;
		display: flex;
		flex-direction: column;
	}

	.video-title {
		font-size: 28rpx;
		color: #333;
		margin-bottom: 5rpx;
	}

	.video-desc {
		font-size: 24rpx;
		color: #999;
	}

	.challenge-section {
		padding: 0 20rpx;
	}

	.challenge-card {
		background: linear-gradient(135deg, #3CB371 0%, #2E8B57 100%);
		border-radius: 20rpx;
		padding: 40rpx;
		display: flex;
		flex-direction: column;
		align-items: center;
		color: #FFFFFF;
	}

	.challenge-title {
		font-size: 36rpx;
		font-weight: 600;
		margin-bottom: 15rpx;
	}

	.challenge-desc {
		font-size: 26rpx;
		margin-bottom: 20rpx;
		opacity: 0.9;
		text-align: center;
	}

	.challenge-btn {
		font-size: 28rpx;
		padding: 15rpx 40rpx;
		background-color: rgba(255, 255, 255, 0.2);
		border-radius: 30rpx;
	}
</style>

