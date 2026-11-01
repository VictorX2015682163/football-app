<template>
	<view class="experience-container">
		<!-- 实时比分 -->
		<view class="score-section">
			<view class="match-info">
				<text class="match-name">{{ matchInfo.name }}</text>
				<text class="match-time">{{ matchInfo.time }}</text>
			</view>
			<view class="score-board">
				<view class="team-section">
					<text class="team-name">{{ matchInfo.teamA }}</text>
					<text class="team-score">{{ matchInfo.scoreA }}</text>
				</view>
				<text class="score-divider">:</text>
				<view class="team-section">
					<text class="team-score">{{ matchInfo.scoreB }}</text>
					<text class="team-name">{{ matchInfo.teamB }}</text>
				</view>
			</view>
			<view class="match-status">
				<text class="status-text">{{ matchInfo.status }}</text>
				<text class="status-time">{{ matchInfo.statusTime }}</text>
			</view>
		</view>

		<!-- 直播区域 -->
		<view class="section">
			<view class="section-header">
				<text class="section-title">🎥 比赛直播</text>
			</view>
			<view class="live-area">
				<view class="live-placeholder">
					<text class="live-icon">▶</text>
					<text class="live-text">直播中</text>
					<text class="live-viewers">👁 {{ liveData.viewers }}人观看</text>
				</view>
			</view>
		</view>

		<!-- MVP投票 -->
		<view class="section">
			<view class="section-header">
				<text class="section-title">🏆 MVP投票</text>
			</view>
			<view class="mvp-list">
				<view class="mvp-item" v-for="(player, index) in mvpCandidates" :key="index" @click="voteMVP(index)">
					<image class="player-avatar" :src="player.avatar" mode="aspectFill"></image>
					<view class="player-info">
						<text class="player-name">{{ player.name }}</text>
						<text class="player-position">{{ player.position }}</text>
						<text class="player-stats">{{ player.stats }}</text>
					</view>
					<view class="vote-section">
						<text class="vote-icon" :class="{ voted: player.voted }">{{ player.voted ? '❤️' : '🤍' }}</text>
						<text class="vote-count">{{ player.votes }}</text>
					</view>
				</view>
			</view>
			<view class="mvp-stats">
				<text class="stats-text">总票数: {{ totalVotes }}</text>
				<text class="stats-text">今日投票: {{ todayVotes }}</text>
			</view>
		</view>

		<!-- 比赛录像与回放 -->
		<view class="section">
			<view class="section-header">
				<text class="section-title">📹 比赛回放</text>
			</view>
			<view class="replay-list">
				<view class="replay-item" v-for="(replay, index) in replays" :key="index" @click="playReplay(replay)">
					<view class="replay-cover">
						<text class="play-icon">▶</text>
						<text class="replay-duration">{{ replay.duration }}</text>
					</view>
					<view class="replay-info">
						<text class="replay-title">{{ replay.title }}</text>
						<text class="replay-time">{{ replay.time }}</text>
					</view>
				</view>
			</view>
		</view>

		<!-- 线上助威墙 -->
		<view class="section">
			<view class="section-header">
				<text class="section-title">💬 助威墙</text>
			</view>
			<view class="cheer-wall">
				<scroll-view class="cheer-scroll" scroll-y :scroll-top="scrollTop" :scroll-with-animation="true">
					<view class="cheer-item" v-for="(cheer, index) in cheerMessages" :key="index">
						<text class="cheer-name">{{ cheer.name }}:</text>
						<text class="cheer-text">{{ cheer.text }}</text>
						<text class="cheer-time">{{ cheer.time }}</text>
					</view>
				</scroll-view>
				<view class="cheer-input-area">
					<input class="cheer-input" v-model="cheerText" placeholder="为球员加油助威..." @confirm="sendCheer" />
					<text class="cheer-send" @click="sendCheer">发送</text>
				</view>
			</view>
		</view>

		<!-- 实时数据 -->
		<view class="section">
			<view class="section-header">
				<text class="section-title">📊 实时数据</text>
			</view>
			<view class="stats-grid">
				<view class="stat-item" v-for="(stat, index) in realtimeStats" :key="index">
					<text class="stat-label">{{ stat.label }}</text>
					<text class="stat-value">{{ stat.value }}</text>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				matchInfo: {
					name: '春季联赛',
					time: '2024-03-15 15:00',
					teamA: '青年队',
					teamB: '少年队',
					scoreA: 2,
					scoreB: 1,
					status: '进行中',
					statusTime: '下半场 35分钟'
				},
				liveData: {
					viewers: 1250
				},
				mvpCandidates: [
					{ name: '张三', position: '前锋', stats: '2球1助', avatar: '/static/logo.png', votes: 245, voted: false },
					{ name: '李四', position: '中场', stats: '1球2助', avatar: '/static/logo.png', votes: 189, voted: false },
					{ name: '王五', position: '后卫', stats: '5次抢断', avatar: '/static/logo.png', votes: 156, voted: false }
				],
				replays: [
					{ title: '精彩进球集锦', duration: '5:30', time: '15:20' },
					{ title: '上半场回放', duration: '45:00', time: '15:45' },
					{ title: '关键瞬间', duration: '3:15', time: '16:00' }
				],
				cheerMessages: [
					{ name: '球迷A', text: '加油！', time: '刚刚' },
					{ name: '球迷B', text: '踢得漂亮！', time: '1分钟前' },
					{ name: '球迷C', text: '继续努力！', time: '2分钟前' },
					{ name: '球迷D', text: '太精彩了！', time: '3分钟前' }
				],
				cheerText: '',
				scrollTop: 0,
				realtimeStats: [
					{ label: '控球率', value: '58%' },
					{ label: '射门', value: '12' },
					{ label: '射正', value: '8' },
					{ label: '角球', value: '5' },
					{ label: '犯规', value: '7' },
					{ label: '黄牌', value: '2' }
				]
			}
		},
		computed: {
			totalVotes() {
				return this.mvpCandidates.reduce((sum, player) => sum + player.votes, 0)
			},
			todayVotes() {
				return this.mvpCandidates.filter(p => p.voted).length
			}
		},
		onLoad() {
			// 模拟实时数据更新
			this.startRealtimeUpdate()
		},
		onUnload() {
			if (this.updateTimer) {
				clearInterval(this.updateTimer)
			}
		},
		methods: {
			voteMVP(index) {
				const player = this.mvpCandidates[index]
				if (player.voted) {
					player.voted = false
					player.votes--
					uni.showToast({
						title: '已取消投票',
						icon: 'none'
					})
				} else {
					// 取消其他投票
					this.mvpCandidates.forEach(p => {
						if (p.voted) {
							p.voted = false
							p.votes--
						}
					})
					player.voted = true
					player.votes++
					uni.showToast({
						title: '投票成功！',
						icon: 'success'
					})
				}
			},
			playReplay(replay) {
				uni.showModal({
					title: replay.title,
					content: `开始播放回放\n时长: ${replay.duration}`,
					confirmText: '播放',
					success: (res) => {
						if (res.confirm) {
							uni.showToast({
								title: '开始播放',
								icon: 'success'
							})
						}
					}
				})
			},
			sendCheer() {
				if (!this.cheerText.trim()) {
					return
				}
				this.cheerMessages.unshift({
					name: '我',
					text: this.cheerText,
					time: '刚刚'
				})
				this.cheerText = ''
				this.scrollTop = 0
				uni.showToast({
					title: '发送成功',
					icon: 'success'
				})
			},
			startRealtimeUpdate() {
				// 模拟实时数据更新
				this.updateTimer = setInterval(() => {
					// 更新观看人数
					this.liveData.viewers += Math.floor(Math.random() * 10) - 5
					if (this.liveData.viewers < 1000) {
						this.liveData.viewers = 1000
					}
					
					// 模拟新的助威消息
					if (Math.random() > 0.7) {
						const messages = ['加油！', '踢得漂亮！', '继续努力！', '太精彩了！', '好球！']
						const names = ['球迷A', '球迷B', '球迷C', '球迷D', '球迷E']
						this.cheerMessages.unshift({
							name: names[Math.floor(Math.random() * names.length)],
							text: messages[Math.floor(Math.random() * messages.length)],
							time: '刚刚'
						})
						if (this.cheerMessages.length > 20) {
							this.cheerMessages.pop()
						}
					}
				}, 3000)
			}
		}
	}
