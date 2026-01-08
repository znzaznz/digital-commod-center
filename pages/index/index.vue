<!-- pages/index/index.vue -->
<template>
  <!-- 监听 drawerStateChange 事件 -->
  <page-layout title="控制中心" @drawerStateChange="handleDrawerState">

    <!-- 核心：当抽屉打开时，隐藏 chart-box -->
    <!-- 这里用 v-show 或 :style 都可以，推荐用 opacity 防止图表重新渲染闪烁 -->
    <view class="chart-box" v-show="isChartVisible">
      <l-echart ref="chartRef"></l-echart>
    </view>

    <view class="stats-grid" :style="{ opacity: isChartVisible ? 1 : 0 }">
      <view class="stat-card" v-for="item in stats" :key="item.label">
        <text class="label">{{ item.label }}</text>
        <text class="value">{{ item.value }}</text>
      </view>
    </view>

  </page-layout>
</template>

<script setup>
import {ref, onMounted, onUnmounted} from 'vue'; // 1. 引入 onUnmounted
import * as echarts from '@/uni_modules/lime-echart/static/web/echarts.esm.min.js';

const chartRef = ref(null);
const isChartVisible = ref(true);
let myChart = null; // 2. 定义一个变量用来存放图表实例

// 处理抽屉状态
const handleDrawerState = (isOpen) => {
  isChartVisible.value = !isOpen;
};

const stats = [
  {label: 'Active Projects', value: '12'},
  {label: 'Resource Load', value: '98'},
  {label: 'Tasks Solved', value: '45'}
];

onMounted(async () => {
  // 建议加上一个小延迟，确保 canvas 节点准备好
  setTimeout(async () => {
    if (!chartRef.value) return;

    // 将实例赋值给全局变量 myChart
    myChart = await chartRef.value.init(echarts);

    const option = {
      backgroundColor: 'transparent',
      series: [{
        type: 'gauge',
        startAngle: 220, endAngle: -40, radius: '80%',
        progress: {show: true, width: 12, itemStyle: {color: '#00f2ff', shadowBlur: 15, shadowColor: '#00f2ff'}},
        axisLine: {lineStyle: {width: 12, color: [[1, 'rgba(0, 242, 255, 0.1)']]}},
        axisTick: {show: true, distance: -20, lineStyle: {color: '#00f2ff', width: 2}},
        splitLine: {show: false}, axisLabel: {show: false}, pointer: {show: false},
        detail: {valueAnimation: true, formatter: '{value}%', color: '#fff', fontSize: 50, fontWeight: 'bold'},
        data: [{value: 85}]
      }]
    };
    myChart.setOption(option);
  }, 300);
});

// 3. 核心步骤：页面退出时卸载图表
onUnmounted(() => {
  if (myChart) {
    myChart.dispose(); // 销毁 ECharts 实例，释放内存
    myChart = null;    // 清空变量引用
    console.log('ECharts 模块已成功卸载');
  }
});
</script>

<style>
/* 防止通配符报错，手动指定标签 */
page, view, text {
  box-sizing: border-box;
}

page {
  height: 100%;
  overflow: hidden;
  background-color: #050a0f;
}

.chart-box {
  width: 100%;
  height: 600rpx;
  transition: opacity 0.2s;
}

.stats-grid {
  display: flex;
  justify-content: space-around;
  margin-top: 50rpx;
  transition: opacity 0.2s;
}

.stat-card {
  background: rgba(0, 242, 255, 0.05);
  border: 1px solid rgba(0, 242, 255, 0.2);
  border-radius: 12rpx;
  padding: 30rpx;
  width: 30%;
  text-align: center;
}

.label {
  font-size: 20rpx;
  color: #88c;
  display: block;
  margin-bottom: 10rpx;
}

.value {
  font-size: 40rpx;
  color: #00f2ff;
  font-weight: bold;
  text-shadow: 0 0 10rpx #00f2ff;
}
</style>