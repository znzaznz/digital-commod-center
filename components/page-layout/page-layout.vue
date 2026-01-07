<!-- components/page-layout/page-layout.vue -->
<template>
  <view class="page-container">
    <!-- 全局背景装饰 -->
    <view class="bg-decoration"></view>

    <!-- 1. 扫描线加载遮罩 (仅在跳转时显示) -->
    <view class="loading-overlay" v-if="isNavigating">
      <view class="scan-box">
        <view class="scan-line"></view>
      </view>
      <text class="loading-text">指挥仓切换中...</text>
    </view>

    <!-- 导航栏 -->
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

    <!-- 抽屉遮罩 -->
    <view class="drawer-mask" :class="{ 'mask-active': isDrawerOpen }" @tap="toggleDrawer(false)"
          @touchmove.stop.prevent></view>

    <!-- 抽屉内容主体 -->
    <view class="drawer-content" :class="{ 'drawer-active': isDrawerOpen }" @tap.stop>
      <view class="drawer-header">切换驾驶舱</view>
      <view class="menu-list">
        <view
            class="menu-item"
            :class="{ 'active-item': item.path === currentPath }"
            v-for="(item, index) in menuList"
            :key="index"
            @tap="navTo(item.path)"
        >
          <text class="menu-icon" decode>&gt;</text>
          <text class="menu-text">{{ item.name }}</text>
        </view>
      </view>
      <view class="drawer-footer">CORE SYSTEM V1.0.4</view>
    </view>

    <!-- 页面内容占位 -->
    <view :style="{ height: headerHeight + 'px' }"></view>

    <!-- 2. 页面内容主体 (带有入场动画) -->
    <view class="page-content">
      <slot></slot>
    </view>
  </view>
</template>

<script setup>
import {ref, onMounted} from 'vue';

defineProps({title: {type: String, default: '控制中心'}});

const emit = defineEmits(['drawerStateChange']);

const isDrawerOpen = ref(false);
const isNavigating = ref(false); // 跳转状态控制
const statusBarHeight = ref(0);
const titleBarHeight = ref(0);
const headerHeight = ref(0);
const currentPath = ref('');

const toggleDrawer = (status) => {
  isDrawerOpen.value = status;
  emit('drawerStateChange', status);
};

const menuList = [
  {name: '控制台主页', path: '/pages/index/index'},
  {name: '运行日志', path: '/pages/logs/logs'},
  {name: '资源监控', path: '/pages/monitor/monitor'},
  {name: '系统设置', path: '/pages/settings/settings'}
];

const navTo = (url) => {
  if (url === currentPath.value) {
    toggleDrawer(false);
    return;
  }

  toggleDrawer(false);
  // 开启扫描动画
  isNavigating.value = true;

  // 延迟一小会儿跳转，让用户看到扫描过程，增加仪式感
  setTimeout(() => {
    uni.redirectTo({
      url,
      complete: () => {
        // 虽然跳转后页面卸载，但养成良好的清理习惯
        isNavigating.value = false;
      }
    });
  }, 450);
};

onMounted(() => {
  const systemInfo = uni.getWindowInfo();
  statusBarHeight.value = systemInfo.statusBarHeight;
  const menuButtonInfo = uni.getMenuButtonBoundingClientRect();
  titleBarHeight.value = (menuButtonInfo.top - statusBarHeight.value) * 2 + menuButtonInfo.height;
  headerHeight.value = statusBarHeight.value + titleBarHeight.value;

  const pages = getCurrentPages();
  const currentPage = pages[pages.length - 1];
  if (currentPage) {
    currentPath.value = '/' + currentPage.route;
  }
});
</script>

<style scoped>
/* --- 基础布局 --- */
.page-container {
  min-height: 100vh;
  background-color: #050a0f;
  position: relative;
  overflow: hidden;
}

/* --- 页面入场动画 --- */
@keyframes pageFadeIn {
  from {
    opacity: 0;
    transform: translateY(40rpx);
    filter: blur(10px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
    filter: blur(0);
  }
}

.page-content {
  animation: pageFadeIn 0.7s cubic-bezier(0.165, 0.84, 0.44, 1) forwards;
  opacity: 0;
  will-change: transform, opacity;
  padding: 20rpx;
  position: relative;
  z-index: 1;
}

/* --- 扫描线加载层样式 --- */
.loading-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background-color: rgba(5, 10, 15, 0.95);
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
  background: linear-gradient(to right, transparent, #00f2ff, transparent);
  box-shadow: 0 0 15rpx #00f2ff;
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
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
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
  z-index: 10;
}

.line {
  width: 100%;
  height: 3rpx;
  background: #00f2ff;
  box-shadow: 0 0 8rpx #00f2ff;
}

/* --- 抽屉样式 --- */
.drawer-mask {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background: rgba(0, 0, 0, 0);
  backdrop-filter: blur(0px);
  z-index: 9998;
  pointer-events: none;
  transition: all 0.4s ease;
}

.mask-active {
  background: rgba(0, 0, 0, 0.7);
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
  box-shadow: 20rpx 0 50rpx rgba(0, 0, 0, 0.9);
  padding: 120rpx 0;
  z-index: 9999;
  transform: translateX(-105%);
  transition: transform 0.4s cubic-bezier(0.165, 0.84, 0.44, 1);
}

.drawer-active {
  transform: translateX(0);
}

.drawer-header {
  color: #00f2ff;
  font-weight: bold;
  margin-bottom: 40rpx;
  font-size: 34rpx;
  padding-left: 40rpx;
}

/* --- 菜单项 --- */
.menu-item {
  display: flex;
  align-items: center;
  color: rgba(255, 255, 255, 0.5);
  padding: 35rpx 30rpx;
  padding-left: 50rpx;
  border-bottom: 1px solid rgba(255, 255, 255, 0.05);
  font-size: 30rpx;
  transition: all 0.3s;
  position: relative;
}

.active-item {
  color: #ffffff !important;
  background: linear-gradient(90deg, rgba(0, 242, 255, 0.15) 0%, transparent 100%);
  font-weight: bold;
}

.menu-icon {
  margin-right: 20rpx;
  font-weight: bold;
  color: inherit;
}

.active-item .menu-icon {
  color: #00f2ff;
}

.drawer-footer {
  position: absolute;
  bottom: 40rpx;
  color: #2a3a4a;
  font-size: 20rpx;
  letter-spacing: 2rpx;
}

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