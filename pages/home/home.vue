<template>
  <view class="container">
    <!-- 搜索框区域 -->
    <view class="search-box-wrapper">
      <view class="search-box flex align-center">
        <view class="search-input flex-1 flex align-center">
          <uni-icons type="search" size="16" color="#999"></uni-icons>
          <input 
            type="text" 
            v-model="searchKey" 
            placeholder="搜索平台名称"
            confirm-type="search"
            @confirm="handleSearch"
          />
        </view>
        <button class="search-btn" @tap="handleSearch">搜索</button>
      </view>
    </view>
    
    <!-- 内容区域 -->
    <view class="content-wrapper">
      <!-- 密码列表 -->
      <view class="password-list">
        <view v-for="(item, index) in filteredPasswordList" :key="index" class="password-item-wrapper">
          <movable-area class="movable-area">
            <movable-view 
              class="movable-view"
              direction="horizontal"
              :out-of-bounds="false"
              :x="item.x || 0"
              :disabled="!item.canMove"
              @touchstart="handleTouchStart(index)"
              @touchend="handleTouchEnd(index)"
              @change="handleMovableChange($event, index)"
            >
              <view class="password-item flex" @tap.stop="viewPasswordDetail(item)">
                <!-- 左侧内容 -->
                <view class="item-left flex">
                  <image class="project-image" :src="item.projectImage" mode="aspectFill"></image>
                  <view class="info">
                    <view class="platform-name text-ellipsis">
                      <text v-if="item.isTop" class="top-tag">置顶</text>
                      {{item.platformName}}
                    </view>
                    <view class="description text-ellipsis">{{item.description}}</view>
                  </view>
                </view>
                
                <!-- 右侧内容 -->
                <view class="item-right" @tap.stop>
                  <view class="password-area flex align-center">
                    <text 
                      class="password-text" 
                      @tap.stop="copyPassword(item)"
                    >{{item.showPassword ? item.password : '••••••'}}</text>
                    <uni-icons 
                      :type="item.showPassword ? 'eye-filled' : 'eye'"
                      size="24"
                      color="#666"
                      @click.stop="togglePassword(index)"
                    ></uni-icons>
                  </view>
                  <view class="update-time">{{item.updateTime}}</view>
                </view>
              </view>
            </movable-view>
            <view class="action-buttons">
              <view class="action-btn top-btn" @tap="handleTop(item)">
                <uni-icons type="top" size="20" color="#FFFFFF"></uni-icons>
              </view>
              <view class="action-btn delete-btn" @tap="handleDelete(item)">
                <uni-icons type="trash" size="20" color="#FFFFFF"></uni-icons>
              </view>
            </view>
          </movable-area>
        </view>
      </view>
    </view>
    
    <!-- 新增按钮 -->
    <view class="add-btn" @tap="showAddModal">
      <uni-icons type="plus" size="30" color="#FFFFFF"></uni-icons>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      searchKey: '',
      passwordList: [
        {
          id: 1,
          projectImage: '/static/default-avatar.png',
          platformName: '测试平台1',
          password: '123456',
          description: '这是一个测试账号',
          updateTime: '2024-03-20',
          showPassword: false,
          isTop: false
        },
        {
          id: 2,
          projectImage: '/static/default-avatar.png',
          platformName: '测试平台2',
          password: 'abc123',
          description: '这是第二个测试账号',
          updateTime: '2024-03-21',
          showPassword: false,
          isTop: false
        },
        {
          id: 3,
          projectImage: '/static/default-avatar.png',
          platformName: '测试平台3',
          password: 'abc123',
          description: '这是第三个测试账号',
          updateTime: '2024-03-21',
          showPassword: false,
          isTop: false
        },
        {
          id: 4,
          projectImage: '/static/default-avatar.png',
          platformName: '测试平台4',
          password: 'abc123',
          description: '这是第四个测试账号',
          updateTime: '2024-03-21',
          showPassword: false,
          isTop: false
        },
        {
          id: 5,
          projectImage: '/static/default-avatar.png',
          platformName: '测试平台5',
          password: 'abc123',
          description: '这是第五个测试账号',
          updateTime: '2024-03-21',
          showPassword: false,
          isTop: false
        }
      ],
      swipeOptions: [{
        text: '置顶',
        style: {
          backgroundColor: '#007AFF'
        }
      }, {
        text: '删除',
        style: {
          backgroundColor: '#dd524d'
        }
      }],
      buttonWidth: 200,
      startX: 0,
      currentIndex: -1,
      statusBarHeight: 0,
      navBarHeight: 0,
      searchBarHeight: 0, // 搜索栏高度（包含状态栏和导航栏）
    }
  },
  computed: {
    // 过滤后的密码列表
    filteredPasswordList() {
      if (!this.searchKey) {
        // 没有搜索关键词时，按置顶状态排序
        return [...this.passwordList].sort((a, b) => {
          if (a.isTop === b.isTop) return 0
          return a.isTop ? -1 : 1
        })
      }
      // 有搜索关键词时，过滤并保持置顶排序
      return this.passwordList
        .filter(item => 
          item.platformName.toLowerCase().includes(this.searchKey.toLowerCase())
        )
        .sort((a, b) => {
          if (a.isTop === b.isTop) return 0
          return a.isTop ? -1 : 1
        })
    }
  },
  methods: {
    // 搜索处理
    handleSearch() {
      console.log('搜索关键词：', this.searchKey)
    },
    
    // 切换密码显示
    togglePassword(index) {
      const item = this.filteredPasswordList[index]
      const originalIndex = this.passwordList.findIndex(p => p.id === item.id)
      this.$set(this.passwordList[originalIndex], 'showPassword', !item.showPassword)
    },
    
    // 处理滑动操作
    handleSwipeClick(e, item) {
      if (e.index === 0) {
        this.handleTop(item)
      } else {
        this.handleDelete(item)
      }
    },
    
    // 置顶操作
    handleTop(item) {
      const index = this.passwordList.findIndex(p => p.id === item.id)
      if (index > -1) {
        this.$set(this.passwordList[index], 'isTop', !item.isTop)
        // 重置滑动状态
        this.$set(item, 'x', 0)
        uni.showToast({
          title: item.isTop ? '已取消置顶' : '已置顶',
          icon: 'success'
        })
      }
    },
    
    // 删除操作
    handleDelete(item) {
      uni.showModal({
        title: '提示',
        content: '确定要删除该密码记录吗？',
        success: (res) => {
          if (res.confirm) {
            const index = this.passwordList.findIndex(p => p.id === item.id)
            if (index > -1) {
              this.passwordList.splice(index, 1)
              uni.showToast({
                title: '已删除',
                icon: 'success'
              })
            }
          }
          // 无论是否确认删除，都重置滑动状态
          this.$set(item, 'x', 0)
        }
      })
    },
    
    // 查看密码详情
    viewPasswordDetail(item) {
      uni.navigateTo({
        url: `/pages/password/detail?id=${item.id}&platformName=${encodeURIComponent(item.platformName)}`
      })
    },
    
    // 显示添加模态框
    showAddModal() {
      uni.navigateTo({
        url: '/pages/password/add'
      })
    },
    
    // 修改加载密码列表方法
    loadPasswordList() {
      // 这里可以添加从本地存储或服务器获取数据的逻辑
      console.log('加载密码列表')
      // 如果需要从存储或服务器加载数据，可以在这里处理
      // 暂时不覆盖测试数据
      /*
      this.passwordList = [
        // ... 测试数据
      ]
      */
    },
    
    // 处理触摸开始
    handleTouchStart(index) {
      this.currentIndex = index
      this.startX = this.filteredPasswordList[index].x || 0
      // 重置其他项的滑动状态
      this.resetAllItems(index)
    },
    
    // 处理触摸结束
    handleTouchEnd(index) {
      const item = this.filteredPasswordList[index]
      const x = item.x || 0
      
      // 如果滑动距离超过按钮宽度的一半，则完全展开
      if (x < -this.buttonWidth / 2) {
        this.$set(item, 'x', -this.buttonWidth)
      } else {
        this.$set(item, 'x', 0)
      }
    },
    
    // 处理滑动变化
    handleMovableChange(e, index) {
      const x = e.detail.x
      if (this.currentIndex === index) {
        // 限制最大滑动距离
        const limitedX = Math.max(-this.buttonWidth, Math.min(0, x))
        this.$set(this.filteredPasswordList[index], 'x', limitedX)
      }
    },
    
    // 重置所有项的滑动状态
    resetAllItems(exceptIndex) {
      this.filteredPasswordList.forEach((item, index) => {
        if (index !== exceptIndex) {
          this.$set(item, 'x', 0)
        }
      })
    },
    
    // 复制密码
    copyPassword(item) {
      uni.setClipboardData({
        data: item.password,
        success: () => {
          uni.showToast({
            title: '密码已复制',
            icon: 'none'
          })
        }
      })
    },
  },
  onLoad() {
    // 修改获取全局变量的方式
    try {
      const app = getApp({allowDefault: true})
      if (app && app.globalData) {
        this.statusBarHeight = app.globalData.statusBarHeight || 0
        this.navBarHeight = app.globalData.navBarHeight || 0
        // 计算搜索栏总高度（状态栏 + 导航栏 + 搜索框高度）
        this.searchBarHeight = this.statusBarHeight + this.navBarHeight + 64 // 64 是搜索框的高度(含padding)
      } else {
        // 如果获取不到 globalData，使用系统信息
        const systemInfo = uni.getSystemInfoSync()
        this.statusBarHeight = systemInfo.statusBarHeight
        this.navBarHeight = 44 // 默认导航栏高度
        this.searchBarHeight = this.statusBarHeight + this.navBarHeight + 64
      }
    } catch (e) {
      console.error('获取系统信息失败', e)
      // 使用默认值
      this.statusBarHeight = 20
      this.navBarHeight = 44
      this.searchBarHeight = 128 // 20 + 44 + 64
    }

    // 初始化加载数据
    this.loadPasswordList()
  }
}
</script>

