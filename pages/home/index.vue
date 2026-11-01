<template>
	<view class="home-container">
		<!-- 顶部Banner -->
		<view class="banner-section">
			<swiper class="banner-swiper" :autoplay="true" :interval="3000" :duration="500" circular>
				<swiper-item v-for="(item, index) in banners" :key="index">
					<view class="banner-item">
						<text class="banner-title">{{ item.title }}</text>
						<text class="banner-desc">{{ item.desc }}</text>
					</view>
				</swiper-item>
			</swiper>
		</view>

		<!-- 快捷入口宫格 -->
		<view class="grid-section">
			<view class="grid-item" v-for="(item, index) in gridItems" :key="index" @click="navigateTo(item.path)">
				<view class="grid-icon">{{ item.icon }}</view>
				<text class="grid-text">{{ item.text }}</text>
			</view>
		</view>

		<!-- 热门赛事 -->
		<view class="section">
			<view class="section-header">
				<text class="section-title">热门赛事</text>
				<text class="section-more" @click="goToMatch">更多 ></text>
			</view>
			<scroll-view class="match-scroll" scroll-x>
				<view class="match-card" v-for="(match, index) in hotMatches" :key="index" @click="viewMatch(match)">
					<text class="match-name">{{ match.name }}</text>
					<text class="match-time">{{ match.time }}</text>
					<text class="match-location">{{ match.location }}</text>
				</view>
			</scroll-view>
		</view>

		<!-- 推荐视频 -->
		<view class="section">
			<view class="section-header">
				<text class="section-title">推荐视频</text>
				<text class="section-more" @click="goToAcademy">更多 ></text>
			</view>
			<view class="video-list">
				<view class="video-card" v-for="(video, index) in videos" :key="index" @click="playVideo(video)">
					<view class="video-cover">
						<text class="play-icon">▶</text>
					</view>
					<text class="video-title">{{ video.title }}</text>
					<text class="video-duration">{{ video.duration }}</text>
				</view>
			</view>
		</view>

		<!-- 足球知识卡片 -->
		<view class="section">
			<view class="section-header">
				<text class="section-title">足球知识卡片</text>
			</view>
			<view class="knowledge-card">
				<text class="knowledge-title">{{ knowledgeCard.title }}</text>
				<text class="knowledge-content">{{ knowledgeCard.content }}</text>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				banners: [
					{ title: '今日训练', desc: '基础控球练习 18:00-19:30' },
					{ title: '明日比赛', desc: '友谊赛 vs 青年队 15:00' }
				],
				gridItems: [
					{ icon: '⚽', text: '训练计划', path: '/pages/training/index' },
					{ icon: '🏆', text: '赛事报名', path: '/pages/match/index' },
					{ icon: '🎓', text: '技术学院', path: '/pages/academy/index' },
					{ icon: '💪', text: '健康营养', path: '/pages/health/index' },
					{ icon: '📊', text: '数据档案', path: '/pages/profile/index' },
					{ icon: '💬', text: '社区', path: '/pages/community/index' }
				],
				hotMatches: [
					{ name: '春季联赛', time: '3月15日 15:00', location: '市体育场' },
					{ name: '友谊赛', time: '3月20日 14:00', location: '训练基地' },
					{ name: '杯赛', time: '3月25日 16:00', location: '中心球场' }
				],
				videos: [
					{ title: '基础传球技巧', duration: '5:30' },
					{ title: '射门训练', duration: '8:20' },
					{ title: '防守站位', duration: '6:15' }
				],
				knowledgeCard: {
					title: '足球小知识',
					content: '现代足球起源于19世纪的英国，1863年英格兰足球协会成立，标志着现代足球的诞生。'
				}
			}
		},
		methods: {
			navigateTo(path) {
				// 判断是否为tabBar页面
				const tabBarPages = [
					'/pages/home/index',
					'/pages/training/index',
					'/pages/match/index',
					'/pages/profile/index',
					'/pages/community/index'
				]
				
				if (tabBarPages.includes(path)) {
					// tabBar页面使用switchTab
					uni.switchTab({
						url: path
					})
				} else {
					// 非tabBar页面使用navigateTo
					uni.navigateTo({
						url: path
					})
				}
			},
			playVideo(video) {
				uni.navigateTo({
					url: '/pages/academy/index'
				})
			},
			goToMatch() {
				uni.switchTab({
					url: '/pages/match/index'
				})
			},
			goToAcademy() {
				uni.navigateTo({
					url: '/pages/academy/index'
				})
			},
			viewMatch(match) {
				uni.switchTab({
					url: '/pages/match/index'
				})
			}
		}
	}