</script>

<style scoped>
	.experience-container {
		min-height: 100vh;
		background-color: #F5F5F5;
		padding-bottom: 40rpx;
	}

	.score-section {
		background: linear-gradient(135deg, #3CB371 0%, #2E8B57 100%);
		padding: 40rpx 30rpx;
		color: #FFFFFF;
		margin-bottom: 20rpx;
	}

	.match-info {
		text-align: center;
		margin-bottom: 30rpx;
	}

	.match-name {
		display: block;
		font-size: 32rpx;
		font-weight: 600;
		margin-bottom: 10rpx;
	}

	.match-time {
		display: block;
		font-size: 24rpx;
		opacity: 0.9;
	}

	.score-board {
		display: flex;
		align-items: center;
		justify-content: center;
		margin-bottom: 30rpx;
	}

	.team-section {
		display: flex;
		flex-direction: column;
		align-items: center;
		gap: 15rpx;
	}

	.team-name {
		font-size: 28rpx;
		opacity: 0.9;
	}

	.team-score {
		font-size: 72rpx;
		font-weight: 700;
	}

	.score-divider {
		font-size: 72rpx;
		font-weight: 700;
		margin: 0 40rpx;
	}

	.match-status {
		text-align: center;
		padding-top: 20rpx;
		border-top: 1rpx solid rgba(255, 255, 255, 0.2);
	}

	.status-text {
		display: block;
		font-size: 28rpx;
		margin-bottom: 8rpx;
	}

	.status-time {
		display: block;
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
		margin-bottom: 20rpx;
	}

	.section-title {
		font-size: 32rpx;
		font-weight: 600;
		color: #333;
	}

	.live-area {
		width: 100%;
		height: 400rpx;
		background-color: #000;
		border-radius: 15rpx;
		overflow: hidden;
	}

	.live-placeholder {
		width: 100%;
		height: 100%;
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		color: #FFFFFF;
	}

	.live-icon {
		font-size: 80rpx;
		margin-bottom: 20rpx;
	}

	.live-text {
		font-size: 32rpx;
		margin-bottom: 15rpx;
	}

	.live-viewers {
		font-size: 24rpx;
		opacity: 0.8;
	}

	.mvp-list {
		display: flex;
		flex-direction: column;
		gap: 20rpx;
		margin-bottom: 20rpx;
	}

	.mvp-item {
		display: flex;
		align-items: center;
		padding: 25rpx;
		background-color: #F8F8F8;
		border-radius: 15rpx;
		transition: transform 0.2s;
	}

	.mvp-item:active {
		transform: scale(0.98);
	}

	.player-avatar {
		width: 100rpx;
		height: 100rpx;
		border-radius: 50%;
		margin-right: 20rpx;
	}

	.player-info {
		flex: 1;
		display: flex;
		flex-direction: column;
		gap: 5rpx;
	}

	.player-name {
		font-size: 28rpx;
		font-weight: 600;
		color: #333;
	}

	.player-position {
		font-size: 24rpx;
		color: #666;
	}

	.player-stats {
		font-size: 24rpx;
		color: #3CB371;
	}

	.vote-section {
		display: flex;
		flex-direction: column;
		align-items: center;
		gap: 8rpx;
	}

	.vote-icon {
		font-size: 40rpx;
		transition: transform 0.2s;
	}

	.vote-icon.voted {
		animation: voteAnimation 0.3s;
	}

	@keyframes voteAnimation {
		0% { transform: scale(1); }
		50% { transform: scale(1.3); }
		100% { transform: scale(1); }
	}

	.vote-count {
		font-size: 24rpx;
		color: #666;
	}

	.mvp-stats {
		display: flex;
		justify-content: space-around;
		padding: 20rpx;
		background-color: #F8F8F8;
		border-radius: 15rpx;
	}

	.stats-text {
		font-size: 24rpx;
		color: #666;
	}

	.replay-list {
		display: flex;
		flex-direction: column;
		gap: 20rpx;
	}

	.replay-item {
		display: flex;
		align-items: center;
		padding: 20rpx;
		background-color: #F8F8F8;
		border-radius: 15rpx;
		transition: transform 0.2s;
	}

	.replay-item:active {
		transform: scale(0.98);
	}

	.replay-cover {
		position: relative;
		width: 200rpx;
		height: 120rpx;
		background: linear-gradient(135deg, #3CB371 0%, #2E8B57 100%);
		border-radius: 10rpx;
		display: flex;
		align-items: center;
		justify-content: center;
		margin-right: 20rpx;
	}

	.play-icon {
		font-size: 50rpx;
		color: #FFFFFF;
	}

	.replay-duration {
		position: absolute;
		bottom: 8rpx;
		right: 8rpx;
		background-color: rgba(0, 0, 0, 0.6);
		color: #FFFFFF;
		font-size: 20rpx;
		padding: 4rpx 10rpx;
		border-radius: 8rpx;
	}

	.replay-info {
		flex: 1;
		display: flex;
		flex-direction: column;
		gap: 8rpx;
	}

	.replay-title {
		font-size: 28rpx;
		font-weight: 600;
		color: #333;
	}

	.replay-time {
		font-size: 24rpx;
		color: #999;
	}

	.cheer-wall {
		display: flex;
		flex-direction: column;
	}

	.cheer-scroll {
		height: 400rpx;
		margin-bottom: 20rpx;
	}

	.cheer-item {
		padding: 15rpx 20rpx;
		background-color: #F8F8F8;
		border-radius: 10rpx;
		margin-bottom: 10rpx;
		display: flex;
		align-items: center;
		gap: 10rpx;
	}

	.cheer-name {
		font-size: 24rpx;
		color: #3CB371;
		font-weight: 600;
	}

	.cheer-text {
		flex: 1;
		font-size: 26rpx;
		color: #333;
	}

	.cheer-time {
		font-size: 22rpx;
		color: #999;
	}

	.cheer-input-area {
		display: flex;
		align-items: center;
		gap: 15rpx;
		padding-top: 20rpx;
		border-top: 1rpx solid #E5E5E5;
	}

	.cheer-input {
		flex: 1;
		height: 70rpx;
		padding: 0 20rpx;
		background-color: #F8F8F8;
		border-radius: 35rpx;
		font-size: 28rpx;
		color: #333;
	}

	.cheer-send {
		font-size: 28rpx;
		color: #3CB371;
		font-weight: 600;
		padding: 15rpx 30rpx;
	}

	.stats-grid {
		display: grid;
		grid-template-columns: repeat(3, 1fr);
		gap: 20rpx;
	}

	.stat-item {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		padding: 25rpx 15rpx;
		background: linear-gradient(135deg, #E8F5E9 0%, #C8E6C9 100%);
		border-radius: 15rpx;
	}

	.stat-label {
		font-size: 24rpx;
		color: #666;
		margin-bottom: 10rpx;
	}

	.stat-value {
		font-size: 36rpx;
		font-weight: 600;
		color: #2E8B57;
	}
</style>

