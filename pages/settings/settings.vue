<template>
  <page-layout title="VIP服务中心">
    <view class="vip-container">
      <!-- 1. 加急按钮：直接传 text 和高度 prop -->
      <view class="mb-40">
        <cyber-btn
            text="加急!!!"
            color="rgba(255,87,127,0.4)"
            height="220rpx"
        />
      </view>

      <!-- 2. 下方双格按钮 -->
      <view class="btn-grid">
        <cyber-btn
            color="rgba(0,242,255,0.4)"
            width="48%"
            height="260rpx"
            :shrink="false"
        >
          <view class="slot-content">
            <text class="iconfont icon-download" style="color: #00f2ff; font-size: 60rpx;"></text>
            <text class="btn-subtext">查看进度报告</text>
          </view>
        </cyber-btn>

        <cyber-btn
            color="rgba(0,242,255,0.4)"
            width="48%"
            height="260rpx"
            :shrink="false"
        >
          <view class="slot-content">
            <text class="iconfont icon-Call" style="color: #00f2ff; font-size: 60rpx;"></text>
            <text class="btn-subtext">联系部门主管</text>
          </view>
        </cyber-btn>
      </view>
    </view>
  </page-layout>
</template>

<style scoped>
.vip-container {
  padding: 40rpx;
}

.mb-40 {
  margin-bottom: 40rpx;
}

.btn-grid {
  display: flex;
  justify-content: space-between;
}

/* 核心：定义插槽内内容的布局 */
.slot-content {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

.btn-subtext {
  color: #fff;
  font-size: 26rpx;
  margin-top: 30rpx;
  font-weight: bold;
  letter-spacing: 2rpx;
  text-align: center;
}
</style>
<script setup lang="ts">
</script>