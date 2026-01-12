<!-- pages/index/center.vue -->
<template>
  <page-layout title="监控中心" @drawerStateChange="handleDrawerState" >

    <!-- 1. 顶部装饰 -->
    <view class="top-decoration">
      <view class="market-tag">
        <text class="dot-blink"></text>
        <text>今日完成度提升 +2.5%</text>
      </view>
    </view>

    <!-- 2. 图表区域 -->
    <view class="chart-box" v-show="isChartVisible">
      <view class="chart-title">项目总体完成度</view>
      <view class="chart">
        <gauge-chart :value="valueFromOutside" />
      </view>
    </view>

    <!-- 3. 统计网格 -->
    <view class="stats-grid" :style="{ opacity: isChartVisible ? 1 : 0 }">
      <view class="stat-card" v-for="(item, index) in stats" :key="item.label">
        <text class="label">{{ item.label }}</text>
        <text class="value">{{ item.value }}</text>
        <view class="dot-indicator">
          <view class="dot"></view>
          <view class="dot active"></view>
          <view class="dot"></view>
        </view>
      </view>
    </view>

    <!-- 4. 系统节点完成度 -->
    <view class="resource-section" :style="{ opacity: isChartVisible ? 1 : 0 }">
      <view class="section-header">
        <text class="title">各部分进度明细</text>
      </view>
      <view class="progress-item" v-for="node in nodes" :key="node.name">
        <view class="node-info">
          <text class="node-name">{{ node.name }}</text>
          <text class="node-percent">{{ node.percent }}%</text>
        </view>
        <view class="progress-bg">
          <view class="progress-bar" :style="{ width: node.percent + '%' }"></view>
        </view>
      </view>
    </view>

    <!-- 底部 HUD 区域 -->
    <view class="bottom-hud-spacer"></view>
    <view class="bottom-hud-bar">
      <view class="hud-glow-line"></view>
      <view class="hud-content">
        <view class="hud-group">
          <view class="hud-label">项目开始时间</view>
          <view class="hud-value-box">
            <text class="hud-value">{{ hudInfo.year }}</text>
            <text class="hud-unit">年</text>
            <text class="hud-value">{{ hudInfo.month }}</text>
            <text class="hud-unit">月</text>
            <text class="hud-value">{{ hudInfo.day }}</text>
            <text class="hud-unit">日</text>
          </view>
        </view>
        <view class="status-indicator">
          <view class="pulse-dot"></view>
          <text>{{ hudInfo.status }}</text>
        </view>
      </view>
    </view>

  </page-layout>
</template>

<script setup>
import {ref, reactive} from 'vue';
import GaugeChart from "@/components/charts/gauge-chart/gauge-chart.vue";

const isChartVisible = ref(true);
const valueFromOutside = ref(85);

// 1. 定义底部 HUD 的数据对象
const hudInfo = reactive({
  year: '2026',
  month: '01',
  day: '04',
  status: '开发中'
});


const handleDrawerState = (isOpen) => {
  isChartVisible.value = !isOpen;
};

const stats = [
  {label: '活跃度', value: '12'},
  {label: '已解决', value: '45'},
  {label: '待处理', value: '98'},
];

const nodes = [
  {name: '前端架构', percent: 78},
  {name: '后端逻辑', percent: 42},
  {name: '数据接口', percent: 60},
  {name: '数据进度', percent: 60},
];
</script>

<style>
/* --- 基础设置 --- */
page {
  background-color: #03080c;
  overflow-y: auto;
}

/* --- 1. 顶部装饰 (紧凑型) --- */
.top-decoration {
  padding: 20rpx 40rpx; /* 缩小上下边距 */
  padding-bottom: 30rpx;
}

.market-tag {
  display: inline-flex;
  align-items: center;
  background: rgba(0, 242, 255, 0.1);
  border: 1px solid rgba(0, 242, 255, 0.4);
  padding: 12rpx 28rpx;
  border-radius: 6rpx;
  color: #00f2ff;
  font-size: 26rpx; /* 字号加大 */
  font-weight: bold;
}

.dot-blink {
  width: 12rpx;
  height: 12rpx;
  background: #00f2ff;
  border-radius: 50%;
  margin-right: 18rpx;
  box-shadow: 0 0 15rpx #00f2ff;
  animation: blink 2s infinite;
}

/* --- 2. 图表 (紧凑型) --- */
.chart-box {
  width: 100%;
}

.chart {
  margin: 20rpx 0;
  height: 420rpx; /* 缩小高度 */
}

.chart-title {
  color: #00f2ff;
  font-weight: 900;
  text-align: center;
  font-size: 38rpx; /* 字号加大 */
  letter-spacing: 14rpx;
  text-shadow: 0 0 20rpx rgba(0, 242, 255, 0.5);
}

