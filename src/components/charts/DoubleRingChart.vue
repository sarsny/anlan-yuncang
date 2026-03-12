<script setup>
import { onMounted, ref } from 'vue';
import * as echarts from 'echarts';

const chartRef = ref(null);
let myChart = null;

const props = defineProps({
  govData: {
    type: Number,
    default: 170754
  },
  socialData: {
    type: Number,
    default: 652370
  }
});

const initChart = () => {
  if (myChart) myChart.dispose();
  const chartDom = chartRef.value;
  if (!chartDom) return;
  
  myChart = echarts.init(chartDom);

  const option = {
    tooltip: {
      trigger: 'item',
      formatter: '{a} <br/>{b}: {c} ({d}%)'
    },
    legend: {
      bottom: '0%',
      left: 'center',
      textStyle: {
        color: '#fff'
      },
      data: ['政府实物储备', '社会化协议储备']
    },
    series: [
      {
        name: '储备类型',
        type: 'pie',
        radius: ['40%', '55%'],
        label: {
          show: false
        },
        labelLine: {
          show: false
        },
        data: [
          { value: props.govData, name: '政府实物储备', itemStyle: { color: '#3b82f6' } },
          { value: props.socialData, name: '社会化协议储备', itemStyle: { color: '#f97316' } }
        ]
      },
      {
        name: '外环装饰',
        type: 'pie',
        radius: ['60%', '62%'],
        label: { show: false },
        itemStyle: {
          color: 'rgba(255, 255, 255, 0.1)'
        },
        data: [{ value: 1, name: '', tooltip: { show: false } }],
        silent: true,
        z: 0
      }
    ]
  };

  myChart.setOption(option);
};

onMounted(() => {
  // Ensure DOM is ready
  setTimeout(() => {
    initChart();
  }, 100);
  window.addEventListener('resize', () => myChart?.resize());
});
</script>

<template>
  <div ref="chartRef" class="w-full h-full min-h-[180px]"></div>
</template>
