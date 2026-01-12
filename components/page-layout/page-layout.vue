<template>
  <view class="page-container">
    <!-- 1. 全屏扫描加载遮罩 -->
    <view v-if="isLoading" class="loading-overlay">
      <view class="scan-box">
        <view class="scan-line"></view>
      </view>
      <text class="loading-text">指挥舱加载中...</text>
    </view>

    <view class="bg-decoration"></view>

    <!-- 2. 顶部导航栏 -->
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

    <!-- 3. 抽屉遮罩 -->
    <view class="drawer-mask" :class="{ 'mask-active': isDrawerOpen }" @tap="toggleDrawer(false)"
          @touchmove.stop.prevent></view>

    <!-- 4. 抽屉内容 -->
    <view class="drawer-content" :class="{ 'drawer-active': isDrawerOpen }" @tap.stop>
      <view class="drawer-header">切换驾驶舱</view>
      <view class="menu-list">
        <view
            class="menu-item"
            v-for="(item, index) in menuList"
            :key="index"
            :class="{ 'active-item': currentPath === item.path }"
            @tap="navTo(item.path)"
        >
          <text class="menu-text">{{ item.name }}</text>
        </view>
      </view>
    </view>

    <!-- 5. 页面核心内容区 -->
    <!-- 这里用 flex 布局，先放一个导航栏占位块，剩下的全部给 page-content -->
    <view :style="{ height: headerHeight + 'px' }" style="flex-shrink: 0;"></view>

    <view class="page-content">
      <slot></slot>
    </view>
  </view>
</template>

<script setup>
import {ref, onMounted} from 'vue';

const props = defineProps({title: {type: String, default: '数字技术中心'}});
const emit = defineEmits(['drawerStateChange']);

const isDrawerOpen = ref(false);
const isLoading = ref(false);
const statusBarHeight = ref(0);
const titleBarHeight = ref(44);
const headerHeight = ref(0);
const currentPath = ref('');

const menuList = [
  {name: '控制中心', path: '/pages/center/center'},
  {name: '运行日志', path: '/pages/logs/logs'},
  {name: '资源监控', path: '/pages/monitor/monitor'},
  {name: '系统设置', path: '/pages/settings/settings'}
];

const toggleDrawer = (status) => {
  isDrawerOpen.value = status;
  emit('drawerStateChange', status);
};

const navTo = (url) => {
  if (currentPath.value === url) {
    isDrawerOpen.value = false;
    return;
  }
  isDrawerOpen.value = false;
  isLoading.value = true;
  emit('drawerStateChange', true);
  setTimeout(() => {
    uni.redirectTo({
      url,
      success: () => {
        isLoading.value = false;
        emit('drawerStateChange', false);
      }
    });
  }, 100);
};

onMounted(() => {
  const pages = getCurrentPages();
  if (pages.length > 0) {
    const currentPage = pages[pages.length - 1];
    currentPath.value = '/' + currentPage.route;
  }
  try {
    const windowInfo = uni.getWindowInfo();
    statusBarHeight.value = windowInfo.statusBarHeight || 0;
  } catch (e) {
    const systemInfo = uni.getSystemInfoSync();
    statusBarHeight.value = systemInfo.statusBarHeight || 0;
  }
  // #ifdef MP-WEIXIN
  const menuButtonInfo = uni.getMenuButtonBoundingClientRect();
  titleBarHeight.value = (menuButtonInfo.top - statusBarHeight.value) * 2 + menuButtonInfo.height;
  // #endif
  headerHeight.value = statusBarHeight.value + titleBarHeight.value;
});
</script>

<style>
/* 注意：为了让 100% 生效，必须在 App.vue 或这里去掉 scoped 设置全局 page */
page {
  height: 100%;
  overflow: hidden;
  margin: 0;
  padding: 0;
}
</style>

<style scoped>
.page-container {
  height: 100vh; /* 固定高度 */
  display: flex; /* 开启flex */
  flex-direction: column;
  background-color: #050a0f;
  position: relative;
  overflow: hidden;
}

.page-content {
  flex: 1; /* 关键：占据除导航栏外的所有空间 */
  display: flex; /* 关键：让slot的子元素也能flex */
  flex-direction: column;
  padding: 20rpx;
  position: relative;
  z-index: 1;
  overflow-y: auto; /* 内容过多时内部滚动 */
  animation: pageFadeIn 0.6s cubic-bezier(0.16, 1, 0.3, 1) forwards;
}

/* --- 下面保持你原有的其他样式不变 --- */
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
  position: relative;
}

.active-item {
  color: #fff !important;
  font-weight: bold;
  background: linear-gradient(90deg, rgba(0, 242, 255, 0.15) 0%, transparent 100%);
}

.active-item::before {
  content: '';
  position: absolute;
  left: 0;
  top: 20%;
  height: 60%;
  width: 6rpx;
  background: #00f2ff;
  box-shadow: 0 0 15rpx #00f2ff;
}

.bg-decoration {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background-image: linear-gradient(rgba(0, 242, 255, 0.03) 1px, transparent 1px), linear-gradient(90deg, rgba(0, 242, 255, 0.03) 1px, transparent 1px);
  background-size: 60rpx 60rpx;
  z-index: 0;
  pointer-events: none;
}

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

/* 扫描线动画略... (保持原样) */
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
</style>