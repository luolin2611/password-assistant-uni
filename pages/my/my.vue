<template>
  <view class="container">
    <!-- 用户信息区域 -->
    <view class="user-info">
      <image class="avatar" src="/static/default-avatar.png" mode="aspectFill"></image>
      <text class="username">{{userInfo.username || '未设置昵称'}}</text>
      
      <!-- 统计信息 -->
      <view class="stats flex">
        <view class="stat-item">
          <text class="number">{{passwordCount}}</text>
          <text class="label">密码记录</text>
        </view>
        <view class="stat-item">
          <text class="number">{{createTime}}</text>
          <text class="label">创建时间</text>
        </view>
      </view>
    </view>
    
    <!-- 退出登录按钮 -->
    <button class="logout-btn" @tap="handleLogout">退出登录</button>
  </view>
</template>

<script>
export default {
  data() {
    return {
      userInfo: {},
      passwordCount: 0,
      createTime: ''
    }
  },
  onLoad() {
    this.loadUserInfo()
  },
  methods: {
    loadUserInfo() {
      // 加载用户信息
      this.userInfo = {
        username: '测试用户'
      }
      this.passwordCount = 10
      this.createTime = '2024-03-20'
    },
    handleLogout() {
      uni.showModal({
        title: '提示',
        content: '确定要退出登录吗？',
        success: (res) => {
          if (res.confirm) {
            // 执行退出登录逻辑
            uni.reLaunch({
              url: '/pages/login/login'
            })
          }
        }
      })
    }
  }
}
</script>

<style>
.container {
  padding: 40rpx;
}

.user-info {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 60rpx 0;
}

.avatar {
  width: 160rpx;
  height: 160rpx;
  border-radius: 50%;
  margin-bottom: 30rpx;
}

.username {
  font-size: 36rpx;
  font-weight: bold;
  margin-bottom: 40rpx;
}

.stats {
  width: 100%;
  justify-content: space-around;
  margin-top: 40rpx;
}

.stat-item {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.number {
  font-size: 40rpx;
  font-weight: bold;
  color: #007AFF;
  margin-bottom: 10rpx;
}

.label {
  font-size: 28rpx;
  color: #666;
}

.logout-btn {
  margin-top: 80rpx;
  background-color: #dd524d;
  color: #FFFFFF;
}
</style> 