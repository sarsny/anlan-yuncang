<script setup>
import { onMounted, onUnmounted, ref } from 'vue';
import * as echarts from 'echarts';

const mapContainer = ref(null);
let myChart = null;
const popupVisible = ref(false);
const popupData = ref({});

// Mock Data - Hangzhou Wide Distribution
const govPoints = [
  { name: '上城区政府库', value: [120.2095, 30.2435, 100] }, // Shangcheng Center
  { name: '丁兰街道点', value: [120.2295, 30.3435, 50] },   // Shangcheng North
  { name: '西湖区中心库', value: [120.1295, 30.2735, 120] }, // West
  { name: '滨江区前置点', value: [120.2115, 30.1835, 80] },  // South
  { name: '余杭区物资站', value: [120.0295, 30.3535, 60] }   // Northwest
];

const socialPoints = [
  { name: '极邦应急仓', value: [120.3995, 30.1435, 200] }, // Xiaoshan East
  { name: '思为科技仓', value: [120.2595, 30.2035, 150] }, // Xiaoshan North
  { name: '华联萧山总仓', value: [120.2695, 30.1635, 180] }  // Xiaoshan Center
];

const logisticsPoints = [
  { name: '顺丰钱塘枢纽', value: [120.4595, 30.2835, 250] }, // Qiantang
  { name: '京东临平仓', value: [120.3095, 30.4135, 300] }    // Linping
];

const communityPoints = [
  { name: '小营街道社区仓', value: [120.1795, 30.2535] },
  { name: '湖滨街道前置仓', value: [120.1650, 30.2550] },
  { name: '清波街道物资点', value: [120.1680, 30.2380] },
  { name: '望江街道应急柜', value: [120.1850, 30.2350] },
  { name: '南星街道服务站', value: [120.1700, 30.2150] },
  { name: '紫阳街道储备点', value: [120.1800, 30.2250] },
  { name: '闸弄口街道分仓', value: [120.2050, 30.2850] },
  { name: '凯旋街道供应站', value: [120.1950, 30.2650] },
  { name: '采荷街道便民点', value: [120.2050, 30.2550] },
  { name: '四季青街道中转', value: [120.2150, 30.2550] },
  { name: '笕桥街道投放点', value: [120.2350, 30.3050] },
  { name: '彭埠街道物资站', value: [120.2450, 30.2950] },
  { name: '丁兰街道社区库', value: [120.2250, 30.3450] },
  { name: '九堡街道应急点', value: [120.2650, 30.3150] },
  { name: '笕桥花园社区仓', value: [120.2300, 30.3100] },
  { name: '南星水澄桥库', value: [120.1750, 30.2100] },
  { name: '望江近江家园点', value: [120.1900, 30.2300] },
  { name: '四季青钱江苑站', value: [120.2100, 30.2600] },
  { name: '采荷洁莲社区点', value: [120.2000, 30.2500] },
  { name: '闸弄口天杭社区', value: [120.2000, 30.2800] },
];

const flowLines = [
  {
    coords: [
      [120.3995, 30.1435], // Jibang (Xiaoshan)
      [120.2095, 30.2435]  // Downtown (Shangcheng)
    ]
  },
  {
    coords: [
      [120.3095, 30.4135], // Linping
      [120.2295, 30.3435]  // Dinglan (Shangcheng)
    ]
  }
];

