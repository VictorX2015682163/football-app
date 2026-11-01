<template>
	<view class="match-container">
		<!-- 本地赛事信息 -->
		<view class="section">
			<view class="section-header">
				<text class="section-title">本地赛事</text>
			</view>
			<view class="match-list">
				<view class="match-item" v-for="(match, index) in localMatches" :key="index" @click="viewMatchDetail(match)">
					<view class="match-header">
						<text class="match-name">{{ match.name }}</text>
						<text class="match-status" :class="match.status">{{ match.statusText }}</text>
					</view>
					<view class="match-info">
						<text class="info-item">📅 {{ match.date }}</text>
						<text class="info-item">📍 {{ match.location }}</text>
						<text class="info-item">👥 {{ match.participants }}人报名</text>
					</view>
					<button class="register-btn" v-if="match.status === 'open'" @click.stop="openRegister(match)">立即报名</button>
				</view>
			</view>
		</view>

		<!-- 在线报名 -->
		<view class="section">
			<view class="section-header">
				<text class="section-title">我的报名</text>
			</view>
			<view class="my-registrations">
				<view class="registration-item" v-for="(reg, index) in myRegistrations" :key="index">
					<view class="reg-info">
						<text class="reg-name">{{ reg.matchName }}</text>
						<text class="reg-status" :class="reg.status">{{ reg.statusText }}</text>
					</view>
					<text class="reg-time">{{ reg.registerTime }}</text>
				</view>
				<view class="empty-state" v-if="myRegistrations.length === 0">
					<text>暂无报名记录</text>
				</view>
			</view>
		</view>

		<!-- 赛程与战报 -->
		<view class="section">
			<view class="section-header">
				<text class="section-title">赛程与战报</text>
			</view>
			<view class="schedule-list">
				<view class="schedule-item" v-for="(schedule, index) in schedules" :key="index">
					<view class="schedule-date">
						<text class="date-day">{{ schedule.day }}</text>
						<text class="date-month">{{ schedule.month }}</text>
					</view>
					<view class="schedule-content">
						<text class="schedule-title">{{ schedule.title }}</text>
						<text class="schedule-result">{{ schedule.result }}</text>
						<text class="schedule-time">{{ schedule.time }}</text>
					</view>
				</view>
			</view>
		</view>

		<!-- 比赛直播入口 -->
		<view class="section">
			<view class="live-section">
				<view class="live-card" @click="goToLive">
					<text class="live-title">🎥 比赛直播</text>
					<text class="live-desc">观看实时比赛，为球员加油助威</text>
					<text class="live-btn">进入直播 →</text>
				</view>
			</view>
		</view>

		<!-- MVP投票入口 -->
		<view class="section">
			<view class="mvp-section">
				<view class="mvp-card" @click="goToMVP">
					<text class="mvp-title">🏆 MVP投票</text>
					<text class="mvp-desc">为你心中的最佳球员投票</text>
					<view class="mvp-stats">
						<text class="stat-item">总票数: {{ mvpStats.total }}</text>
						<text class="stat-item">今日投票: {{ mvpStats.today }}</text>
					</view>
				</view>
			</view>
		</view>

		<!-- 报名弹窗 -->
		<view class="modal" v-if="showRegisterModal" @click="closeRegister">
			<view class="modal-content" @click.stop>
				<view class="modal-header">
					<text class="modal-title">赛事报名</text>
					<text class="modal-close" @click="closeRegister">×</text>
				</view>
				<view class="modal-body">
					<view class="form-item">
						<text class="form-label">姓名</text>
						<input class="form-input" v-model="registerForm.name" placeholder="请输入姓名" />
					</view>
					<view class="form-item">
						<text class="form-label">手机号</text>
						<input class="form-input" v-model="registerForm.phone" placeholder="请输入手机号" type="number" />
					</view>
					<view class="form-item">
						<text class="form-label">位置</text>
						<picker mode="selector" :range="positions" @change="selectPosition">
							<view class="form-picker">{{ registerForm.position || '请选择位置' }}</view>
						</picker>
					</view>
				</view>
				<button class="modal-submit" @click="submitRegister">提交报名</button>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				localMatches: [
					{ name: '春季联赛', date: '2024-03-15', location: '市体育场', participants: 24, status: 'open', statusText: '报名中' },
					{ name: '友谊赛', date: '2024-03-20', location: '训练基地', participants: 16, status: 'open', statusText: '报名中' },
					{ name: '杯赛', date: '2024-03-25', location: '中心球场', participants: 32, status: 'closed', statusText: '已截止' }
				],
				myRegistrations: [
					{ matchName: '春季联赛', status: 'confirmed', statusText: '已确认', registerTime: '2024-03-10 10:30' }
				],
				schedules: [
					{ day: '15', month: '3月', title: '春季联赛', result: '2:1', time: '15:00' },
					{ day: '20', month: '3月', title: '友谊赛', result: '待定', time: '14:00' }
				],
				mvpStats: {
					total: 1250,
					today: 89
				},
				showRegisterModal: false,
				currentMatch: null,
				registerForm: {
					name: '',
					phone: '',
					position: ''
				},
				positions: ['前锋', '中场', '后卫', '守门员']
			}
		},
		methods: {
			viewMatchDetail(match) {
				uni.showModal({
					title: match.name,
					content: `日期: ${match.date}\n地点: ${match.location}\n报名人数: ${match.participants}人`,
					showCancel: false
				})
			},
			openRegister(match) {
				this.currentMatch = match
				this.showRegisterModal = true
			},
			closeRegister() {
				this.showRegisterModal = false
				this.registerForm = { name: '', phone: '', position: '' }
			},
			selectPosition(e) {
				this.registerForm.position = this.positions[e.detail.value]
			},
			submitRegister() {
				if (!this.registerForm.name || !this.registerForm.phone || !this.registerForm.position) {
					uni.showToast({
						title: '请填写完整信息',
						icon: 'none'
					})
					return
				}
				uni.showToast({
					title: '报名成功！',
					icon: 'success'
				})
				this.myRegistrations.push({
					matchName: this.currentMatch.name,
					status: 'pending',
					statusText: '待确认',
					registerTime: new Date().toLocaleString()
				})
				this.closeRegister()
			},
			goToLive() {
				uni.navigateTo({
					url: '/pages/match-experience/index'
				})
			},
			goToMVP() {
				uni.showModal({
					title: 'MVP投票',
					content: '为你心中的最佳球员投票！',
					confirmText: '去投票',
					success: (res) => {
						if (res.confirm) {
							uni.navigateTo({
								url: '/pages/match-experience/index'
							})
						}
					}
				})
			}
		}
	}