/* --- 3. 统计卡片 (文字加强) --- */
.stats-grid {
  display: flex;
  justify-content: space-between;
  padding: 0 30rpx;
  margin-top: 10rpx;
}

.stat-card {
  width: 31%;
  background: rgba(13, 27, 42, 0.7);
  border-top: 3px solid rgba(0, 242, 255, 0.6);
  padding: 20rpx 10rpx; /* 缩小内边距 */
  border-radius: 12rpx;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.label {
  color: #9ec5cc;
  font-size: 24rpx; /* 字号加大 */
  margin-bottom: 8rpx;
  font-weight: bold;
}

.value {
  color: #fff;
  font-size: 54rpx; /* 字号加大 */
  font-weight: 900;
  text-shadow: 0 0 20rpx rgba(0, 242, 255, 0.8);
}

/* --- 4. 进度条模块 (更紧凑) --- */
.resource-section {
  margin: 40rpx 30rpx 0; /* 缩小顶部间距 */
  padding: 30rpx;
  background: rgba(0, 242, 255, 0.05);
  border-radius: 16rpx;
  border: 1px solid rgba(0, 242, 255, 0.15);
}

.section-header .title {
  color: #00f2ff;
  font-size: 30rpx; /* 字号加大 */
  font-weight: bold;
  letter-spacing: 4rpx;
}

.progress-item {
  margin-top: 25rpx;
}

.node-info {
  display: flex;
  justify-content: space-between;
  margin-bottom: 10rpx;
}

.node-name {
  color: #c0e6ed;
  font-size: 28rpx; /* 字号加大 */
  font-weight: bold;
}

.node-percent {
  color: #00f2ff;
  font-size: 28rpx;
  font-weight: bold;
}

.progress-bg {
  height: 12rpx; /* 进度条加粗 */
  background: rgba(255, 255, 255, 0.08);
  border-radius: 8rpx;
}

.progress-bar {
  height: 100%;
  background-color: #00f2ff;
  border-radius: 8rpx;
}

/* --- 5. 底部 HUD (大字体) --- */
.bottom-hud-spacer {
  height: 200rpx;
}

.bottom-hud-bar {
  position: fixed;
  bottom: 40rpx;
  left: 20rpx;
  right: 20rpx;
  height: 140rpx; /* 稍微增高以容纳大字 */
  background: linear-gradient(180deg, rgba(12, 38, 58, 0.95) 0%, rgba(5, 15, 25, 0.98) 100%);
  border-radius: 20rpx;
  border: 1px solid rgba(0, 242, 255, 0.4);
  box-shadow: 0 -10rpx 40rpx rgba(0, 0, 0, 0.7);
  overflow: hidden;
  backdrop-filter: blur(15px);
  z-index: 100;
  margin: 0 20rpx;
}

.hud-content {
  display: flex;
  justify-content: space-between;
  align-items: center;
  height: 100%;
  padding: 0 40rpx;
}

.hud-label {
  color: rgba(0, 242, 255, 0.5);
  font-size: 22rpx; /* 加大标签字号 */
  letter-spacing: 2rpx;
  margin-bottom: 10rpx;
  font-weight: bold;
}

.hud-value {
  color: #00f2ff;
  font-size: 46rpx; /* 数值加大 */
  font-family: 'Courier New', monospace;
  font-weight: 900;
  text-shadow: 0 0 15rpx rgba(0, 242, 255, 0.6);
}

.hud-unit {
  color: rgba(0, 242, 255, 0.35);
  font-size: 18rpx;
  margin: 0 12rpx;
}

.status-indicator {
  padding: 12rpx 24rpx;
  background: rgba(0, 242, 255, 0.12);
  border: 1px solid rgba(0, 242, 255, 0.3);
  border-radius: 8rpx;
}

.status-indicator text {
  color: #00f2ff;
  font-size: 24rpx; /* 字号加大 */
  font-weight: 900;
}

/* --- 动画效果 --- */
@keyframes blink {
  0%, 100% {
    opacity: 1;
  }
  50% {
    opacity: 0.3;
  }
}

@keyframes scan-line {
  0% {
    transform: translateX(-100%);
  }
  100% {
    transform: translateX(100%);
  }
}

@keyframes pulse {
  0% {
    transform: scale(1);
    opacity: 1;
  }
  50% {
    transform: scale(1.5);
    opacity: 0.5;
  }
  100% {
    transform: scale(1);
    opacity: 1;
  }
}

.hud-glow-line {
  position: absolute;
  top: 0;
  height: 4rpx;
  width: 100%;
  background: linear-gradient(90deg, transparent, rgba(0, 242, 255, 0.8), transparent);
  animation: scan-line 3s infinite linear;
}

.pulse-dot {
  width: 14rpx;
  height: 14rpx;
  background: #00f2ff;
  border-radius: 50%;
  margin-right: 15rpx;
  display: inline-block;
  animation: pulse 1.5s infinite;
}
</style>