<style>
.container {
  min-height: 100vh;
  background-color: #F8F8F8;
  padding-bottom: 100rpx;
  padding-top: 180rpx; /* 为固定的搜索框留出空间 */
}

/* 搜索框固定样式 */
.search-box-wrapper {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  z-index: 100;
  background-color: #FFFFFF;
  padding: 20rpx;
  box-shadow: 0 2rpx 8rpx rgba(0,0,0,0.1);
}

.search-box {
  gap: 20rpx;
}

.search-input {
  flex: 1;
  height: 72rpx;
  background-color: #F5F5F5;
  border-radius: 36rpx;
  padding: 0 30rpx;
  gap: 10rpx;
}

.search-input input {
  flex: 1;
  height: 100%;
  font-size: 28rpx;
}

.search-btn {
  width: 120rpx;
  height: 72rpx;
  line-height: 72rpx;
  font-size: 28rpx;
  padding: 0;
  margin: 0;
  background-color: #007AFF;
  color: #FFFFFF;
  border-radius: 36rpx;
}

/* 内容区域样式 */
.content-wrapper {
  padding: 0 20rpx;
}

.header {
  padding: 40rpx 0;
  justify-content: center;
}

.avatar {
  width: 120rpx;
  height: 120rpx;
  border-radius: 50%;
}

