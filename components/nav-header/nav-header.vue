<template>
  <view class="nav-fixed">
    <!-- 1. 状态栏占位 -->
    <view :style="{ height: statusBarHeight + 'px' }"></view>
    <!-- 2. 标题栏内容 -->
    <view class="title-bar" :style="{ height: titleBarHeight + 'px' }">
      <text class="nav-title">{{ title }}</text>
    </view>
  </view>
  <!-- 3. 占位块：防止下方内容钻到标题栏下面 -->
  <view :style="{ height: headerHeight + 'px' }"></view>
</template>

<script setup>
import {ref, onMounted} from 'vue';

defineProps({
  title: String
});

const statusBarHeight = ref(0);
const titleBarHeight = ref(0);
const headerHeight = ref(0);

onMounted(() => {
  const systemInfo = uni.getSystemInfoSync();
  statusBarHeight.value = systemInfo.statusBarHeight;

  // #ifdef MP-WEIXIN
  const menuButtonInfo = uni.getMenuButtonBoundingClientRect();
  titleBarHeight.value = (menuButtonInfo.top - statusBarHeight.value) * 2 + menuButtonInfo.height;
  // #endif

  // #ifndef MP-WEIXIN
  titleBarHeight.value = 44; // 非微信小程序默认44px
  // #endif

  headerHeight.value = statusBarHeight.value + titleBarHeight.value;
});
</script>

<style scoped>
.nav-fixed {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  z-index: 1000;
  background-color: #050a0f; /* 和你页面背景色一致 */
}

.title-bar {
  justify-content: center;
  display: flex;
  align-items: center;
  padding: 0 30rpx;
}

.nav-title {
  font-size: 36rpx; /* 你要的大字体 */
  font-weight: 900; /* 你要的加粗 */
  color: #00f2ff;
  text-shadow: 0 0 10rpx #00f2ff;
  letter-spacing: 0rpx;
}
</style>