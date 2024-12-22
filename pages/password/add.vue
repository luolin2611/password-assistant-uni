<template>
  <view class="container">
    <form @submit="handleSubmit">
      <!-- 项目图片 -->
      <view class="form-item">
        <text class="label">项目图片</text>
        <view class="upload-image" @tap="chooseImage">
          <image v-if="formData.projectImage" :src="formData.projectImage" mode="aspectFill"></image>
          <uni-icons v-else type="camera-filled" size="40" color="#999"></uni-icons>
        </view>
      </view>
      
      <!-- 平台名称 -->
      <view class="form-item">
        <text class="label">平台名称</text>
        <input 
          class="input" 
          v-model="formData.platformName"
          placeholder="请输入平台名称"
        />
      </view>
      
      <!-- 密码 -->
      <view class="form-item">
        <text class="label">密码</text>
        <view class="password-input">
          <input 
            class="input" 
            :type="showPassword ? 'text' : 'password'"
            v-model="formData.password"
            placeholder="请输入密码"
          />
          <uni-icons 
            :type="showPassword ? 'eye-filled' : 'eye'" 
            size="24" 
            @click="togglePasswordVisibility"
          ></uni-icons>
        </view>
      </view>
      
      <!-- 描述信息 -->
      <view class="form-item">
        <text class="label">描述信息</text>
        <textarea 
          class="textarea" 
          v-model="formData.description"
          placeholder="请输入描述信息"
        />
      </view>
      
      <!-- 提交按钮 -->
      <button class="submit-btn" form-type="submit" type="primary">保存</button>
    </form>
  </view>
</template>

<script>
export default {
  data() {
    return {
      formData: {
        projectImage: '',
        platformName: '',
        password: '',
        description: ''
      },
      showPassword: false
    }
  },
  methods: {
    // 选择图片
    chooseImage() {
      uni.chooseImage({
        count: 1,
        success: (res) => {
          this.formData.projectImage = res.tempFilePaths[0]
        }
      })
    },
    
    // 切换密码显示
    togglePasswordVisibility() {
      this.showPassword = !this.showPassword
    },
    
    // 提交表单
    handleSubmit() {
      if (!this.formData.platformName || !this.formData.password) {
        uni.showToast({
          title: '请填写必要信息',
          icon: 'none'
        })
        return
      }
      
      // 添加创建时间和更新时间
      const now = new Date().toISOString()
      const passwordData = {
        ...this.formData,
        createTime: now,
        updateTime: now
      }
      
      // 保存密码记录
      // TODO: 实现数据存储逻辑
      
      uni.navigateBack()
    }
  }
}
</script>

<style>
.container {
  padding: 30rpx;
}

.form-item {
  margin-bottom: 40rpx;
}

.label {
  display: block;
  margin-bottom: 20rpx;
  font-size: 28rpx;
  color: #333;
}

.upload-image {
  width: 200rpx;
  height: 200rpx;
  border: 2rpx dashed #ddd;
  border-radius: 12rpx;
  display: flex;
  align-items: center;
  justify-content: center;
}

.upload-image image {
  width: 100%;
  height: 100%;
  border-radius: 12rpx;
}

.input {
  height: 80rpx;
  border: 2rpx solid #eee;
  border-radius: 8rpx;
  padding: 0 20rpx;
}

.password-input {
  display: flex;
  align-items: center;
  border: 2rpx solid #eee;
  border-radius: 8rpx;
  padding-right: 20rpx;
}

.password-input .input {
  flex: 1;
  border: none;
}

.textarea {
  width: 100%;
  height: 200rpx;
  border: 2rpx solid #eee;
  border-radius: 8rpx;
  padding: 20rpx;
}

.submit-btn {
  margin-top: 60rpx;
}
</style> 