const initMap = async () => {
  try {
    const chartDom = mapContainer.value;
    myChart = echarts.init(chartDom);
    myChart.showLoading();

    // Load Hangzhou GeoJSON
    const response = await fetch('/hangzhou.json');
    if (!response.ok) throw new Error('Failed to load map data');
    const geoJson = await response.json();
    
    echarts.registerMap('hangzhou', geoJson);
    myChart.hideLoading();

    const option = {
      geo: {
        map: 'hangzhou',
        roam: true,
        zoom: 1.2,
        center: [120.15, 30.28], // Adjust center to show main districts
        label: {
          show: false,
          color: '#fff'
        },
        itemStyle: {
          areaColor: '#1e293b', // slate-800 (Default color)
          borderColor: '#475569', // slate-600
          borderWidth: 1,
          shadowColor: 'rgba(0, 0, 0, 0.5)',
          shadowBlur: 10
        },
        emphasis: {
          itemStyle: {
            areaColor: '#334155' // slate-700
          },
          label: {
            show: true,
            color: '#fff'
          }
        },
        // Highlight Shangcheng District using regions
        regions: [
          {
            name: '上城区',
            itemStyle: {
              areaColor: '#0f172a', // Darker slate
              borderColor: '#06b6d4', // Cyan-500 highlight
              borderWidth: 2,
              shadowColor: 'rgba(6, 182, 212, 0.5)',
              shadowBlur: 15
            },
            label: {
              show: true,
              color: '#06b6d4',
              fontSize: 14,
              fontWeight: 'bold'
            }
          }
        ]
      },
      tooltip: {
        trigger: 'item',
        formatter: (params) => {
          if (params.seriesType === 'scatter' || params.seriesType === 'effectScatter') {
            return `${params.marker} ${params.name}`;
          }
          return params.name;
        }
      },
      series: [
        {
          name: '政府储备点',
          type: 'scatter',
          coordinateSystem: 'geo',
          data: govPoints,
          symbolSize: 10,
          itemStyle: {
            color: '#3b82f6', // Blue
            shadowBlur: 5,
            shadowColor: '#3b82f6'
          },
          label: {
            show: true,
            position: 'right',
            formatter: '{b}',
            color: '#cbd5e1', // slate-300
            fontSize: 10,
            backgroundColor: 'rgba(0,0,0,0.6)',
            padding: [2, 4],
            borderRadius: 2
          }
        },
        {
          name: '社会化储备点',
          type: 'effectScatter',
          coordinateSystem: 'geo',
          data: socialPoints,
          symbolSize: 15,
          showEffectOn: 'render',
          rippleEffect: {
            brushType: 'stroke',
            scale: 3
          },
          itemStyle: {
            color: '#f97316', // Orange
            shadowBlur: 10,
            shadowColor: '#f97316'
          },
          label: {
            show: true,
            position: 'right',
            formatter: '{b}',
            color: '#fff',
            fontWeight: 'bold',
            fontSize: 11,
            backgroundColor: 'rgba(0,0,0,0.6)',
            padding: [2, 4],
            borderRadius: 2
          }
        },
        {
          name: '物流运输点',
          type: 'scatter',
          coordinateSystem: 'geo',
          data: logisticsPoints,
          symbol: 'path://M20,8h-3L14,2H4C2.9,2,2,2.9,2,4v12h2c0,1.7,1.3,3,3,3s3-1.3,3-3h4c0,1.7,1.3,3,3,3s3-1.3,3-3h2v-5L20,8z M7,17c-0.6,0-1-0.4-1-1s0.4-1,1-1s1,0.4,1,1S7.6,17,7,17z M17,17c-0.6,0-1-0.4-1-1s0.4-1,1-1s1,0.4,1,1S17.6,17,17,17z M16.5,12l2.2-3H15v3H16.5z',
          symbolSize: 20,
          itemStyle: {
            color: '#10b981', // Green
            shadowBlur: 10,
            shadowColor: '#10b981'
          },
          label: {
            show: true,
            position: 'right',
            formatter: '{b}',
            color: '#fff',
            fontSize: 11,
            backgroundColor: 'rgba(0,0,0,0.6)',
            padding: [2, 4],
            borderRadius: 2
          }
        },
        {
          name: '社区级云仓',
          type: 'scatter',
          coordinateSystem: 'geo',
          data: communityPoints,
          symbolSize: 6,
          itemStyle: {
            color: '#ec4899', // Pink-500
            shadowBlur: 3,
            shadowColor: '#ec4899'
          },
          label: {
            show: false,
            emphasis: {
              show: true,
              position: 'top',
              formatter: '{b}',
              color: '#fff',
              fontSize: 10,
              backgroundColor: 'rgba(0,0,0,0.6)',
              padding: [2, 4],
              borderRadius: 2
            }
          }
        },
        {
          id: 'flow-lines',
          type: 'lines',
          coordinateSystem: 'geo',
          effect: {
            show: true,
            period: 6,
            trailLength: 0.7,
            color: '#fff',
            symbolSize: 3
          },
          lineStyle: {
            color: '#f97316',
            width: 1,
            curveness: 0.2,
            opacity: 0.5
          },
          data: flowLines
        }
      ]
    };

    myChart.setOption(option);

    myChart.on('click', (params) => {
      // Mock data for popup
      let details = {};
      if (params.name === '极邦应急仓') {
        details = {
          name: '极邦应急仓',
          distance: '40分钟',
          status: '活跃',
          stock: '200,000 件',
          category: '防汛/生活',
          manager: '张经理 138xxxx'
        };
      } else if (params.name === '京东临平仓') {
        details = {
          name: '京东临平仓',
          distance: '50分钟',
          status: '活跃',
          stock: '300,000 件',
          category: '生活/医疗',
          manager: '李经理 139xxxx'
        };
      } else {
        // Generic fallback
        details = {
          name: params.name,
          distance: '未知',
          status: '正常',
          stock: '100,000 件',
          category: '通用物资',
          manager: '值班员 137xxxx'
        };
      }
      
      popupData.value = details;
      popupVisible.value = true;
    });
    
  } catch (e) {
    console.error('Map loading failed:', e);
  }
};