</script>

<style scoped>
	.home-container {
		min-height: 100vh;
		background-color: #F5F5F5;
		padding-bottom: 40rpx;
	}

	.banner-section {
		margin: 20rpx;
		border-radius: 20rpx;
		overflow: hidden;
		background: linear-gradient(135deg, #3CB371 0%, #2E8B57 100%);
	}

	.banner-swiper {
		height: 200rpx;
	}

	.banner-item {
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		height: 100%;
		padding: 30rpx;
	}

	.banner-title {
		font-size: 36rpx;
		font-weight: 600;
		color: #FFFFFF;
		margin-bottom: 10rpx;
	}

	.banner-desc {
		font-size: 28rpx;
		color: rgba(255, 255, 255, 0.9);
	}

	.grid-section {
		display: grid;
		grid-template-columns: repeat(3, 1fr);
		gap: 20rpx;
		padding: 20rpx;
		background-color: #FFFFFF;
		margin: 20rpx;
		border-radius: 20rpx;
		box-shadow: 0 2rpx 10rpx rgba(0, 0, 0, 0.05);
	}

	.grid-item {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		padding: 30rpx 20rpx;
		border-radius: 15rpx;
		transition: transform 0.2s;
	}

	.grid-item:active {
		transform: scale(0.95);
		background-color: #F5F5F5;
	}

	.grid-icon {
		font-size: 60rpx;
		margin-bottom: 10rpx;
	}

	.grid-text {
		font-size: 26rpx;
		color: #333;
	}

	.section {
		margin: 20rpx;
		background-color: #FFFFFF;
		border-radius: 20rpx;
		padding: 30rpx;
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
		padding: 10rpx;
		transition: opacity 0.2s;
	}

	.section-more:active {
		opacity: 0.6;
	}

	.match-scroll {
		white-space: nowrap;
	}

	.match-card {
		display: inline-block;
		width: 300rpx;
		padding: 20rpx;
		margin-right: 20rpx;
		background-color: #F8F8F8;
		border-radius: 15rpx;
		vertical-align: top;
		transition: transform 0.2s, background-color 0.2s;
	}

	.match-card:active {
		transform: scale(0.98);
		background-color: #E8F5E9;
	}

	.match-name {
		display: block;
		font-size: 28rpx;
		font-weight: 600;
		color: #333;
		margin-bottom: 10rpx;
	}

	.match-time {
		display: block;
		font-size: 24rpx;
		color: #666;
		margin-bottom: 5rpx;
	}

	.match-location {
		display: block;
		font-size: 24rpx;
		color: #999;
	}

	.video-list {
		display: flex;
		flex-direction: column;
		gap: 20rpx;
	}

	.video-card {
		display: flex;
		align-items: center;
		padding: 15rpx;
		background-color: #F8F8F8;
		border-radius: 15rpx;
		transition: transform 0.2s, background-color 0.2s;
	}

	.video-card:active {
		transform: scale(0.98);
		background-color: #E8F5E9;
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
		position: relative;
	}

	.play-icon {
		font-size: 30rpx;
		color: #FFFFFF;
	}

	.video-title {
		flex: 1;
		font-size: 28rpx;
		color: #333;
		margin-right: 10rpx;
	}

	.video-duration {
		font-size: 24rpx;
		color: #999;
	}

	.knowledge-card {
		padding: 30rpx;
		background: linear-gradient(135deg, #F0F8F5 0%, #E8F5E9 100%);
		border-radius: 15rpx;
	}

	.knowledge-title {
		display: block;
		font-size: 30rpx;
		font-weight: 600;
		color: #2E8B57;
		margin-bottom: 15rpx;
	}

	.knowledge-content {
		display: block;
		font-size: 26rpx;
		color: #666;
		line-height: 1.6;
	}
</style>

