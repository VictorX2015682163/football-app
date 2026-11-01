<template>
	<view class="academy-container">
		<!-- 搜索栏 -->
		<view class="search-section">
			<input class="search-input" v-model="searchKeyword" placeholder="搜索视频教程..." @input="onSearch" />
			<text class="search-icon">🔍</text>
		</view>

		<!-- 分类标签 -->
		<view class="category-section">
			<scroll-view class="category-scroll" scroll-x>
				<view class="category-item" v-for="(cat, index) in categories" :key="index" 
					:class="{ active: currentCategory === index }" @click="selectCategory(index)">
					<text>{{ cat }}</text>
				</view>
			</scroll-view>
		</view>

		<!-- 视频教学区 -->
		<view class="section">
			<view class="section-header">
				<text class="section-title">视频教学</text>
			</view>
			<view class="video-grid">
				<view class="video-card" v-for="(video, index) in filteredVideos" :key="index" @click="playVideo(video)">
					<view class="video-cover">
						<text class="play-icon">▶</text>
						<text class="video-duration">{{ video.duration }}</text>
					</view>
					<view class="video-info">
						<text class="video-title">{{ video.title }}</text>
						<text class="video-desc">{{ video.desc }}</text>
						<view class="video-meta">
							<text class="video-views">👁 {{ video.views }}</text>
							<text class="video-level">{{ video.level }}</text>
						</view>
					</view>
				</view>
			</view>
		</view>

		<!-- 挑战模式入口 -->
		<view class="challenge-section">
			<view class="challenge-card" @click="openChallenge">
				<view class="challenge-header">
					<text class="challenge-title">🎯 挑战模式</text>
					<text class="challenge-badge">每日任务</text>
				</view>
				<text class="challenge-desc">完成今日训练任务，获得积分奖励</text>
				<view class="challenge-progress">
					<text class="progress-text">今日进度: {{ challengeProgress.current }}/{{ challengeProgress.total }}</text>
					<view class="progress-bar">
						<view class="progress-fill" :style="{ width: (challengeProgress.current / challengeProgress.total * 100) + '%' }"></view>
					</view>
				</view>
				<button class="challenge-btn">开始挑战 →</button>
			</view>
		</view>

		<!-- 个性化训练计划 -->
		<view class="section">
			<view class="section-header">
				<text class="section-title">个性化训练计划</text>
			</view>
			<view class="plan-list">
				<view class="plan-card" v-for="(plan, index) in trainingPlans" :key="index" @click="viewPlan(plan)">
					<view class="plan-header">
						<text class="plan-title">{{ plan.title }}</text>
						<text class="plan-difficulty" :class="plan.difficulty">{{ plan.difficultyText }}</text>
					</view>
					<text class="plan-desc">{{ plan.desc }}</text>
					<view class="plan-footer">
						<text class="plan-duration">⏱ {{ plan.duration }}分钟</text>
						<text class="plan-lessons">{{ plan.lessons }}个课程</text>
					</view>
				</view>
			</view>
		</view>

		<!-- 挑战弹窗 -->
		<view class="modal" v-if="showChallengeModal" @click="closeChallenge">
			<view class="modal-content challenge-modal" @click.stop>
				<view class="modal-header">
					<text class="modal-title">每日挑战</text>
					<text class="modal-close" @click="closeChallenge">×</text>
				</view>
				<view class="modal-body">
					<view class="challenge-tasks">
						<view class="task-item" v-for="(task, index) in challengeTasks" :key="index">
							<view class="task-check" :class="{ completed: task.completed }">
								<text v-if="task.completed">✓</text>
							</view>
							<view class="task-info">
								<text class="task-name">{{ task.name }}</text>
								<text class="task-reward">奖励: {{ task.reward }}积分</text>
							</view>
						</view>
					</view>
				</view>
				<button class="modal-submit" @click="startChallenge">开始挑战</button>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				searchKeyword: '',
				currentCategory: 0,
				categories: ['全部', '传球', '射门', '防守', '控球', '体能'],
				videos: [
					{ title: '基础传球技巧', desc: '学习精准传球的基本要领', duration: '5:30', views: '1.2k', level: '初级', category: 1 },
					{ title: '射门训练', desc: '提高射门精准度和力量', duration: '8:20', views: '2.5k', level: '中级', category: 2 },
					{ title: '防守站位', desc: '掌握正确的防守位置', duration: '6:15', views: '980', level: '初级', category: 3 },
					{ title: '控球练习', desc: '提升球感和控球能力', duration: '7:45', views: '1.8k', level: '中级', category: 4 },
					{ title: '体能训练', desc: '增强耐力和爆发力', duration: '10:00', views: '1.5k', level: '中级', category: 5 },
					{ title: '高级传球', desc: '学习复杂传球技巧', duration: '9:30', views: '890', level: '高级', category: 1 }
				],
				challengeProgress: {
					current: 2,
					total: 5
				},
				trainingPlans: [
					{ title: '初学者计划', desc: '适合刚接触足球的学员', duration: 30, lessons: 8, difficulty: 'easy', difficultyText: '简单' },
					{ title: '进阶训练', desc: '提升技术和战术理解', duration: 45, lessons: 12, difficulty: 'medium', difficultyText: '中等' },
					{ title: '专业提升', desc: '针对高水平球员的训练', duration: 60, lessons: 15, difficulty: 'hard', difficultyText: '困难' }
				],
				showChallengeModal: false,
				challengeTasks: [
					{ name: '完成基础传球训练', reward: 50, completed: true },
					{ name: '观看3个教学视频', reward: 30, completed: true },
					{ name: '完成射门练习', reward: 50, completed: false },
					{ name: '分享训练心得', reward: 20, completed: false },
					{ name: '完成体能训练', reward: 40, completed: false }
				]
			}
		},
		computed: {
			filteredVideos() {
				let result = this.videos
				if (this.currentCategory > 0) {
					result = result.filter(v => v.category === this.currentCategory)
				}
				if (this.searchKeyword) {
					result = result.filter(v => 
						v.title.includes(this.searchKeyword) || 
						v.desc.includes(this.searchKeyword)
					)
				}
				return result
			}
		},
		methods: {
			onSearch() {
				// 搜索逻辑已在 computed 中处理
			},
			selectCategory(index) {
				this.currentCategory = index
			},
			playVideo(video) {
				uni.showModal({
					title: video.title,
					content: `开始播放视频教程\n时长: ${video.duration}`,
					confirmText: '播放',
					success: (res) => {
						if (res.confirm) {
							// 这里可以集成视频播放器
							uni.showToast({
								title: '开始播放',
								icon: 'success'
							})
						}
					}
				})
			},
			openChallenge() {
				this.showChallengeModal = true
			},
			closeChallenge() {
				this.showChallengeModal = false
			},
			startChallenge() {
				uni.showToast({
					title: '挑战开始！',
					icon: 'success'
				})
				this.closeChallenge()
			},
			viewPlan(plan) {
				uni.showModal({
					title: plan.title,
					content: `${plan.desc}\n\n时长: ${plan.duration}分钟\n课程数: ${plan.lessons}个`,
					confirmText: '开始训练',
					success: (res) => {
						if (res.confirm) {
							uni.navigateTo({
								url: '/pages/training/index'
							})
						}
					}
				})
			}
		}
	}
