<!-- components/TechPieChart/TechPieChart.vue -->
<template>
  <view class="chart-container">
    <l-echart ref="chartRef"></l-echart>
  </view>
</template>

<script setup>
import {ref, onMounted, onUnmounted, watch, nextTick} from 'vue';
import * as echarts from '@/uni_modules/lime-echart/static/web/echarts.esm.min.js';

const props = defineProps({
  chartData: {
    type: Array,
    default: () => [
      {value: 65, name: '张总项目'},
      {value: 20, name: '李总项目'},
      {value: 15, name: '何总项目'}
    ]
  }
});

const chartRef = ref(null);
let myChart = null;

// 赛博霓虹色系
const techColors = [
  {main: '#00f2ff', glow: 'rgba(0, 242, 255, 0.4)'}, // 亮青
  {main: '#7000ff', glow: 'rgba(112, 0, 255, 0.4)'}, // 霓虹紫
  {main: '#00ff95', glow: 'rgba(0, 255, 149, 0.4)'}, // 荧光绿
  {main: '#ffed4a', glow: 'rgba(255, 237, 74, 0.4)'}  // 亮黄
];

const getOption = () => {
  const formattedData = props.chartData.map((item, index) => {
    const colorTheme = techColors[index % techColors.length];
    return {
      value: item.value,
      name: item.name,
      itemStyle: {
        color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [
          {offset: 0, color: colorTheme.main},
          {offset: 1, color: colorTheme.glow}
        ]),
        shadowBlur: 10,
        shadowColor: colorTheme.main,
        borderRadius: 2
      }
    };
  });

  return {
    backgroundColor: 'transparent',
    // 图例改到下方，解决侧边空间不足问题
    legend: {
      bottom: '5%',
      left: 'center',
      itemWidth: 10,
      itemHeight: 10,
      textStyle: {color: '#a5dff9', fontSize: 10},
      icon: 'rect'
    },
    title: {
      text: '投入分布',
      left: 'center',
      top: '44%',
      textStyle: {
        color: '#00f2ff',
        fontSize: 14,
        fontWeight: 'normal',
        textShadowBlur: 10,
        textShadowColor: '#00f2ff'
      }
    },
    series: [
      // 背景装饰虚线圈
      {
        type: 'pie',
        radius: ['80%', '82%'],
        center: ['50%', '48%'],
        silent: true,
        data: [{value: 1, itemStyle: {color: 'rgba(0, 242, 255, 0.1)'}}],
        label: {show: false}
      },
      // 主环形图
      {
        name: '项目投入',
        type: 'pie',
        radius: ['52%', '75%'], // 加厚环形，方便内嵌文字
        center: ['50%', '48%'],
        avoidLabelOverlap: false,
        padAngle: 4, // 扇区间隔
        itemStyle: {
          borderWidth: 0
        },
        label: {
          show: true,
          position: 'inside', // 文字放在环内
          formatter: '{d}%', // 只显示百分比，名字靠图例识别
          color: '#000', // 亮色背景配深色字更清晰
          fontSize: 11,
          fontWeight: 'bold'
        },
        emphasis: {
          scale: true,
          scaleSize: 5
        },
        data: formattedData
      }
    ]
  };
};

const init = async () => {
  if (!chartRef.value) return;
  myChart = await chartRef.value.init(echarts);
  myChart.setOption(getOption());
};

const resize = () => {
  if (myChart) myChart.resize();
};

watch(() => props.chartData, () => {
  if (myChart) myChart.setOption(getOption(), true);
}, {deep: true});

onMounted(() => {
  nextTick(() => setTimeout(init, 300));
});

onUnmounted(() => {
  if (myChart) {
    myChart.dispose();
    myChart = null;
  }
});

defineExpose({resize});
</script>

<style scoped>
.chart-container {
  width: 100%;
  height: 100%;
}
</style>