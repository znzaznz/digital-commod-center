<!-- pages/boot/boot.vue -->
<template>
  <view class="boot-container">
    <view class="hud-box">
      <!-- 装饰边角 -->
      <view class="corner tl"></view><view class="corner tr"></view>
      <view class="corner bl"></view><view class="corner br"></view>

      <!-- 核心扫描环 -->
      <view class="loader">
        <view class="scan-ring"></view>
        <view class="data-flow"></view>
        <text class="num">{{ percent }}%</text>
      </view>

      <!-- 系统诊断日志 -->
      <view class="console">
        <view v-for="(log, i) in logs" :key="i" class="line">
          >> {{ log }}
        </view>
        <view class="cursor">_</view>
      </view>

      <view class="footer">COCKPIT OS v1.0.4 INITIALIZING</view>
    </view>
  </view>
</template>

<script setup>
import { ref, onMounted } from 'vue';

const percent = ref(0);
const logs = ref([]);
const messages = [
  "CORE MODULE CHECKING...",
  "GRID SYSTEM ONLINE",
  "NEURAL LINK ESTABLISHED",
  "DATABASE SYNCING...",
  "GUI RENDERER READY"
];

onMounted(() => {
  let i = 0;
  const timer = setInterval(() => {
    percent.value += Math.floor(Math.random() * 8) + 2;

    if (percent.value % 20 < 10 && i < messages.length) {
      logs.value.push(messages[i++]);
    }

    if (percent.value >= 100) {
      percent.value = 100;
      clearInterval(timer);

      // 载入完成，直接切换到首页
      setTimeout(() => {
        uni.redirectTo({
          url: '/pages/center/center'
        });
      }, 600);
    }
  }, 80);
});
</script>

<style scoped>
.boot-container {
  width: 100vw; height: 100vh; background-color: #050a0f;
  display: flex; justify-content: center; align-items: center; color: #00f2ff;
}
.hud-box {
  width: 85%; height: 70vh; border: 1px solid rgba(0, 242, 255, 0.2);
  position: relative; padding: 40rpx; display: flex; flex-direction: column; align-items: center;
}
.loader {
  width: 300rpx; height: 300rpx; border-radius: 50%; border: 2rpx solid rgba(0, 242, 255, 0.1);
  display: flex; justify-content: center; align-items: center; margin-top: 80rpx; position: relative;
}
.scan-ring {
  position: absolute; width: 100%; height: 100%; border: 4rpx solid transparent;
  border-top-color: #00f2ff; border-radius: 50%; animation: rotate 1s linear infinite;
}
@keyframes rotate { from { transform: rotate(0deg); } to { transform: rotate(360deg); } }
.num { font-size: 50rpx; font-weight: bold; font-family: 'Courier New', monospace; text-shadow: 0 0 15rpx #00f2ff; }
.console { width: 100%; margin-top: 100rpx; font-family: monospace; font-size: 20rpx; line-height: 1.6; }
.cursor { display: inline-block; animation: blink 0.8s infinite; }
@keyframes blink { 0%, 100% { opacity: 1; } 50% { opacity: 0; } }
.corner { position: absolute; width: 30rpx; height: 30rpx; border: 4rpx solid #00f2ff; }
.tl { top: -2px; left: -2px; border-right: none; border-bottom: none; }
.tr { top: -2px; right: -2px; border-left: none; border-bottom: none; }
.bl { bottom: -2px; left: -2px; border-right: none; border-top: none; }
.br { bottom: -2px; right: -2px; border-left: none; border-top: none; }
.footer { position: absolute; bottom: 30rpx; font-size: 18rpx; opacity: 0.3; letter-spacing: 4rpx; }
</style>