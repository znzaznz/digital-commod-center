<template>
  <page-layout title="资源监控" @drawerStateChange="handleDrawerState">
    <view class="monitor-container">
      <!-- 顶部图表区 -->
      <view class="chart-box" v-show="isChartVisible">
        <view class="chart-title">部门各项目投入比例</view>
        <view class="chart">
          <pie-chart :chartData="valueFromOutside"/>
        </view>
      </view>

      <!-- 下方列表卡片区 -->
      <view class="activities-container" v-show="isChartVisible">
        <view class="activities-card">
          <!-- 固定头部 -->
          <view class="card-header">
            <text class="card-title">近期完成任务</text>
            <text class="share-icon iconfont icon-24gl-clipboardList" style="color: #00f2ff; font-size: 46rpx;"></text>
          </view>

          <!-- 可滚动列表区 -->
          <scroll-view scroll-y="true" class="activity-scroll">
            <view class="activity-list">
              <view class="activity-item" v-for="(item, index) in activityList" :key="index">
                <view class="icon-wrapper">
                  <text class="activity-icon" :class="item.icon"></text>
                </view>
                <text class="activity-text">{{ item.content }}</text>
              </view>
              <!-- 底部留白，防止最后一条太贴边 -->
              <view style="height: 20rpx;"></view>
            </view>
          </scroll-view>
        </view>
      </view>
    </view>
  </page-layout>
</template>

<script setup>
import {ref, reactive} from 'vue';
import PieChart from "@/components/charts/pie-chart/pie-chart.vue";

const isChartVisible = ref(true);

const valueFromOutside = reactive([
  {value: 20, name: '李总项目'},
  {value: 15, name: '何总项目'},
  {value: 65, name: '张总项目'}
]);

const activityList = ref([
  {content: '赵哲楠写完了供应链大屏的前端页面赵哲楠写完了供应链大屏的前端页面', icon: 'icon-setting'},
  {content: '陈璐璐完成了聚水潭xxx接口的对接', icon: 'icon-setting'},
  {content: '全立明完成了xxx的数据爬取', icon: 'icon-refresh'},
  {content: '皇甫君完成了xxxxx的项目对接', icon: 'icon-deploy'},
  {content: '赵哲楠写完了供应链大屏的前端页面', icon: 'icon-setting'},
  {content: '陈璐璐完成了聚水潭xxx接口的对接', icon: 'icon-setting'},
  {content: '全立明完成了xxx的数据爬取', icon: 'icon-refresh'},
  {content: '皇甫君完成了xxxxx的项目对接', icon: 'icon-deploy'},
  {content: '测试数据1：更多内容滚动测试', icon: 'icon-setting'},
  {content: '测试数据2：更多内容滚动测试', icon: 'icon-refresh'},
  {content: '测试数据3：到底部了吗？', icon: 'icon-deploy'},
]);

const handleDrawerState = (isOpen) => {
  isChartVisible.value = !isOpen;
};
</script>

<style scoped>
/* 容器高度锁定为视口高度，禁止全局滚动 */
.monitor-container {
  height: 100%;
  display: flex;
  flex-direction: column;
  overflow: hidden;
  background-color: #000; /* 建议背景色与图表一致 */
}

.chart-box {
  flex-shrink: 0; /* 保证图表不被压缩 */
  padding-top: 20rpx;
}

.chart {
  margin: 20rpx 0;
  height: 550rpx; /* 稍微缩小一点给下方列表留空间 */
}

.chart-title {
  color: white;
  font-weight: 900;
  text-align: center;
  font-size: 32rpx;
  letter-spacing: 10rpx;
  text-shadow: 0 0 20rpx rgba(0, 242, 255, 0.5);
}

/* 列表卡片容器 */
.activities-container {
  flex: 1;
  min-height: 0; /* 关键：允许 flex 子项内部滚动而不撑开父级 */
  padding: 20rpx 30rpx 20rpx;
  box-sizing: border-box;
}

.activities-card {
  height: 100%;
  display: flex;
  flex-direction: column;
  background: rgba(10, 31, 44, 0.8);
  border: 1px solid rgba(0, 242, 255, 0.3);
  border-radius: 40rpx;
  box-shadow: inset 0 0 20rpx rgba(0, 242, 255, 0.1);
  overflow: hidden;
}

.card-header {
  flex-shrink: 0;
  padding: 30rpx 40rpx;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.card-title {
  color: #ffffff;
  font-size: 34rpx;
  font-weight: 500;
}

.share-icon {
  color: #4fd1d9;
  font-size: 40rpx;
}

/* 滚动区域高度充满卡片剩余部分 */
.activity-scroll {
  flex: 1;
  height: 0; /* 配合 flex:1 使用 */
}

.activity-list {
  padding: 0 30rpx;
}

.activity-item {
  background: rgba(255, 255, 255, 0.05);
  border-radius: 20rpx;
  padding: 24rpx 30rpx;
  margin-bottom: 20rpx;
  display: flex;
  align-items: center;
}

.icon-wrapper {
  margin-right: 20rpx;
  display: flex;
  align-items: center;
}

.activity-icon {
  width: 32rpx;
  height: 32rpx;
  border: 4rpx solid #4fd1d9;
  border-radius: 8rpx;
  display: inline-block;
}

.activity-text {
  color: #a0dfe3;
  font-size: 26rpx;
  line-height: 1.4;
  flex: 1;
}

/* 图标差异化 */
.icon-refresh {
  border-radius: 50%;
}

.icon-deploy {
  border-style: dashed;
}

/* 隐藏滚动条样式 (可选) */
::v-deep .uni-scroll-view::-webkit-scrollbar {
  display: none;
}
</style>