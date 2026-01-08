<!-- components/page-layout/page-layout.vue -->
<template>
  <view class="page-container">
    <!-- 全局背景格栅装饰 -->
    <view class="bg-decoration"></view>

    <!-- 1. 扫描线加载遮罩 (仅在跳转时显示) -->
    <view class="loading-overlay" v-if="isNavigating">
      <view class="scan-box">
        <view class="scan-line"></view>
      </view>
      <text class="loading-text">指挥仓切换中...</text>
    </view>

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

    <!-- 4. 抽屉内容主体 -->
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

    <!-- 页面顶部占位 -->
    <view :style="{ height: headerHeight + 'px' }"></view>

    <!-- 5. 页面内容插槽 (带有入场动画) -->
    <!-- 注意：只有当状态允许时，才渲染内容，防止 ECharts 穿透 -->
    <view class="page-content">
      <slot></slot>
    </view>
  </view>
</template>

<script setup>
import { ref, onMounted, computed, watch } from 'vue';

const props = defineProps({
  title: { type: String, default: '控制中心' }
});

// 定义事件：contentVisible 用于通知内层 ECharts 的存亡
const emit = defineEmits(['drawerStateChange', 'contentVisible']);

const isDrawerOpen = ref(false);    // 抽屉状态
const isNavigating = ref(false);   // 页面跳转状态
const statusBarHeight = ref(0);
const titleBarHeight = ref(0);
const headerHeight = ref(0);
const currentPath = ref('');

// --- 核心逻辑整合 ---

/**
 * 统一的显示闸门：
 * 只要 “抽屉开了” 或者 “正在跳转”
 * 主内容区域的复杂组件（ECharts）就应该被销毁
 */
const isMainContentVisible = computed(() => {
  return !isDrawerOpen.value && !isNavigating.value;
});

// 监听这个“闸门”，一旦变化就通知父组件
watch(isMainContentVisible, (newVal) => {
  emit('contentVisible', newVal);
}, { immediate: true });

// --- 功能函数 ---

const toggleDrawer = (status) => {
  isDrawerOpen.value = status;
  emit('drawerStateChange', status);
};

const menuList = [
  { name: '控制台主页', path: '/pages/index/index' },
  { name: '运行日志', path: '/pages/logs/logs' },
  { name: '资源监控', path: '/pages/monitor/monitor' },
  { name: '系统设置', path: '/pages/settings/settings' }
];

const navTo = (url) => {
  if (url === currentPath.value) {
    toggleDrawer(false);
    return;
  }

  // 1. 先关抽屉
  isDrawerOpen.value = false;
  // 2. 开启扫描动画（此时 isMainContentVisible 会自动变为 false，通知内层销毁 ECharts）
  isNavigating.value = true;

  setTimeout(() => {
    uni.redirectTo({
      url,
      complete: () => {
        isNavigating.value = false;
      }
    });
  }, 450); // 仪式感延迟
};

onMounted(() => {
  // 获取系统状态栏和胶囊按钮高度，适配不同手机
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
.page-container {
  min-height: 100vh;
  background-color: #050a0f;
  position: relative;
  overflow: hidden;
}

/* --- 页面淡入动画 --- */
@keyframes pageFadeIn {
  from { opacity: 0; transform: translateY(30rpx); filter: blur(10px); }
  to { opacity: 1; transform: translateY(0); filter: blur(0); }
}
.page-content {
  animation: pageFadeIn 0.6s cubic-bezier(0.16, 1, 0.3, 1) forwards;
  padding: 20rpx;
  position: relative;
  z-index: 1;
}

/* --- 扫描线加载遮罩 --- */
.loading-overlay {
  position: fixed; top: 0; left: 0; width: 100vw; height: 100vh;
  background-color: rgba(5, 10, 15, 0.98);
  z-index: 10000;
  display: flex; flex-direction: column; justify-content: center; align-items: center;
}
.scan-box {
  width: 400rpx; height: 200rpx; border: 1px solid rgba(0, 242, 255, 0.2);
  position: relative; overflow: hidden;
}
.scan-line {
  width: 100%; height: 4rpx; background: #00f2ff;
  box-shadow: 0 0 20rpx #00f2ff; position: absolute; top: 0;
  animation: scanning 0.8s linear infinite;
}
@keyframes scanning {
  0% { top: 0%; opacity: 0; }
  50% { opacity: 1; }
  100% { top: 100%; opacity: 0; }
}
.loading-text {
  margin-top: 40rpx; color: #00f2ff; font-size: 22rpx; letter-spacing: 8rpx;
  text-shadow: 0 0 10rpx #00f2ff; font-family: monospace;
}

/* --- 导航栏 --- */
.nav-fixed { position: fixed; top: 0; left: 0; width: 100%; z-index: 1000; background-color: #050a0f; }
.title-bar { display: flex; align-items: center; justify-content: center; position: relative; }
.nav-title { font-size: 34rpx; font-weight: 900; color: #00f2ff; text-shadow: 0 0 10rpx #00f2ff; }
.hamburger-btn { position: absolute; left: 30rpx; width: 40rpx; height: 28rpx; display: flex; flex-direction: column; justify-content: space-between; }
.line { width: 100%; height: 3rpx; background: #00f2ff; box-shadow: 0 0 8rpx #00f2ff; }

/* --- 抽屉与遮罩 --- */
.drawer-mask {
  position: fixed; top: 0; left: 0; width: 100vw; height: 100vh;
  background: rgba(0, 0, 0, 0); transition: all 0.4s ease;
  z-index: 9998; pointer-events: none;
}
.mask-active { background: rgba(0, 0, 0, 0.8); backdrop-filter: blur(8px); pointer-events: auto; }
.drawer-content {
  position: fixed; top: 0; left: 0; width: 65%; height: 100%;
  background: #050a0f; z-index: 9999; transform: translateX(-105%);
  transition: transform 0.4s cubic-bezier(0.16, 1, 0.3, 1);
  padding: 120rpx 0; border-right: 1px solid rgba(0, 242, 255, 0.1);
}
.drawer-active { transform: translateX(0); }
.drawer-header { color: #00f2ff; font-weight: bold; padding: 0 40rpx 40rpx; font-size: 34rpx; }
.menu-item {
  display: flex; align-items: center; color: rgba(255, 255, 255, 0.4);
  padding: 35rpx 50rpx; font-size: 30rpx; transition: all 0.3s;
}
.active-item {
  color: #fff !important; font-weight: bold;
  background: linear-gradient(90deg, rgba(0, 242, 255, 0.1) 0%, transparent 100%);
}
.menu-icon { margin-right: 20rpx; color: inherit; }
.active-item .menu-icon { color: #00f2ff; }
.drawer-footer { position: absolute; bottom: 40rpx; left: 40rpx; color: #1a2a3a; font-size: 18rpx; }

/* 背景格栅 */
.bg-decoration {
  position: fixed; top: 0; left: 0; width: 100vw; height: 100vh;
  background-image: linear-gradient(rgba(0, 242, 255, 0.03) 1px, transparent 1px),
  linear-gradient(90deg, rgba(0, 242, 255, 0.03) 1px, transparent 1px);
  background-size: 60rpx 60rpx; z-index: 0; pointer-events: none;
}
</style>