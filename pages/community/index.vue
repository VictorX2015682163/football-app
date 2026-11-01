<template>
	<view class="community-container">
		<!-- 话题区 -->
		<view class="section">
			<view class="section-header">
				<text class="section-title">热门话题</text>
			</view>
			<scroll-view class="topic-scroll" scroll-x>
				<view class="topic-item" v-for="(topic, index) in topics" :key="index" @click="viewTopic(topic)">
					<text class="topic-tag">{{ topic.tag }}</text>
					<text class="topic-count">{{ topic.count }}条</text>
				</view>
			</scroll-view>
		</view>

		<!-- 附近球友推荐 -->
		<view class="section">
			<view class="section-header">
				<text class="section-title">附近球友</text>
				<text class="section-more">更多 ></text>
			</view>
			<scroll-view class="friends-scroll" scroll-x>
				<view class="friend-card" v-for="(friend, index) in nearbyFriends" :key="index" @click="viewProfile(friend)">
					<image class="friend-avatar" :src="friend.avatar" mode="aspectFill"></image>
					<text class="friend-name">{{ friend.name }}</text>
					<text class="friend-distance">{{ friend.distance }}km</text>
				</view>
			</scroll-view>
		</view>

		<!-- 动态流 -->
		<view class="section">
			<view class="section-header">
				<text class="section-title">我的动态</text>
			</view>
			<view class="post-list">
				<view class="post-card" v-for="(post, index) in posts" :key="index">
					<view class="post-header" @click="viewProfile(post.user)">
						<image class="post-avatar" :src="post.user.avatar" mode="aspectFill"></image>
						<view class="post-user-info">
							<text class="post-username">{{ post.user.name }}</text>
							<text class="post-time">{{ post.time }}</text>
						</view>
					</view>
					<text class="post-content">{{ post.content }}</text>
					<view class="post-images" v-if="post.images && post.images.length > 0">
						<image class="post-image" v-for="(img, imgIndex) in post.images" :key="imgIndex" 
							:src="img" mode="aspectFill" @click="previewImage(post.images, imgIndex)"></image>
					</view>
					<view class="post-footer">
						<view class="post-action" @click="toggleLike(index)">
							<text class="action-icon" :class="{ liked: post.liked }">{{ post.liked ? '❤️' : '🤍' }}</text>
							<text class="action-count">{{ post.likes }}</text>
						</view>
						<view class="post-action" @click="showComment(index)">
							<text class="action-icon">💬</text>
							<text class="action-count">{{ post.comments }}</text>
						</view>
						<view class="post-action" @click="sharePost(index)">
							<text class="action-icon">📤</text>
						</view>
					</view>
					<!-- 评论列表 -->
					<view class="comment-list" v-if="post.showComments && post.commentList">
						<view class="comment-item" v-for="(comment, commentIndex) in post.commentList" :key="commentIndex">
							<text class="comment-name">{{ comment.name }}:</text>
							<text class="comment-text">{{ comment.text }}</text>
						</view>
					</view>
				</view>
			</view>
		</view>

		<!-- 评论输入框 -->
		<view class="comment-input" v-if="showCommentInput">
			<input class="input-field" v-model="commentText" placeholder="写评论..." @confirm="submitComment" />
			<text class="send-btn" @click="submitComment">发送</text>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				topics: [
					{ tag: '#训练心得', count: 128 },
					{ tag: '#赛事讨论', count: 89 },
					{ tag: '#技术分享', count: 156 },
					{ tag: '#装备推荐', count: 67 },
					{ tag: '#球友约球', count: 234 }
				],
				nearbyFriends: [
					{ name: '李四', avatar: '/static/logo.png', distance: 1.2 },
					{ name: '王五', avatar: '/static/logo.png', distance: 2.5 },
					{ name: '赵六', avatar: '/static/logo.png', distance: 3.8 },
					{ name: '孙七', avatar: '/static/logo.png', distance: 5.1 }
				],
				posts: [
					{
						user: { name: '张三', avatar: '/static/logo.png' },
						time: '2小时前',
						content: '今天训练状态不错，完成了所有训练计划！继续加油💪',
						images: [],
						likes: 24,
						comments: 5,
						liked: false,
						showComments: false,
						commentList: [
							{ name: '李四', text: '加油！' },
							{ name: '王五', text: '很棒！' }
						]
					},
					{
						user: { name: '李四', avatar: '/static/logo.png' },
						time: '5小时前',
						content: '分享一个传球技巧，希望对大家有帮助！',
						images: [],
						likes: 45,
						comments: 12,
						liked: true,
						showComments: false,
						commentList: []
					},
					{
						user: { name: '王五', avatar: '/static/logo.png' },
						time: '1天前',
						content: '周末有球友一起踢球吗？',
						images: [],
						likes: 18,
						comments: 8,
						liked: false,
						showComments: false,
						commentList: []
					}
				],
				showCommentInput: false,
				currentPostIndex: -1,
				commentText: ''
			}
		},
		methods: {
			viewTopic(topic) {
				uni.showModal({
					title: topic.tag,
					content: `查看 ${topic.count} 条相关动态`,
					confirmText: '查看',
					success: (res) => {
						if (res.confirm) {
							// 这里可以跳转到话题详情页
							uni.showToast({
								title: '加载话题动态',
								icon: 'loading'
							})
						}
					}
				})
			},
			viewProfile(user) {
				uni.navigateTo({
					url: '/pages/profile/index'
				})
			},
			toggleLike(index) {
				const post = this.posts[index]
				post.liked = !post.liked
				post.likes += post.liked ? 1 : -1
				
				// 点赞动画效果
				if (post.liked) {
					uni.showToast({
						title: '已点赞',
						icon: 'success',
						duration: 1000
					})
				}
			},
			showComment(index) {
				const post = this.posts[index]
				post.showComments = !post.showComments
				if (post.showComments) {
					this.currentPostIndex = index
					this.showCommentInput = true
				} else {
					this.showCommentInput = false
				}
			},
			submitComment() {
				if (!this.commentText.trim()) {
					return
				}
				if (this.currentPostIndex >= 0) {
					const post = this.posts[this.currentPostIndex]
					if (!post.commentList) {
						post.commentList = []
					}
					post.commentList.push({
						name: '我',
						text: this.commentText
					})
					post.comments++
				}
				this.commentText = ''
				this.showCommentInput = false
				uni.showToast({
					title: '评论成功',
					icon: 'success'
				})
			},
			sharePost(index) {
				uni.showToast({
					title: '分享功能',
					icon: 'none'
				})
			},
			previewImage(images, current) {
				uni.previewImage({
					urls: images,
					current: current
				})
			}
		}
	}
