<!-- components/GaugeChart/GaugeChart.vue -->
<template>
  <view class="chart-container">
    <l-echart ref="chartRef"></l-echart>
  </view>
</template>

<script setup>
import {ref, onMounted, onUnmounted, watch, nextTick} from 'vue';
import * as echarts from '@/uni_modules/lime-echart/static/web/echarts.esm.min.js';

const props = defineProps({
  value: {
    type: Number,
    default: 0
  }
});

const chartRef = ref(null);
let myChart = null;

// 核心配置逻辑抽离
const getOption = (val) => {
  return {
    backgroundColor: 'transparent',
    series: [
      {
        type: 'gauge',
        radius: '87%',
        startAngle: 360,
        endAngle: 0,
        axisLine: {
          lineStyle: {
            color: [[1, 'rgb(0, 255, 255)']],
            width: 7,
            shadowBlur: 40,
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
        type: 'gauge',
        radius: '85%',
        startAngle: 360,
        endAngle: 0,
        splitNumber: 200,
        axisLine: {show: false},
        splitLine: {show: false},
        axisTick: {
          show: true,
          splitNumber: 1,
          distance: 0,
          length: 5,
          lineStyle: {color: '#00f2ff', width: 2, type: 'solid'},
        },
        axisLabel: {show: false},
        pointer: {show: false},
        detail: {show: false}
      },
      {
        type: 'gauge',
        radius: '70%',
        startAngle: 360,
        endAngle: 0,
        axisLine: {
          lineStyle: {
            color: [[1, 'rgb(0, 255, 255)']],
            width: 5,
            shadowBlur: 30,
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
        name: '左下/右上小格',
        type: 'gauge',
        radius: '80%',
        startAngle: 360,
        endAngle: 270,
        splitNumber: 1,
        axisLine: {show: false},
        splitLine: {show: false},
        axisLabel: {show: false},
        pointer: {show: false},
        detail: {show: false},
        axisTick: {
          show: true,
          length: 6,
          lineStyle: {color: '#00f2ff', width: 10, type: 'solid'}
        }
      },
      {
        name: '右上角小格',
        type: 'gauge',
        radius: '80%',
        startAngle: 180,
        endAngle: 90,
        splitNumber: 1,
        axisLine: {show: false},
        splitLine: {show: false},
        axisLabel: {show: false},
        pointer: {show: false},
        detail: {show: false},
        axisTick: {
          show: true,
          length: 6,
          lineStyle: {color: '#00f2ff', width: 10, type: 'solid'}
        }
      },
      {
        type: 'gauge',
        zlevel: 10,
        radius: '0%',
        axisLine: {show: false},
        splitLine: {show: false},
        axisTick: {show: false},
        axisLabel: {show: false},
        pointer: {show: false},
        detail: {
          formatter: '{value}{unit|%}',
          offsetCenter: [0, '5%'],
          fontSize: 60,
          color: '#00f2ff',
          fontFamily: 'DigitalFont',
          rich: {
            unit: {
              fontSize: 35,
              color: '#00f2ff',
              padding: [0, 0, 0, 0],
              fontWeight: 'bold',
            }
          }
        },
        data: [{value: val}]
      }
    ]
  };
};

// 暴露给父组件的方法：手动重绘
const resize = () => {
  if (myChart) {
    myChart.resize();
  }
};

// 初始化
const init = async () => {
  if (!chartRef.value) return;
  myChart = await chartRef.value.init(echarts);
  myChart.setOption(getOption(props.value));
};

// 监听数值变化
watch(() => props.value, (newVal) => {
  if (myChart) {
    myChart.setOption({
      series: [{data: [{value: newVal}]}]
    });
  }
});

onMounted(() => {
  nextTick(() => {
    setTimeout(init, 300);
  });

  // 浏览器窗口缩放监听
  // #ifdef H5
  window.addEventListener('resize', resize);
  // #endif
  // #ifndef H5
  uni.onWindowResize(resize);
  // #endif
});

onUnmounted(() => {
  // #ifdef H5
  window.removeEventListener('resize', resize);
  // #endif
  if (myChart) {
    myChart.dispose();
    myChart = null;
  }
});

// 暴露组件方法
defineExpose({resize});
</script>

<style scoped>
.chart-container {
  width: 100%;
  height: 100%;
}
</style>