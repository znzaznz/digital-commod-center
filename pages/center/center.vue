<!-- pages/index/center.vue -->
<template>
  <!-- 监听 drawerStateChange 事件 -->
  <page-layout title="控制中心" @drawerStateChange="handleDrawerState">

    <!-- 核心：当抽屉打开时，隐藏 chart-box -->
    <!-- 这里用 v-show 或 :style 都可以，推荐用 opacity 防止图表重新渲染闪烁 -->
    <view class="chart-box" v-show="isChartVisible">
      <view class="chart-title">xxx项目进度</view>
      <view class="chart">
        <l-echart ref="chartRef"></l-echart>
      </view>
    </view>

    <view class="stats-grid" :style="{ opacity: isChartVisible ? 1 : 0 }">
      <view class="stat-card" v-for="(item, index) in stats" :key="item.label">
        <text class="label">{{ item.label }}:</text>
        <text class="value">{{ item.value }}</text>

        <!-- 新增：底部的三个装饰点 -->
        <view class="dot-indicator">
          <view class="dot"></view>
          <view class="dot active"></view>
          <view class="dot"></view>
        </view>
      </view>
    </view>

  </page-layout>
</template>

<script setup>
import {ref, onMounted, onUnmounted} from 'vue'; // 1. 引入 onUnmounted
import * as echarts from '@/uni_modules/lime-echart/static/web/echarts.esm.min.js';

const chartRef = ref(null);
const isChartVisible = ref(true);
const valueFromOutside = ref(85); // 这是你从外部拿到的变量
let myChart = null; // 2. 定义一个变量用来存放图表实例

// 处理抽屉状态
const handleDrawerState = (isOpen) => {
  isChartVisible.value = !isOpen;
};

const stats = [
  {label: '项目活跃度', value: '12'},
  {label: '任务解决', value: '45'},
  {label: '待决问题', value: '98'},
];

