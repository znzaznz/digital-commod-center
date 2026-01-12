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
    default: () => []
  }
});

// 定义事件
const emit = defineEmits(['segmentClick']);

const chartRef = ref(null);
let myChart = null;

// 定义20种好看的科技感颜色
const techColors = [
  '#04fb8e', // 2. 图片绿 (Maintenance)
  '#5a77ed', // 3. 图片紫蓝 (Others)
  '#4df1fb', // 1. 图片青 (Business Unit A)
  '#ffed4a', // 4. 亮黄
  '#ff7eb3', // 5. 樱花粉
  '#9d4edd', // 6. 电子紫
  '#00f5d4', // 7. 蒂芙尼绿
  '#ff9100', // 8. 亮橙
  '#00bbf9', // 9. 天蓝
  '#f15bb5', // 10. 玫红
  '#fee440', // 11. 柠檬黄
  '#3a86ff', // 12. 皇家蓝
  '#06d6a0', // 13. 薄荷色
  '#ff4d4d', // 14. 珊瑚红
  '#8338ec', // 15. 霓虹紫
  '#fb5607', // 16. 烈焰橙
  '#e5ff00', // 17. 荧光黄
  '#00cc66', // 18. 深翠绿
  '#118ab2', // 19. 湖蓝
  '#ff006e'  // 20. 艳粉
];

const getOption = () => {
  // 将传入的数据与颜色进行映射
  const formattedData = props.chartData.map((item, index) => {
    const color = techColors[index % techColors.length]; // 超过20个循环取色
    return {
      ...item,
      itemStyle: {
        color: color,
        // 添加发光效果
        shadowBlur: 8,
        shadowColor: color,
        shadowOffsetX: 0,
        shadowOffsetY: 0
      }
    };
  });

  return {
    backgroundColor: 'transparent',
    tooltip: {
      trigger: 'item',
      formatter: '{b}: {c} ({d}%)'
    },
    series: [
      {
        name: 'Tech Department',
        type: 'pie',
        radius: ['0%', '85%'], // 饼图大小
        center: ['50%', '50%'],
        avoidLabelOverlap: true,
        itemStyle: {
          borderRadius: 2, // 扇区边缘圆角，更柔和
          borderColor: 'rgba(0,0,0,0.3)', // 微弱的分隔线
          borderWidth: 1
        },
        label: {
          show: true,
          position: 'inside', // 文字在内部
          // 格式化：名称 \n 百分比
          formatter: '{b}\n{d}%',
          color: '#1a1a1a', // 深色文字保证在亮色块上清晰
          fontSize: 12,
          fontWeight: 'bold',
          lineHeight: 16
        },
        // 选中时的放大效果和加深发光
        emphasis: {
          scale: true,
          scaleSize: 10,
          itemStyle: {
            shadowBlur: 40,
            shadowColor: 'rgba(255, 255, 255, 0.5)'
          }
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

// 监听数据变化，实时更新
watch(() => props.chartData, () => {
  if (myChart) {
    myChart.setOption(getOption(), true); // true 表示不合并，完全重画
  }
}, {deep: true});

onMounted(() => {
  nextTick(() => {
    setTimeout(init, 300);
  });
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

defineExpose({resize});
</script>

<style scoped>
.chart-container {
  width: 100%;
  height: 100% /* 根据实际页面布局调整高度 */
}
</style>