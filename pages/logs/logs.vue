<template>
  <!-- 使用你之前创建的全局布局组件 -->
  <page-layout title="运行日志">
    <view class="log-container">
      <view class="log-item" v-for="(item, index) in logList" :key="index">
        <text class="time">[{{ item.time }}]</text>
        <text class="msg">{{ item.msg }}</text>
      </view>
    </view>
  </page-layout>
</template>

<script setup >
import { ref } from 'vue';

// 模拟一些日志数据
const logList = ref([
  { time: '15:20:01', msg: '系统核心启动成功...' },
  { time: '15:20:05', msg: '数据库连接已建立' },
  { time: '15:21:10', msg: '资源监控模块就绪' }
]);
</script>

<style scoped>
.log-container {
  padding: 30rpx;
  color: #00f2ff;
  font-family: monospace;
}
.log-item {
  margin-bottom: 20rpx;
  font-size: 26rpx;
  line-height: 1.5;
  border-left: 4rpx solid #00f2ff;
  padding-left: 20rpx;
  background: rgba(0, 242, 255, 0.05);
}
.time {
  margin-right: 20rpx;
  opacity: 0.7;
}
</style>