onMounted(async () => {
  // 建议加上一个小延迟，确保 canvas 节点准备好
  setTimeout(async () => {
    if (!chartRef.value) return;

    // 将实例赋值给全局变量 myChart
    myChart = await chartRef.value.init(echarts);

    const option = {
      backgroundColor: 'transparent',
      series: [
        // 1. 最外层：超级光晕（利用超大 shadowBlur 实现你说的“老大光晕”）
        {
          type: 'gauge',
          radius: '78%',
          startAngle: 360,
          endAngle: 0,
          axisLine: {
            lineStyle: {
              color: [[1, 'rgb(0, 255, 255)']],
              width: 10, // 这里加宽到 40，光晕会往圆圈内部伸展很多
              shadowBlur: 40, // 扩散范围加大
              shadowColor: 'rgb(0,178,255)'
            }
          },
          splitLine: {show: false},
          axisTick: {show: false},
          axisLabel: {show: false},
          pointer: {show: false},
          detail: {show: false}
        },
        // // 2. 第二层：精密虚线环
        {
          type: 'gauge',
          radius: '72%',
          startAngle: 360,
          endAngle: 0,
          // 核心修复点：明确指定仪表盘的分段总数
          splitNumber: 200, // 将整个圆环分成 100 份
          axisLine: {show: false},
          splitLine: {show: false},
          axisTick: {
            show: true,
            splitNumber: 1, // 关键：每份里只画 1 条线，这样总共就是 100 条
            distance: 0,
            length: 5,
            lineStyle: {
              color: '#00f2ff',
              width: 2, // 这里的宽度决定了“点”的大小
              type: 'solid'
            },
          },
          axisLabel: {show: false},
          pointer: {show: false},
          detail: {show: false}
        },
        {
          type: 'gauge',
          radius: '60%',
          startAngle: 360,
          endAngle: 0,
          axisLine: {
            lineStyle: {
              color: [[1, 'rgb(0, 255, 255)']],
              width: 5, // 这里加宽到 40，光晕会往圆圈内部伸展很多
              shadowBlur: 30, // 扩散范围加大
              shadowColor: 'rgb(0,178,255)'
            }
          },
          splitLine: {show: false},
          axisTick: {show: false},
          axisLabel: {show: false},
          pointer: {show: false},
          detail: {show: false}
        },
        {
          name: '左下角小格',
          type: 'gauge',
          radius: '65%',      // 半径，根据需要调整，应在 60% 内部
          startAngle: 360,    // 左下角起始角度
          endAngle: 270,      // 左下角结束角度
          splitNumber: 2,    // 决定小格子的密度
          axisLine: {show: false},
          splitLine: {show: false},
          axisLabel: {show: false},
          pointer: {show: false},
          detail: {show: false},
          axisTick: {
            show: true,
            length: 6,        // 小格子的“高度”
            lineStyle: {
              color: '#00f2ff',
              width: 10,       // 小格子的“宽度”，增加这个值会让它更像方块
              type: 'solid'
            }
          }
        },
        {
          name: '右上角小格',
          type: 'gauge',
          radius: '65%',
          startAngle: 180,     // 右上角起始角度
          endAngle: 90,      // 右上角结束角度
          splitNumber: 2,
          axisLine: {show: false},
          splitLine: {show: false},
          axisLabel: {show: false},
          pointer: {show: false},
          detail: {show: false},
          axisTick: {
            show: true,
            length: 6,
            lineStyle: {
              color: '#00f2ff',
              width: 10,
              type: 'solid'
            }
          }
        },
        {
          type: 'gauge',
          zlevel: 10, // 确保数字在最上层
          radius: '0%',
          axisLine: {show: false},
          splitLine: {show: false},
          axisTick: {show: false},
          axisLabel: {show: false},
          pointer: {show: false},
          detail: {
            // 1. 使用 {unit|%} 语法，这里的 unit 就是样式标签名
            formatter: '{value}{unit|%}',
            offsetCenter: [0, '5%'],
            fontSize: 60, // 这是数字的大小
            color: '#00f2ff',
            fontFamily: 'DigitalFont',

            // 2. 在 rich 属性里定义 unit 标签的具体样式
            rich: {
              unit: {
                fontSize: 35,        // 百分号的大小，调小一点
                color: '#00f2ff',    // 颜色保持一致
                padding: [0, 0, 0, 5],  // 通过 padding 微调位置：[上, 右, 下, 左]
                // 下边距 10 可以把百分号往上提一点，左边距 5 增加和数字的间距
                fontWeight: 'bold',
              }
            }
          },
          data: [{value: valueFromOutside.value}]
        }
      ]
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
  transition: opacity 0.2s;
}

.chart {
  height: 600rpx;
}

.chart-title {
  margin: 20rpx 20rpx 0;
  font-size: 36rpx;
  text-align: center;
  color: #e0faff;
  font-weight: 500;
}

/* 统计网格布局调整，让卡片更长 */
.stats-grid {
  display: flex;
  justify-content: space-between;
  padding: 0 18rpx;
  margin-top: 50rpx;
  transition: opacity 0.2s;
}

.stat-card {
  background: linear-gradient(135deg, #1a2f42 0%, #08121a 100%);

  /* 2. 边缘勾勒：图片中卡片左上角有一层浅色的边框感 */
  border: 1px solid rgba(0, 242, 255, 0.25);

  /* 3. 内部光感：使用 inset 内阴影模拟图片中那种“内部透光”的效果 */
  /* 给顶部和左侧增加一层极细的微光 */
  box-shadow: inset 2px 2px 15rpx rgba(0, 242, 255, 0.1),
  0 8rpx 20rpx rgba(0, 0, 0, 0.4);

  /* 增加圆角，更符合图片中的弧度 */
  border-radius: 30rpx;

  /* 调整宽度和高度，使其显得更“长” */
  width: 31%;
  padding: 25rpx 10rpx 30rpx;

  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: space-between; /* 让内容撑开 */
  position: relative;
}

.label {
  /* 标签颜色接近白色，增加一点点间距 */
  font-size: 24rpx;
  color: #e0faff;
  font-weight: 500;
  margin-bottom: 10rpx;
  text-align: center;
}

.value {
  /* 数字颜色使用明亮的青色，并增加外发光 */
  font-size: 60rpx; /* 放大数字 */
  color: #72f2ff;
  font-weight: bold;
  text-shadow: 0 0 20rpx rgba(114, 242, 255, 0.6);
  margin-bottom: 20rpx;
}

/* 底部指示点样式 */
.dot-indicator {
  display: flex;
  justify-content: center;
  gap: 8rpx;
}

.dot {
  width: 8rpx;
  height: 8rpx;
  background: rgba(0, 242, 255, 0.2);
  border-radius: 50%;
}

.dot.active {
  background: #72f2ff;
  box-shadow: 0 0 8rpx #72f2ff;
}
</style>