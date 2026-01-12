<template>
  <page-layout title="项目时间轴" class="scroll-container">
    <view class="timeline-container">
      <!-- 1. 中心垂直轴线 -->
      <view class="vertical-line">
        <view class="line-glow"></view>
      </view>

      <!-- 2. 时间轴列表 -->
      <view class="timeline-list">
        <view
            v-for="(item, index) in timelineData"
            :key="index"
            class="timeline-item"
            :class="[item.side, item.status]"
        >
          <!-- 任务卡片 -->
          <view class="task-card">
            <view class="card-content">
              <!-- 图标区域 -->
              <view class="icon-box">
                <text v-if="item.status === 'completed'" class="iconfont status-done">✔</text>
                <text v-if="item.status === 'error'" class="iconfont status-error error-anim">✖</text>
                <text v-if="item.status === 'active'" class="iconfont hammer-animation">🔨</text>
                <text v-if="item.status === 'pending'" class="iconfont hourglass-animation">⏳</text>
              </view>

              <!-- 文字区域 -->
              <view class="text-box">
                <text class="title">{{ item.title }}</text>
                <text class="subtext" v-if="item.subtext">{{ item.subtext }}</text>
              </view>

              <!-- 警告图标 (仅状态为active时显示) -->
              <view v-if="item.status === 'error' || item.status === 'pending'" class="warning-tag">
                <text class="warning-icon">⚠</text>
              </view>
            </view>

            <!-- 卡片边框装饰流光 -->
            <view class="card-border-glow"></view>
          </view>

          <!-- 中心圆点 -->
          <view class="node-dot" v-if="index !== timelineData.length - 1">
            <view class="dot-inner"></view>
            <view class="dot-ring-small"></view>
            <view class="dot-ring-big"></view>
          </view>
        </view>
      </view>
    </view>

    <view class="header-indicator" v-if="hasProblem">
      <view class="dot-track">
        <view v-for="i in 6" :key="i" :class="['scan-dot', 'dot-' + i]"></view>
      </view>
    </view>

    <view class="huge-warning-bg" v-else>
      <view class="warning-triangle">
        <text class="exclamation">!</text>
      </view>
    </view>
  </page-layout>
</template>

<script setup>
import {ref} from 'vue';

const hasProblem = ref(true)

const timelineData = ref([
  {
    title: '需求确认',
    subtext: '确定要做的需求',
    status: 'completed',
    side: 'right',
  },
  {
    title: '核心开发阶段',
    subtext: '进入了开发的阶段',
    status: 'error',
    side: 'left',
  },
  {
    title: '数据集成 (当前)',
    subtext: '集成了当前要做的数据',
    status: 'pending',
    side: 'right',
  },
  {
    title: '系统测试',
    subtext: '目前正在测试中',
    status: 'active',
    side: 'left',
  }
]);
</script>

<style scoped>
/* ==========================================================================
   1. 整体容器布局
   ========================================================================== */
.timeline-container {
  position: relative;
  padding: 60rpx 0;
}

/* 时间轴条目基础排版 */
.timeline-item {
  position: relative;
  display: flex;
  width: 100%;
  margin-bottom: 150rpx;
  align-items: center;
}

.timeline-item:last-child {
  margin-bottom: 0;
}

/* 左右布局控制 */
.timeline-item.right {
  justify-content: flex-end;
  padding-left: 25%;
  padding-right: 5%;
}

.timeline-item.left {
  justify-content: flex-start;
  padding-right: 25%;
  padding-left: 5%;
}

/* ==========================================================================
   2. 顶部指示灯 (流水灯效果)
   ========================================================================== */
.header-indicator {
  display: flex;
  justify-content: center;
  width: 100%;
  padding: 50rpx 0;
  z-index: 10;
}

.dot-track {
  display: flex;
  gap: 25rpx;
}

.scan-dot {
  width: 25rpx;
  height: 25rpx;
  background: rgba(0, 242, 255, 0.2);
  border-radius: 50%;
  position: relative;
}

.scan-dot::after {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: #00f2ff;
  border-radius: 50%;
  box-shadow: 0 0 15rpx #00f2ff;
  opacity: 0;
  animation: dot-glow 1.8s infinite;
}

/* 流水灯序列延迟 */
.dot-1::after {
  animation-delay: 0s;
}

