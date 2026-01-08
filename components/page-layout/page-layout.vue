<template>
  <view class="page-container">
    <view class="bg-decoration"></view>

    <!-- 1. 顶部导航栏 (兼容 H5/小程序) -->
    <view class="nav-fixed">
      <view :style="{ height: statusBarHeight + 'px' }"></view>
      <view class="title-bar" :style="{ height: titleBarHeight + 'px' }">
        <view class="hamburger-btn" @tap="toggleDrawer(true)">
          <view class="line"></view>
          <view class="line"></view>
          <view class="line"></view>
        </view>
        <text class="nav-title">{{ title }}</text>
      </view>
    </view>

    <!-- 2. 抽屉遮罩 -->
    <view class="drawer-mask" :class="{ 'mask-active': isDrawerOpen }" @tap="toggleDrawer(false)" @touchmove.stop.prevent></view>

    <!-- 3. 抽屉内容 -->
    <view class="drawer-content" :class="{ 'drawer-active': isDrawerOpen }" @tap.stop>
      <view class="drawer-header">切换驾驶舱</view>
      <view class="menu-list">
        <view class="menu-item" v-for="(item, index) in menuList" :key="index" @tap="navTo(item.path)">
          <text class="menu-icon">&gt;</text>
          <text class="menu-text">{{ item.name }}</text>
        </view>
      </view>
    </view>

    <!-- 页面内容占位 -->
    <view :style="{ height: headerHeight + 'px' }"></view>

    <view class="page-content">
      <slot></slot>
    </view>
  </view>
</template>

<script setup>
import { ref, onMounted } from 'vue';

const props = defineProps({ title: { type: String, default: '控制中心' } });
const emit = defineEmits(['drawerStateChange']);

const isDrawerOpen = ref(false);
const statusBarHeight = ref(0);
const titleBarHeight = ref(44); // 默认 44px
const headerHeight = ref(0);

const menuList = [{ name: '控制台主页', path: '/pages/index/center' }];

const toggleDrawer = (status) => {
  isDrawerOpen.value = status;
  emit('drawerStateChange', status);
};

const navTo = (url) => {
  isDrawerOpen.value = false;
  uni.redirectTo({ url });
};

onMounted(() => {
  const systemInfo = uni.getSystemInfoSync();
  statusBarHeight.value = systemInfo.statusBarHeight || 0;

  // #ifdef MP-WEIXIN
  // 小程序端计算胶囊位置
  const menuButtonInfo = uni.getMenuButtonBoundingClientRect();
  titleBarHeight.value = (menuButtonInfo.top - statusBarHeight.value) * 2 + menuButtonInfo.height;
  // #endif

  // #ifdef H5
  titleBarHeight.value = 44; // H5 统一定义为 44px
  // #endif

  headerHeight.value = statusBarHeight.value + titleBarHeight.value;
});
</script>

<style scoped>
.page-container {
  min-height: 100vh;
  background-color: #050a0f;
  position: relative;
  overflow: hidden;
}

/* --- 页面淡入动画 --- */
@keyframes pageFadeIn {
  from {
    opacity: 0;
    transform: translateY(30rpx);
    filter: blur(10px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
    filter: blur(0);
  }
}

.page-content {
  animation: pageFadeIn 0.6s cubic-bezier(0.16, 1, 0.3, 1) forwards;
  padding: 20rpx;
  position: relative;
  z-index: 1;
}

/* --- 扫描线加载遮罩 --- */
.loading-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background-color: rgba(5, 10, 15, 0.98);
  z-index: 10000;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

.scan-box {
  width: 400rpx;
  height: 200rpx;
  border: 1px solid rgba(0, 242, 255, 0.2);
  position: relative;
  overflow: hidden;
}

.scan-line {
  width: 100%;
  height: 4rpx;
  background: #00f2ff;
  box-shadow: 0 0 20rpx #00f2ff;
  position: absolute;
  top: 0;
  animation: scanning 0.8s linear infinite;
}

@keyframes scanning {
  0% {
    top: 0%;
    opacity: 0;
  }
  50% {
    opacity: 1;
  }
  100% {
    top: 100%;
    opacity: 0;
  }
}

.loading-text {
  margin-top: 40rpx;
  color: #00f2ff;
  font-size: 22rpx;
  letter-spacing: 8rpx;
  text-shadow: 0 0 10rpx #00f2ff;
  font-family: monospace;
}

/* --- 导航栏 --- */
.nav-fixed {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  z-index: 1000;
  background-color: #050a0f;
}

.title-bar {
  display: flex;
  align-items: center;
  justify-content: center;
  position: relative;
}

.nav-title {
  font-size: 34rpx;
  font-weight: 900;
  color: #00f2ff;
  text-shadow: 0 0 10rpx #00f2ff;
}

.hamburger-btn {
  position: absolute;
  left: 30rpx;
  width: 40rpx;
  height: 28rpx;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}

.line {
  width: 100%;
  height: 3rpx;
  background: #00f2ff;
  box-shadow: 0 0 8rpx #00f2ff;
}

/* --- 抽屉与遮罩 --- */
.drawer-mask {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background: rgba(0, 0, 0, 0);
  transition: all 0.4s ease;
  z-index: 9998;
  pointer-events: none;
}

.mask-active {
  background: rgba(0, 0, 0, 0.8);
  backdrop-filter: blur(8px);
  pointer-events: auto;
}

.drawer-content {
  position: fixed;
  top: 0;
  left: 0;
  width: 65%;
  height: 100%;
  background: #050a0f;
  z-index: 9999;
  transform: translateX(-105%);
  transition: transform 0.4s cubic-bezier(0.16, 1, 0.3, 1);
  padding: 120rpx 0;
  border-right: 1px solid rgba(0, 242, 255, 0.1);
}

.drawer-active {
  transform: translateX(0);
}

.drawer-header {
  color: #00f2ff;
  font-weight: bold;
  padding: 0 40rpx 40rpx;
  font-size: 34rpx;
}

.menu-item {
  display: flex;
  align-items: center;
  color: rgba(255, 255, 255, 0.4);
  padding: 35rpx 50rpx;
  font-size: 30rpx;
  transition: all 0.3s;
}

.active-item {
  color: #fff !important;
  font-weight: bold;
  background: linear-gradient(90deg, rgba(0, 242, 255, 0.1) 0%, transparent 100%);
}

.menu-icon {
  margin-right: 20rpx;
  color: inherit;
}

.active-item .menu-icon {
  color: #00f2ff;
}

.drawer-footer {
  position: absolute;
  bottom: 40rpx;
  left: 40rpx;
  color: #1a2a3a;
  font-size: 18rpx;
}

/* 背景格栅 */
.bg-decoration {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background-image: linear-gradient(rgba(0, 242, 255, 0.03) 1px, transparent 1px),
  linear-gradient(90deg, rgba(0, 242, 255, 0.03) 1px, transparent 1px);
  background-size: 60rpx 60rpx;
  z-index: 0;
  pointer-events: none;
}
</style>