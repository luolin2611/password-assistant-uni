<template>
  <view class="container">
    <!-- 平台信息 -->
    <view class="section">
      <view class="section-title">平台信息</view>
      <view class="info-item">
        <image class="platform-image" :src="passwordDetail.projectImage" mode="aspectFill"></image>
        <view class="platform-info">
          <view class="platform-name">{{passwordDetail.platformName}}</view>
          <view class="description">{{passwordDetail.description}}</view>
        </view>
      </view>
    </view>
    
    <!-- 密码信息 -->
    <view class="section">
      <view class="section-title">密码信息</view>
      <view class="password-area">
        <text class="password-text" v-if="showPassword">{{passwordDetail.password}}</text>
        <text class="password-text" v-else>••••••</text>
        <uni-icons 
          :type="showPassword ? 'eye-filled' : 'eye'"
          size="24"
          color="#666"
          @click="togglePassword"
        ></uni-icons>
      </view>
    </view>
    
    <!-- 其他信息 -->
    <view class="section">
      <view class="section-title">其他信息</view>
      <view class="info-row">
        <text class="label">创建时间</text>
        <text class="value">{{passwordDetail.createTime}}</text>
      </view>
      <view class="info-row">
        <text class="label">更新时间</text>
        <text class="value">{{passwordDetail.updateTime}}</text>
      </view>
    </view>
    
    <!-- 操作按钮 -->
    <view class="action-buttons">
      <button class="btn edit-btn" @tap="handleEdit">编辑</button>
      <button class="btn delete-btn" @tap="handleDelete">删除</button>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      id: '',
      showPassword: false,
      passwordDetail: {
        id: '',
        projectImage: '',
        platformName: '',
        password: '',
        description: '',
        createTime: '',
        updateTime: ''
      }
    }
  },
  onLoad(options) {
    this.id = options.id
    // 设置导航栏标题
    uni.setNavigationBarTitle({
      title: decodeURIComponent(options.platformName)
    })
    this.loadPasswordDetail()
  },
  methods: {
    // 加载密码详情
    loadPasswordDetail() {
      // TODO: 从存储或服务器获取密码详情
      this.passwordDetail = {
        id: this.id,
        projectImage: '/static/default-avatar.png',
        platformName: '测试平台',
        password: '123456',
        description: '这是一个测试账号',
        createTime: '2024-03-20 12:00:00',
        updateTime: '2024-03-20 12:00:00'
      }
    },
    
    // 切换密码显示
    togglePassword() {
      this.showPassword = !this.showPassword
      if (this.showPassword) {
        uni.setClipboardData({
          data: this.passwordDetail.password,
          success: () => {
            uni.showToast({
              title: '密码已复制',
              icon: 'none'
            })
          }
        })
      }
    },
    
    // 编辑密码
    handleEdit() {
      uni.navigateTo({
        url: `/pages/password/add?id=${this.id}`
      })
    },
    
    // 删除密码
    handleDelete() {
      uni.showModal({
        title: '提示',
        content: '确定要删除该密码记录吗？',
        success: (res) => {
          if (res.confirm) {
            // TODO: 执行删除操作
            uni.showToast({
              title: '已删除',
              icon: 'success',
              success: () => {
                setTimeout(() => {
                  uni.navigateBack()
                }, 1500)
              }
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
  padding: 30rpx;
  background-color: #F8F8F8;
  min-height: 100vh;
}

.section {
  background-color: #FFFFFF;
  border-radius: 12rpx;
  padding: 30rpx;
  margin-bottom: 30rpx;
}

.section-title {
  font-size: 32rpx;
  font-weight: bold;
  margin-bottom: 20rpx;
  color: #333;
}

.info-item {
  display: flex;
  align-items: center;
}

.platform-image {
  width: 100rpx;
  height: 100rpx;
  border-radius: 12rpx;
  margin-right: 20rpx;
}

.platform-info {
  flex: 1;
}

.platform-name {
  font-size: 32rpx;
  font-weight: bold;
  margin-bottom: 10rpx;
}

.description {
  font-size: 28rpx;
  color: #666;
}

.password-area {
  display: flex;
  align-items: center;
  justify-content: space-between;
  background-color: #F5F5F5;
  padding: 20rpx;
  border-radius: 8rpx;
}

.password-text {
  font-size: 32rpx;
  color: #333;
  flex: 1;
  margin-right: 20rpx;
}

.info-row {
  display: flex;
  justify-content: space-between;
  margin-bottom: 20rpx;
}

.info-row:last-child {
  margin-bottom: 0;
}

.label {
  color: #666;
  font-size: 28rpx;
}

.value {
  color: #333;
  font-size: 28rpx;
}

.action-buttons {
  display: flex;
  gap: 30rpx;
  margin-top: 60rpx;
}

.btn {
  flex: 1;
  height: 88rpx;
  line-height: 88rpx;
  border-radius: 44rpx;
  font-size: 32rpx;
}

.edit-btn {
  background-color: #007AFF;
  color: #FFFFFF;
}

.delete-btn {
  background-color: #dd524d;
  color: #FFFFFF;
}
</style> 