.dot-2::after {
  animation-delay: 0.2s;
}

.dot-3::after {
  animation-delay: 0.4s;
}

.dot-4::after {
  animation-delay: 0.6s;
}

.dot-5::after {
  animation-delay: 0.8s;
}

.dot-6::after {
  animation-delay: 1.0s;
}

/* ==========================================================================
   3. 中心轴线与节点
   ========================================================================== */
/* 垂直线 */
.vertical-line {
  position: absolute;
  left: 50%;
  top: 0;
  bottom: 0;
  width: 2rpx;
  background: rgba(0, 242, 255, 0.2);
  transform: translateX(-50%);
}

.line-glow {
  width: 100%;
  height: 100%;
  background: linear-gradient(180deg, transparent, rgba(0, 242, 255, 0.8) 20%, rgba(0, 242, 255, 0.8) 80%, transparent);
  box-shadow: 0 0 15rpx rgba(0, 242, 255, 0.5);
}

/* 节点圆点 */
.node-dot {
  position: absolute;
  bottom: -80rpx;
  left: 50%;
  transform: translateX(-50%);
  z-index: 10;
}

.dot-inner {
  width: 25rpx;
  height: 25rpx;
  background: #58E2C8;
  border-radius: 50%;
}

.dot-ring-small {
  position: absolute;
  top: 50%;
  left: 50%;
  width: 35rpx;
  height: 35rpx;
  border: 4rpx solid rgba(88, 226, 200, 0.5);
  border-radius: 50%;
  transform: translate(-50%, -50%);
  animation: pulse 3s infinite;
}

.dot-ring-big {
  position: absolute;
  top: 50%;
  left: 50%;
  width: 45rpx;
  height: 45rpx;
  border: 4rpx solid rgba(88, 226, 200, 0.5);
  border-radius: 50%;
  transform: translate(-50%, -50%);
  animation: pulse 2s infinite;
}

/* ==========================================================================
   4. 任务卡片样式
   ========================================================================== */
.task-card {
  position: relative;
  width: 100%;
  background: rgba(13, 38, 58);
  backdrop-filter: blur(10px);
  border: 1px solid rgba(0, 242, 255, 0.3);
  border-radius: 20rpx;
  padding: 30rpx;
  transition: all 0.3s;
}

/* 激活态高亮 */
.active .task-card {
  border-color: #00f2ff;
  box-shadow: 0 0 30rpx rgba(0, 242, 255, 0.5);
  background: rgb(39, 82, 85);
}

/* 卡片内容布局 */
.card-content {
  display: flex;
  align-items: center;
}

.icon-box {
  width: 70rpx;
  height: 70rpx;
  background: rgba(0, 242, 255, 0.1);
  border-radius: 12rpx;
  display: flex;
  justify-content: center;
  align-items: center;
  margin-right: 20rpx;
  color: #00f2ff;
  font-size: 40rpx;
}

.text-box {
  flex: 1;
}

.title {
  color: #fff;
  font-size: 30rpx;
  font-weight: bold;
  display: block;
}

.subtext {
  color: rgba(0, 242, 255, 0.6);
  font-size: 22rpx;
  display: block;
}

/* ==========================================================================
   5. 警告反馈系统
   ========================================================================== */
.warning-tag {
  color: #ff9800;
  font-size: 36rpx;
  text-shadow: 0 0 10rpx #ff9800;
}

.huge-warning-bg {
  display: flex;
  justify-content: center;
  align-items: center;
  pointer-events: none;
  opacity: 1;
}

.warning-triangle {
  width: 180rpx;
  height: 160rpx;
  border: 8rpx solid #ff7e00;
  border-radius: 10rpx;
  display: flex;
  justify-content: center;
  align-items: center;
  box-shadow: 0 0 40rpx rgba(255, 126, 0, 0.4);
  background: rgba(255, 126, 0);
  clip-path: polygon(50% 0%, 0% 100%, 100% 100%);
}

.exclamation {
  color: white;
  font-size: 80rpx;
  font-weight: 900;
  margin-top: 40rpx;
}

/* ==========================================================================
   6. 动画库 (Keyframes)
   ========================================================================== */
/* 流水灯闪烁 */
@keyframes dot-glow {
  0%, 20% {
    opacity: 0;
    transform: scale(1);
  }
  10% {
    opacity: 1;
    transform: scale(1.2);
  }
}