</script>

<style scoped>
	.academy-container {
		min-height: 100vh;
		background-color: #F5F5F5;
		padding-bottom: 40rpx;
	}

	.search-section {
		position: relative;
		padding: 20rpx;
		background-color: #FFFFFF;
		margin-bottom: 20rpx;
	}

	.search-input {
		width: 100%;
		height: 70rpx;
		padding: 0 60rpx 0 30rpx;
		background-color: #F8F8F8;
		border-radius: 35rpx;
		font-size: 28rpx;
		color: #333;
	}

	.search-icon {
		position: absolute;
		right: 40rpx;
		top: 50%;
		transform: translateY(-50%);
		font-size: 32rpx;
	}

	.category-section {
		background-color: #FFFFFF;
		padding: 20rpx 0;
		margin-bottom: 20rpx;
	}

	.category-scroll {
		white-space: nowrap;
		padding: 0 20rpx;
	}

	.category-item {
		display: inline-block;
		padding: 12rpx 30rpx;
		margin-right: 15rpx;
		background-color: #F8F8F8;
		border-radius: 30rpx;
		font-size: 26rpx;
		color: #666;
		transition: all 0.2s;
	}

	.category-item.active {
		background-color: #3CB371;
		color: #FFFFFF;
	}

	.section {
		background-color: #FFFFFF;
		border-radius: 20rpx;
		padding: 30rpx;
		margin: 20rpx;
		box-shadow: 0 2rpx 10rpx rgba(0, 0, 0, 0.05);
	}

	.section-header {
		margin-bottom: 20rpx;
	}

	.section-title {
		font-size: 32rpx;
		font-weight: 600;
		color: #333;
	}

	.video-grid {
		display: grid;
		grid-template-columns: repeat(2, 1fr);
		gap: 20rpx;
	}

	.video-card {
		background-color: #F8F8F8;
		border-radius: 15rpx;
		overflow: hidden;
		transition: transform 0.2s;
	}

	.video-card:active {
		transform: scale(0.98);
	}

	.video-cover {
		position: relative;
		width: 100%;
		height: 200rpx;
		background: linear-gradient(135deg, #3CB371 0%, #2E8B57 100%);
		display: flex;
		align-items: center;
		justify-content: center;
	}

	.play-icon {
		font-size: 50rpx;
		color: #FFFFFF;
		opacity: 0.9;
	}

	.video-duration {
		position: absolute;
		bottom: 10rpx;
		right: 10rpx;
		background-color: rgba(0, 0, 0, 0.6);
		color: #FFFFFF;
		font-size: 20rpx;
		padding: 4rpx 10rpx;
		border-radius: 8rpx;
	}

	.video-info {
		padding: 15rpx;
	}

	.video-title {
		display: block;
		font-size: 26rpx;
		font-weight: 600;
		color: #333;
		margin-bottom: 8rpx;
		overflow: hidden;
		text-overflow: ellipsis;
		white-space: nowrap;
	}

	.video-desc {
		display: block;
		font-size: 22rpx;
		color: #666;
		margin-bottom: 10rpx;
		overflow: hidden;
		text-overflow: ellipsis;
		white-space: nowrap;
	}

	.video-meta {
		display: flex;
		justify-content: space-between;
		align-items: center;
	}

	.video-views {
		font-size: 20rpx;
		color: #999;
	}

	.video-level {
		font-size: 20rpx;
		padding: 4rpx 10rpx;
		background-color: #E8F5E9;
		color: #2E8B57;
		border-radius: 10rpx;
	}

	.challenge-section {
		padding: 0 20rpx;
		margin-bottom: 20rpx;
	}

	.challenge-card {
		background: linear-gradient(135deg, #3CB371 0%, #2E8B57 100%);
		border-radius: 20rpx;
		padding: 40rpx;
		color: #FFFFFF;
	}

	.challenge-header {
		display: flex;
		justify-content: space-between;
		align-items: center;
		margin-bottom: 15rpx;
	}

	.challenge-title {
		font-size: 36rpx;
		font-weight: 600;
	}

	.challenge-badge {
		font-size: 22rpx;
		padding: 6rpx 15rpx;
		background-color: rgba(255, 255, 255, 0.2);
		border-radius: 20rpx;
	}

	.challenge-desc {
		font-size: 26rpx;
		margin-bottom: 25rpx;
		opacity: 0.9;
	}

	.challenge-progress {
		margin-bottom: 25rpx;
	}

	.progress-text {
		display: block;
		font-size: 24rpx;
		margin-bottom: 10rpx;
		opacity: 0.9;
	}

	.progress-bar {
		width: 100%;
		height: 8rpx;
		background-color: rgba(255, 255, 255, 0.3);
		border-radius: 4rpx;
		overflow: hidden;
	}

	.progress-fill {
		height: 100%;
		background-color: #FFFFFF;
		border-radius: 4rpx;
		transition: width 0.3s;
	}

	.challenge-btn {
		width: 100%;
		background-color: rgba(255, 255, 255, 0.2);
		color: #FFFFFF;
		font-size: 28rpx;
		padding: 20rpx;
		border-radius: 30rpx;
		border: none;
	}

	.plan-list {
		display: flex;
		flex-direction: column;
		gap: 20rpx;
	}

	.plan-card {
		padding: 25rpx;
		background-color: #F8F8F8;
		border-radius: 15rpx;
		transition: transform 0.2s;
	}

	.plan-card:active {
		transform: scale(0.98);
	}

	.plan-header {
		display: flex;
		justify-content: space-between;
		align-items: center;
		margin-bottom: 15rpx;
	}

	.plan-title {
		font-size: 30rpx;
		font-weight: 600;
		color: #333;
	}

	.plan-difficulty {
		font-size: 22rpx;
		padding: 6rpx 15rpx;
		border-radius: 20rpx;
		background-color: #E8F5E9;
		color: #2E8B57;
	}

	.plan-difficulty.medium {
		background-color: #FFF3E0;
		color: #F57C00;
	}

	.plan-difficulty.hard {
		background-color: #FFEBEE;
		color: #D32F2F;
	}

	.plan-desc {
		display: block;
		font-size: 24rpx;
		color: #666;
		margin-bottom: 15rpx;
		line-height: 1.5;
	}

	.plan-footer {
		display: flex;
		justify-content: space-between;
		align-items: center;
	}

	.plan-duration, .plan-lessons {
		font-size: 22rpx;
		color: #999;
	}

	.modal {
		position: fixed;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		background-color: rgba(0, 0, 0, 0.5);
		display: flex;
		align-items: center;
		justify-content: center;
		z-index: 1000;
	}

	.challenge-modal {
		width: 80%;
		background-color: #FFFFFF;
		border-radius: 20rpx;
		overflow: hidden;
	}

	.modal-header {
		display: flex;
		justify-content: space-between;
		align-items: center;
		padding: 30rpx;
		border-bottom: 1rpx solid #E5E5E5;
	}

	.modal-title {
		font-size: 32rpx;
		font-weight: 600;
		color: #333;
	}

	.modal-close {
		font-size: 50rpx;
		color: #999;
		line-height: 1;
	}

	.modal-body {
		padding: 30rpx;
	}

	.challenge-tasks {
		display: flex;
		flex-direction: column;
		gap: 20rpx;
	}

	.task-item {
		display: flex;
		align-items: center;
		padding: 20rpx;
		background-color: #F8F8F8;
		border-radius: 15rpx;
	}

	.task-check {
		width: 50rpx;
		height: 50rpx;
		border: 2rpx solid #DDD;
		border-radius: 50%;
		display: flex;
		align-items: center;
		justify-content: center;
		margin-right: 20rpx;
		font-size: 30rpx;
		color: #FFFFFF;
	}

	.task-check.completed {
		background-color: #3CB371;
		border-color: #3CB371;
	}

	.task-info {
		flex: 1;
		display: flex;
		flex-direction: column;
		gap: 5rpx;
	}

	.task-name {
		font-size: 28rpx;
		color: #333;
	}

	.task-reward {
		font-size: 24rpx;
		color: #3CB371;
	}

	.modal-submit {
		width: 100%;
		background-color: #3CB371;
		color: #FFFFFF;
		font-size: 30rpx;
		padding: 25rpx;
		border: none;
	}
</style>