</script>

<style scoped>
	.match-container {
		min-height: 100vh;
		background-color: #F5F5F5;
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
		margin-bottom: 20rpx;
	}

	.section-title {
		font-size: 32rpx;
		font-weight: 600;
		color: #333;
	}

	.match-list {
		display: flex;
		flex-direction: column;
		gap: 20rpx;
	}

	.match-item {
		padding: 25rpx;
		background-color: #F8F8F8;
		border-radius: 15rpx;
		transition: transform 0.2s;
	}

	.match-item:active {
		transform: scale(0.98);
	}

	.match-header {
		display: flex;
		justify-content: space-between;
		align-items: center;
		margin-bottom: 15rpx;
	}

	.match-name {
		font-size: 30rpx;
		font-weight: 600;
		color: #333;
	}

	.match-status {
		font-size: 24rpx;
		padding: 5rpx 15rpx;
		border-radius: 20rpx;
		background-color: #E8F5E9;
		color: #2E8B57;
	}

	.match-status.closed {
		background-color: #F0F0F0;
		color: #999;
	}

	.match-info {
		display: flex;
		flex-direction: column;
		gap: 8rpx;
		margin-bottom: 15rpx;
	}

	.info-item {
		font-size: 24rpx;
		color: #666;
	}

	.register-btn {
		background-color: #3CB371;
		color: #FFFFFF;
		font-size: 26rpx;
		padding: 12rpx 30rpx;
		border-radius: 25rpx;
		border: none;
		width: 100%;
	}

	.my-registrations {
		display: flex;
		flex-direction: column;
		gap: 15rpx;
	}

	.registration-item {
		display: flex;
		justify-content: space-between;
		align-items: center;
		padding: 20rpx;
		background-color: #F8F8F8;
		border-radius: 15rpx;
	}

	.reg-info {
		display: flex;
		flex-direction: column;
		gap: 5rpx;
	}

	.reg-name {
		font-size: 28rpx;
		color: #333;
	}

	.reg-status {
		font-size: 24rpx;
		color: #3CB371;
	}

	.reg-time {
		font-size: 24rpx;
		color: #999;
	}

	.empty-state {
		text-align: center;
		padding: 60rpx 0;
		color: #999;
		font-size: 26rpx;
	}

	.schedule-list {
		display: flex;
		flex-direction: column;
		gap: 20rpx;
	}

	.schedule-item {
		display: flex;
		align-items: center;
		padding: 20rpx;
		background-color: #F8F8F8;
		border-radius: 15rpx;
	}

	.schedule-date {
		width: 100rpx;
		text-align: center;
		margin-right: 20rpx;
	}

	.date-day {
		display: block;
		font-size: 36rpx;
		font-weight: 600;
		color: #3CB371;
	}

	.date-month {
		display: block;
		font-size: 24rpx;
		color: #666;
	}

	.schedule-content {
		flex: 1;
		display: flex;
		flex-direction: column;
		gap: 5rpx;
	}

	.schedule-title {
		font-size: 28rpx;
		color: #333;
	}

	.schedule-result {
		font-size: 26rpx;
		color: #3CB371;
		font-weight: 600;
	}

	.schedule-time {
		font-size: 24rpx;
		color: #999;
	}

	.live-section, .mvp-section {
		margin-top: 20rpx;
	}

	.live-card, .mvp-card {
		background: linear-gradient(135deg, #3CB371 0%, #2E8B57 100%);
		border-radius: 20rpx;
		padding: 40rpx;
		color: #FFFFFF;
		display: flex;
		flex-direction: column;
		align-items: center;
	}

	.live-title, .mvp-title {
		font-size: 36rpx;
		font-weight: 600;
		margin-bottom: 15rpx;
	}

	.live-desc, .mvp-desc {
		font-size: 26rpx;
		margin-bottom: 20rpx;
		opacity: 0.9;
		text-align: center;
	}

	.live-btn {
		font-size: 28rpx;
		padding: 15rpx 40rpx;
		background-color: rgba(255, 255, 255, 0.2);
		border-radius: 30rpx;
	}

	.mvp-stats {
		display: flex;
		gap: 30rpx;
		margin-top: 10rpx;
	}

	.stat-item {
		font-size: 24rpx;
		opacity: 0.9;
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

	.modal-content {
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

	.form-item {
		margin-bottom: 30rpx;
	}

	.form-label {
		display: block;
		font-size: 26rpx;
		color: #333;
		margin-bottom: 10rpx;
	}

	.form-input, .form-picker {
		width: 100%;
		padding: 20rpx;
		background-color: #F8F8F8;
		border-radius: 10rpx;
		font-size: 28rpx;
		color: #333;
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