/* 节点光圈扩散 */
@keyframes pulse {
  0% {
    transform: translate(-50%, -50%) scale(1);
    opacity: 0.8;
  }
  100% {
    transform: translate(-50%, -50%) scale(1.5);
    opacity: 0;
  }
}

/* 旋转动画 */
.scan-animation {
  animation: rotate 3s infinite linear;
}

@keyframes rotate {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(360deg);
  }
}

.hammer-animation {
  display: inline-block;
  /* 调整动画时长为 1.2s 让动作更清晰，使用 cubic-bezier 增加爆发感 */
  animation: hammer-hit 1s cubic-bezier(0.45, 0.05, 0.55, 0.95) infinite;
  /* 关键：旋转中心点。对于 🔨 符号，右下角通常是手柄末端 */
  transform-origin: bottom right;
  font-size: 44rpx;
}

@keyframes hammer-hit {
  0% {
    transform: rotate(0deg);
  }
  /* 1. 缓慢蓄力向后扬 */
  30% {
    transform: rotate(-25deg);
  }
  /* 2. 快速有力地敲下（时间占比小，速度快） */
  45% {
    transform: rotate(15deg);
  }
  /* 3. 撞击后的微小反弹震动 */
  52% {
    transform: rotate(15deg);
  }
  /* 4. 再次触底（确认感） */
  60% {
    transform: rotate(15deg);
  }
  /* 5. 平滑回到初始位置，准备下一次循环 */
  100% {
    transform: rotate(0deg);
  }
}

.hourglass-animation {
  display: inline-block;
  /* 2.5秒一个周期，可以根据需要调快或调慢 */
  animation: hourglass-flip 2.5s ease-in-out infinite;
  color: rgba(0, 242, 255, 0.7); /* 给等待状态一个稍微淡一点的科技蓝 */
  font-size: 38rpx;
}

@keyframes hourglass-flip {
  0% {
    transform: rotate(0deg);
  }
  /* 0% - 15%：快速翻转 180 度 */
  15% {
    transform: rotate(180deg);
  }
  /* 15% - 50%：停顿（模拟沙子流下） */
  50% {
    transform: rotate(180deg);
  }
  /* 50% - 65%：再次快速翻转到 360 度（回到原点） */
  65% {
    transform: rotate(360deg);
  }
  /* 65% - 100%：再次停顿 */
  100% {
    transform: rotate(360deg);
  }
}


.status-done {
  color: #00fd00;
}

.status-error {
  color: red;
}

.success-anim {
  display: inline-block;
  /* 0.6s 弹出动画 + 2s 的循环呼吸效果 */
  animation: success-pop 0.6s cubic-bezier(0.175, 0.885, 0.32, 1.275),
  success-glow 2s ease-in-out infinite;
  color: #58E2C8;
}

@keyframes success-pop {
  0% {
    transform: scale(0);
    opacity: 0;
  }
  100% {
    transform: scale(1);
    opacity: 1;
  }
}

@keyframes success-glow {
  0%, 100% {
    text-shadow: 0 0 10rpx rgba(88, 226, 200, 0.5);
  }
  50% {
    text-shadow: 0 0 25rpx rgba(88, 226, 200, 0.9);
    transform: scale(1.1); /* 轻微放大，显得有生命力 */
  }
}

/* ==========================================================================
   叉 (✖) 的动画：错误抖动 + 故障风闪烁
   ========================================================================== */
.error-anim {
  display: inline-block;
  /* 0.5s 抖动一次，每隔 2s 触发一次 */
  animation: error-shake 2s infinite;
  color: #ff4d4f;
}

@keyframes error-shake {
  /* 在前 25% 的时间内完成抖动，后面静止，增加节奏感 */
  0%, 25%, 100% {
    transform: translateX(0);
    text-shadow: 0 0 10rpx rgba(255, 77, 79, 0.5);
  }
  2.5%, 7.5%, 12.5%, 17.5%, 22.5% {
    transform: translateX(-4rpx);
    opacity: 0.8;
  }
  5%, 10%, 15%, 20% {
    transform: translateX(4rpx);
    opacity: 1;
    text-shadow: 0 0 20rpx rgba(255, 77, 79, 0.8);
  }
}
</style>