</script>

<style scoped>
	.community-container {
		min-height: 100vh;
		background-color: #F5F5F5;
		padding-bottom: 100rpx;
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

	.topic-scroll {
		white-space: nowrap;
	}

	.topic-item {
		display: inline-block;
		padding: 15rpx 30rpx;
		margin-right: 15rpx;
		background: linear-gradient(135deg, #E8F5E9 0%, #C8E6C9 100%);
		border-radius: 30rpx;
		vertical-align: top;
	}

	.topic-tag {
		display: block;
		font-size: 26rpx;
		color: #2E8B57;
		font-weight: 600;
		margin-bottom: 5rpx;
	}

	.topic-count {
		display: block;
		font-size: 22rpx;
		color: #666;
	}

	.friends-scroll {
		white-space: nowrap;
	}

	.friend-card {
		display: inline-block;
		width: 150rpx;
		text-align: center;
		margin-right: 20rpx;
		vertical-align: top;
	}

	.friend-avatar {
		width: 120rpx;
		height: 120rpx;
		border-radius: 50%;
		border: 3rpx solid #E5E5E5;
		margin-bottom: 10rpx;
	}

	.friend-name {
		display: block;
		font-size: 24rpx;
		color: #333;
		margin-bottom: 5rpx;
		overflow: hidden;
		text-overflow: ellipsis;
		white-space: nowrap;
	}

	.friend-distance {
		display: block;
		font-size: 22rpx;
		color: #999;
	}

	.post-list {
		display: flex;
		flex-direction: column;
		gap: 30rpx;
	}

	.post-card {
		padding-bottom: 30rpx;
		border-bottom: 1rpx solid #F0F0F0;
	}

	.post-card:last-child {
		border-bottom: none;
	}

	.post-header {
		display: flex;
		align-items: center;
		margin-bottom: 20rpx;
	}

	.post-avatar {
		width: 80rpx;
		height: 80rpx;
		border-radius: 50%;
		margin-right: 20rpx;
	}

	.post-user-info {
		flex: 1;
		display: flex;
		flex-direction: column;
		gap: 5rpx;
	}

	.post-username {
		font-size: 28rpx;
		font-weight: 600;
		color: #333;
	}

	.post-time {
		font-size: 24rpx;
		color: #999;
	}

	.post-content {
		display: block;
		font-size: 28rpx;
		color: #333;
		line-height: 1.6;
		margin-bottom: 20rpx;
	}

	.post-images {
		display: flex;
		flex-wrap: wrap;
		gap: 10rpx;
		margin-bottom: 20rpx;
	}

	.post-image {
		width: 200rpx;
		height: 200rpx;
		border-radius: 10rpx;
		background-color: #F0F0F0;
	}

	.post-footer {
		display: flex;
		align-items: center;
		gap: 40rpx;
	}

	.post-action {
		display: flex;
		align-items: center;
		gap: 8rpx;
	}

	.action-icon {
		font-size: 32rpx;
		transition: transform 0.2s;
	}

	.action-icon.liked {
		animation: likeAnimation 0.3s;
	}

	@keyframes likeAnimation {
		0% { transform: scale(1); }
		50% { transform: scale(1.3); }
		100% { transform: scale(1); }
	}

	.action-count {
		font-size: 24rpx;
		color: #666;
	}

	.comment-list {
		margin-top: 20rpx;
		padding: 20rpx;
		background-color: #F8F8F8;
		border-radius: 10rpx;
		display: flex;
		flex-direction: column;
		gap: 15rpx;
	}

	.comment-item {
		font-size: 26rpx;
		color: #333;
		line-height: 1.5;
	}

	.comment-name {
		font-weight: 600;
		color: #3CB371;
	}

	.comment-text {
		color: #666;
	}

	.comment-input {
		position: fixed;
		bottom: 0;
		left: 0;
		right: 0;
		background-color: #FFFFFF;
		padding: 20rpx;
		border-top: 1rpx solid #E5E5E5;
		display: flex;
		align-items: center;
		gap: 20rpx;
		z-index: 100;
	}

	.input-field {
		flex: 1;
		height: 70rpx;
		padding: 0 20rpx;
		background-color: #F8F8F8;
		border-radius: 35rpx;
		font-size: 28rpx;
		color: #333;
	}

	.send-btn {
		font-size: 28rpx;
		color: #3CB371;
		font-weight: 600;
		padding: 15rpx 30rpx;
	}
</style>

