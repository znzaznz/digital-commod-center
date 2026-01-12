<!-- components/CyberBtn.vue -->
<template>
  <view
      class="rim-btn"
      hover-class="btn-active"
      :style="{
      '--ui-color': color,
      'width': width,
      'height': height
    }"
      @tap="$emit('click')"
  >
    <!-- 四条边框流光 -->
    <view class="border-track" v-if="shrink">
      <view class="line top"></view>
      <view class="line right"></view>
      <view class="line bottom"></view>
      <view class="line left"></view>
    </view>

    <!-- 按钮主体 -->
    <view class="glass-btn">
      <view class="glass-reflect"></view>

      <!-- 核心修改：增加插槽，如果没有插槽内容则显示 text 属性 -->
      <slot>
        <text class="btn-text">{{ text }}</text>
      </slot>
    </view>
  </view>
</template>

<script>
export default {
  // 核心配置：开启虚拟化节点，让组件标签本身不占用布局空间
  options: {
    virtualHost: true
  }
}
</script>

<script setup>
defineProps({
  text: {type: String, default: ''},
  width: {type: String, default: '100%'},
  height: {type: String, default: '140rpx'},
  color: {type: String, default: 'rgba(255,87,127,0.4)'},
  shrink: {type: Boolean, default: true},
});
</script>

<style scoped>
.rim-btn {
  width: 100%;
  height: 100%;
  transition: all 0.2s;
  position: relative;
  background: rgba(5, 15, 25, 0.95);
  overflow: hidden;
  border-radius: 20rpx;
  box-sizing: border-box;
}

.glass-btn {
  border-radius: 20rpx;
  width: 100%;
  height: 100%;
  position: relative;
  backdrop-filter: blur(15px);
  display: flex;
  /* 关键：改为 column，让插槽里的图标和文字上下排 */
  flex-direction: column;
  align-items: center;
  justify-content: center;
  overflow: hidden;
  border: 4rpx solid var(--ui-color);
  box-sizing: border-box;
  padding: 20rpx;
}

.glass-reflect {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  height: 45%;
  background: linear-gradient(180deg, rgba(255, 255, 255, 0.1) 0%, transparent 100%);
  pointer-events: none;
}

.btn-text {
  color: #FFFFFF;
  font-size: 44rpx;
  font-weight: bold;
  letter-spacing: 4rpx;
  z-index: 2;
}

/* --- 流光逻辑优化 --- */
.line {
  position: absolute;
  /* 重点：让流光的颜色也跟随变量，或者保持白色高亮 */
  background: linear-gradient(90deg, transparent, #fff, transparent);
  z-index: 10;
  opacity: 0.6;
}

.line.top {
  top: 0;
  left: -100%;
  width: 100%;
  height: 4rpx;
  animation: scan-top 3s linear infinite;
}

.line.right {
  top: -100%;
  right: 0;
  width: 4rpx;
  height: 100%;
  background: linear-gradient(180deg, transparent, #fff, transparent);
  animation: scan-right 3s linear infinite;
  animation-delay: 0.75s;
}

.line.bottom {
  bottom: 0;
  right: -100%;
  width: 100%;
  height: 4rpx;
  animation: scan-bottom 3s linear infinite;
  animation-delay: 1.5s;
}

.line.left {
  bottom: -100%;
  left: 0;
  width: 4rpx;
  height: 100%;
  background: linear-gradient(0deg, transparent, #fff, transparent);
  animation: scan-left 3s linear infinite;
  animation-delay: 2.25s;
}

@keyframes scan-top {
  0% {
    left: -100%;
  }
  25%, 100% {
    left: 100%;
  }
}

@keyframes scan-right {
  0% {
    top: -100%;
  }
  25%, 100% {
    top: 100%;
  }
}

@keyframes scan-bottom {
  0% {
    right: -100%;
  }
  25%, 100% {
    right: 100%;
  }
}

@keyframes scan-left {
  0% {
    bottom: -100%;
  }
  25%, 100% {
    bottom: 100%;
  }
}

.btn-active {
  transform: scale(0.96);
  opacity: 0.8;
}
</style>