const highlightRoute = () => {
  if (!myChart) return;
  myChart.setOption({
    series: [
      {
        id: 'flow-lines',
        type: 'lines',
        lineStyle: {
          width: 3,
          opacity: 1,
          color: '#00ff00'
        },
        effect: {
          symbolSize: 5,
          color: '#fff',
          period: 2
        }
      }
    ]
  });
};

const startMobilization = () => {
  popupVisible.value = false;
  highlightRoute();
  alert('战时动员指令已下达！物流路径已高亮。');
};

defineExpose({ highlightRoute });

onMounted(() => {
  initMap();
  window.addEventListener('resize', () => myChart?.resize());
});

onUnmounted(() => {
  if (myChart) myChart.dispose();
});
</script>

<template>
  <div class="h-full w-full bg-black/20 backdrop-blur-md rounded-xl border border-white/10 p-0 relative overflow-hidden group">
    <div ref="mapContainer" class="w-full h-full bg-gray-900 rounded-xl relative z-0">
      <!-- Fallback / Loading text -->
      <div class="absolute inset-0 flex items-center justify-center text-gray-500 z-[-1]">
        Map Loading...
      </div>
    </div>
    
    <!-- Core Metrics Overlay -->
    <div class="absolute top-16 left-1/2 transform -translate-x-1/2 z-20 flex gap-4">
      <div class="bg-gray-900/80 backdrop-blur-md border border-white/10 px-4 py-2 rounded-lg flex flex-col items-center min-w-[100px] group hover:border-blue-500/50 transition-colors">
        <span class="text-xs text-gray-400">上云企业总数</span>
        <span class="text-lg font-bold text-white group-hover:text-blue-400">12 <span class="text-xs font-normal text-gray-500">家</span></span>
      </div>
      <div class="bg-gray-900/80 backdrop-blur-md border border-white/10 px-4 py-2 rounded-lg flex flex-col items-center min-w-[100px] group hover:border-orange-500/50 transition-colors">
        <span class="text-xs text-gray-400">上云物资总数</span>
        <span class="text-lg font-bold text-white group-hover:text-orange-400">65.2 <span class="text-xs font-normal text-gray-500">万件</span></span>
      </div>
      <div class="bg-gray-900/80 backdrop-blur-md border border-white/10 px-4 py-2 rounded-lg flex flex-col items-center min-w-[100px] group hover:border-green-500/50 transition-colors">
        <span class="text-xs text-gray-400 flex items-center gap-1">
          云仓效能比
          <span class="w-1.5 h-1.5 rounded-full bg-green-500 animate-pulse"></span>
        </span>
        <span class="text-lg font-bold text-transparent bg-clip-text bg-gradient-to-r from-green-400 to-emerald-300">1 : 15</span>
      </div>
    </div>

    <!-- Title Overlay -->
    <div class="absolute top-4 left-4 z-10 pointer-events-none">
      <h2 class="text-xl font-semibold text-white/90 border-l-4 border-cyan-500 pl-3 drop-shadow-md">
        GIS 资源动态
      </h2>
    </div>

    <!-- Legend Overlay -->
    <div class="absolute bottom-4 right-4 z-10 bg-black/60 p-2 rounded backdrop-blur-sm border border-white/10">
      <div class="flex items-center gap-2 mb-1">
        <span class="w-3 h-3 rounded-full bg-blue-500"></span>
        <span class="text-xs text-gray-300">政府储备点</span>
      </div>
      <div class="flex items-center gap-2">
        <span class="w-3 h-3 rounded-full bg-orange-500 animate-pulse"></span>
        <span class="text-xs text-gray-300">社会化企业</span>
      </div>
    </div>

    <!-- Info Popup -->
    <div v-if="popupVisible" class="absolute top-1/2 left-1/2 transform -translate-x-1/2 -translate-y-1/2 z-50 bg-gray-900/95 border border-cyan-500/50 p-5 rounded-xl shadow-2xl w-80 text-left animate-fade-in backdrop-blur-md">
      <div class="flex justify-between items-start mb-3 border-b border-white/10 pb-2">
        <h3 class="text-lg font-bold text-cyan-400">{{ popupData.name }}</h3>
        <span class="px-2 py-0.5 rounded text-xs bg-green-900/50 text-green-400 border border-green-500/30">{{ popupData.status }}</span>
      </div>
      
      <div class="grid grid-cols-2 gap-3 text-sm mb-4">
        <div>
          <div class="text-gray-500 text-xs">物资种类</div>
          <div class="text-gray-200">{{ popupData.category }}</div>
        </div>
        <div>
          <div class="text-gray-500 text-xs">库存水位</div>
          <div class="text-yellow-400 font-medium">{{ popupData.stock }}</div>
        </div>
        <div>
          <div class="text-gray-500 text-xs">负责人</div>
          <div class="text-gray-200">{{ popupData.manager }}</div>
        </div>
        <div>
          <div class="text-gray-500 text-xs">距主城区</div>
          <div class="text-white">{{ popupData.distance }}</div>
        </div>
      </div>

      <div class="flex gap-2 justify-center pt-2 border-t border-white/10">
        <button @click="startMobilization" class="flex-1 py-1.5 bg-red-600/80 hover:bg-red-600 text-white rounded text-sm font-medium transition-colors border border-red-500/50 shadow-[0_0_10px_rgba(220,38,38,0.3)]">
          战时动员
        </button>
        <button @click="$emit('open-modal', popupData)" class="flex-1 py-1.5 bg-blue-600/80 hover:bg-blue-600 text-white rounded text-sm font-medium transition-colors border border-blue-500/50 shadow-[0_0_10px_rgba(37,99,235,0.3)]">
          物资详情
        </button>
        <button @click="popupVisible = false" class="flex-1 py-1.5 bg-gray-700/80 hover:bg-gray-700 text-white rounded text-sm transition-colors border border-gray-600">
          关闭
        </button>
      </div>
    </div>
  </div>
</template>

<style scoped>
.animate-fade-in {
  animation: fadeIn 0.3s ease-out;
}
@keyframes fadeIn {
  from { opacity: 0; transform: translate(-50%, -40%); }
  to { opacity: 1; transform: translate(-50%, -50%); }
}
/* Ensure map container has size */
#map-container {
  width: 100%;
  height: 100%;
}
</style>