.password-list {
  background-color: transparent;
  padding: 20rpx;
}

.password-item-wrapper {
  position: relative;
  margin-bottom: 20rpx;
  overflow: hidden;
  border-radius: 12rpx;
  background-color: #FFFFFF;
  box-shadow: 0 2rpx 8rpx rgba(0,0,0,0.1);
}

.password-item-wrapper:last-child {
  margin-bottom: 0;
}

.movable-area {
  width: 100%;
  height: 140rpx;
  position: relative;
  background-color: #FFFFFF;
  border-radius: 12rpx;
}

.movable-view {
  width: 100%;
  height: 100%;
  z-index: 2;
  background-color: #FFFFFF;
  border-radius: 12rpx;
}

.password-item {
  padding: 20rpx;
  background-color: #FFFFFF;
  display: flex;
  justify-content: space-between;
  align-items: center;
  height: 100%;
  box-sizing: border-box;
  border-radius: 12rpx;
}

.item-left {
  flex: 1;
  display: flex;
  align-items: center;
  margin-right: 20rpx;
}

.project-image {
  width: 70rpx; /* 稍微减小图片尺寸 */
  height: 70rpx;
  border-radius: 8rpx;
}

.info {
  flex: 1;
  min-width: 0; /* 确保文本可以正确省略 */
}

.platform-name {
  font-size: 30rpx;
}

.description {
  font-size: 26rpx;
}

.item-right {
  display: flex;
  flex-direction: column;
  align-items: flex-end;
  flex-shrink: 0;
  min-width: 120rpx; /* 确保右侧区域有足够宽度 */
}

.password-area {
  display: flex;
  align-items: center;
  margin-bottom: 10rpx;
}

.password-text {
  font-size: 26rpx;
}

.update-time {
  font-size: 22rpx;
}

/* 新增按钮样式 */
.add-btn {
  position: fixed;
  right: 40rpx;
  bottom: calc(100rpx + 40rpx); /* tabbar高度 + 底部间距 */
  width: 100rpx;
  height: 100rpx;
  background-color: #007AFF;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow: 0 4rpx 12rpx rgba(0,0,0,0.2);
  z-index: 101;
}

/* 修改滑动按钮样式 */
.action-buttons {
  position: absolute;
  right: -200rpx; /* 初始位置在屏幕外 */
  top: 0;
  height: 100%;
  display: flex;
  z-index: 1;
}

.action-btn {
  width: 100rpx;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #FFFFFF;
}

.top-btn {
  background-color: #007AFF;
}

.delete-btn {
  background-color: #dd524d;
}

/* 添加置顶标签样式 */
.top-tag {
  font-size: 20rpx;
  color: #007AFF;
  border: 1rpx solid #007AFF;
  padding: 2rpx 8rpx;
  border-radius: 4rpx;
  margin-right: 10rpx;
}

/* 添加过渡动画 */
.movable-view {
  transition: transform 0.3s ease;
}

/* 添加点击效果 */
.password-text:active {
  opacity: 0.7;
}
</style> 