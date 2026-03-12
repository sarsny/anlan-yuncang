<script setup>
import { onMounted, ref, watch } from 'vue';
import * as echarts from 'echarts';

const props = defineProps({
  data: {
    type: Array,
    default: () => [18, 17, 16, 15.5, 15, 14.2]
  },
  color: {
    type: String,
    default: '#10b981'
  },
  label: {
    type: String,
    default: '%'
  }
});

const chartRef = ref(null);
let myChart = null;

const initChart = () => {
  if (myChart) myChart.dispose();
  const chartDom = chartRef.value;
  if (!chartDom) return;
  
  myChart = echarts.init(chartDom);

  const option = {
    grid: {
      top: '20%',
      bottom: '10%',
      left: '10%',
      right: '10%',
      containLabel: true
    },
    tooltip: {
      trigger: 'axis'
    },
    xAxis: {
      type: 'category',
      data: ['1月', '2月', '3月', '4月', '5月', '6月'],
      axisLine: { lineStyle: { color: '#555' } },
      axisLabel: { color: '#aaa' }
    },
    yAxis: {
      type: 'value',
      axisLine: { show: false },
      splitLine: { lineStyle: { color: '#333', type: 'dashed' } },
      axisLabel: { color: '#aaa', formatter: `{value}${props.label}` }
    },
    series: [
      {
        data: props.data,
        type: 'line',
        smooth: true,
        symbol: 'circle',
        symbolSize: 6,
        itemStyle: {
          color: props.color
        },
        areaStyle: {
          color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [
            { offset: 0, color: props.color.replace(')', ', 0.5)').replace('rgb', 'rgba').replace('#10b981', 'rgba(16, 185, 129, 0.5)').replace('#3b82f6', 'rgba(59, 130, 246, 0.5)') },
            { offset: 1, color: 'rgba(0,0,0,0)' }
          ])
        },
        markPoint: {
          data: [
            { type: 'max', name: 'Max' },
            { type: 'min', name: 'Min' }
          ]
        }
      }
    ]
  };

  myChart.setOption(option);
};

onMounted(() => {
  setTimeout(() => {
    initChart();
  }, 100);
  window.addEventListener('resize', () => myChart?.resize());
});

watch(() => props.data, () => {
  initChart();
});
</script>

<template>
  <div ref="chartRef" class="w-full h-full min-h-[150px]"></div>
</template>
