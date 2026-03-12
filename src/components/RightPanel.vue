<script setup>
import { ref, onMounted } from 'vue';
import TrendChart from './charts/TrendChart.vue';
import SurveillanceModal from './SurveillanceModal.vue';

const savedCost = ref(0);
const targetCost = 2450000;
const actualAmount = ref(0);
const targetAmount = 1280000;

// Modal State
const isCamOpen = ref(false);
const camSource = ref('');

// Chart Data
const currentChart = ref('usage'); // usage | level
const chartData = ref([120, 132, 101, 134, 90, 230]);
const chartColor = ref('#3b82f6');
const chartLabel = ref('次');

const toggleChart = (type) => {
  currentChart.value = type;
  if (type === 'usage') {
    chartData.value = [120, 132, 101, 134, 90, 230];
    chartColor.value = '#3b82f6'; // Blue
    chartLabel.value = '次';
  } else {
    chartData.value = [85, 88, 82, 80, 75, 79];
    chartColor.value = '#f97316'; // Orange
    chartLabel.value = '%';
  }
};

const openCamera = (name) => {
  camSource.value = name;
  isCamOpen.value = true;
};

// Simple number animation
const animateValue = (refVal, end, duration) => {
  let start = 0;
  let startTime = null;
  const step = (timestamp) => {
    if (!startTime) startTime = timestamp;
    const progress = Math.min((timestamp - startTime) / duration, 1);
    refVal.value = Math.floor(progress * end);
    if (progress < 1) {
      window.requestAnimationFrame(step);
    }
  };
  window.requestAnimationFrame(step);
};

onMounted(() => {
  animateValue(savedCost, targetCost, 2000);
  animateValue(actualAmount, targetAmount, 2000);
});

const formatMoney = (val) => {
  return new Intl.NumberFormat('zh-CN', { style: 'currency', currency: 'CNY' }).format(val);
};

const suppliers = [
  { name: '极邦应急', credit: 'AAA', status: '优秀', hasCamera: true },
  { name: '思为科技', credit: 'AA+', status: '良好', hasCamera: false },
  { name: '战神救援', credit: 'AA', status: '良好', hasCamera: true },
  { name: '华联商超', credit: 'A', status: '正常', hasCamera: false },
  { name: '京东物流', credit: 'AAA', status: '优秀', hasCamera: true },
];
</script>

<template>
  <div class="h-full w-full bg-black/20 backdrop-blur-md rounded-xl border border-white/10 p-4 flex flex-col space-y-4 overflow-hidden">
    <h2 class="text-xl font-semibold text-white/90 border-l-4 border-purple-500 pl-3">降本增效收益</h2>
    
    <!-- Core Metrics (Compact Row) -->
    <div class="grid grid-cols-2 gap-3">
      <div class="bg-gradient-to-br from-purple-900/40 to-indigo-900/40 p-3 rounded-lg border border-purple-500/30 relative overflow-hidden group">
        <div class="text-xs text-gray-400 mb-0.5">仓储节约成本</div>
        <div class="text-lg font-bold text-transparent bg-clip-text bg-gradient-to-r from-purple-400 to-pink-400">
          {{ formatMoney(savedCost) }}
        </div>
        <div class="text-[10px] text-gray-500 mt-0.5">免租金/运维费</div>
      </div>

      <div class="bg-gradient-to-br from-blue-900/40 to-cyan-900/40 p-3 rounded-lg border border-blue-500/30 relative overflow-hidden">
        <div class="text-xs text-gray-400 mb-0.5">实际调用金额</div>
        <div class="text-lg font-bold text-white">
          {{ formatMoney(actualAmount) }}
        </div>
        <div class="text-[10px] text-gray-500 mt-0.5">按需结算</div>
      </div>
    </div>

    <!-- Loss Reduction Trend -->
    <div class="flex-1 min-h-[150px] flex flex-col">
      <div class="flex justify-between items-center mb-2">
        <h3 class="text-sm font-medium text-gray-300">云仓动态趋势</h3>
        <div class="flex bg-black/30 rounded p-0.5 border border-white/10">
          <button 
            @click="toggleChart('usage')" 
            class="px-2 py-0.5 text-[10px] rounded transition-colors"
            :class="currentChart === 'usage' ? 'bg-blue-600 text-white' : 'text-gray-400 hover:text-white'"
          >
            使用量
          </button>
          <button 
            @click="toggleChart('level')" 
            class="px-2 py-0.5 text-[10px] rounded transition-colors"
            :class="currentChart === 'level' ? 'bg-orange-600 text-white' : 'text-gray-400 hover:text-white'"
          >
            水位
          </button>
        </div>
      </div>
      <div class="flex-1 bg-black/20 rounded-lg p-2 border border-white/5">
        <TrendChart :data="chartData" :color="chartColor" :label="chartLabel" />
      </div>
    </div>

    <!-- Supplier List -->
    <div class="flex-1 overflow-hidden flex flex-col">
      <h3 class="text-sm font-medium text-gray-300 mb-2">供应商信用评价 (一企一档)</h3>
      <div class="overflow-y-auto pr-1 custom-scrollbar flex-1">
        <table class="w-full text-left text-xs text-gray-400">
          <thead class="text-gray-500 border-b border-white/10 sticky top-0 bg-[#0a0a0a] z-10">
            <tr>
              <th class="pb-2">企业名称</th>
              <th class="pb-2">信用等级</th>
              <th class="pb-2 text-right">状态</th>
            </tr>
          </thead>
          <tbody class="divide-y divide-white/5">
            <tr v-for="s in suppliers" :key="s.name" class="hover:bg-white/5 transition-colors">
              <td class="py-2 flex items-center gap-2">
                {{ s.name }}
                <button v-if="s.hasCamera" @click.stop="openCamera(s.name)" class="text-gray-500 hover:text-blue-400 transition-colors" title="查看监控">
                  <svg xmlns="http://www.w3.org/2000/svg" class="h-3 w-3" viewBox="0 0 20 20" fill="currentColor">
                    <path d="M2 6a2 2 0 012-2h6a2 2 0 012 2v8a2 2 0 01-2 2H4a2 2 0 01-2-2V6zM14.553 7.106A1 1 0 0014 8v4a1 1 0 00.553.894l2 1A1 1 0 0018 13V7a1 1 0 00-1.447-.894l-2 1z" />
                  </svg>
                </button>
              </td>
              <td class="py-2">
                <span :class="{'text-green-400': s.credit.startsWith('A'), 'text-yellow-400': !s.credit.startsWith('A')}">{{ s.credit }}</span>
              </td>
              <td class="py-2 text-right">
                <span class="px-2 py-0.5 rounded-full bg-white/10 text-white/80 text-[10px]">{{ s.status }}</span>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>

    <!-- Camera Modal -->
    <SurveillanceModal :isOpen="isCamOpen" :source="camSource" @close="isCamOpen = false" />
  </div>
</template>

<style scoped>
.custom-scrollbar::-webkit-scrollbar {
  width: 4px;
}
.custom-scrollbar::-webkit-scrollbar-thumb {
  background: #444;
  border-radius: 2px;
}

@keyframes pulse-slow {
  0%, 100% { opacity: 1; transform: scale(1); }
  50% { opacity: 0.8; transform: scale(1.02); }
}
.animate-pulse-slow {
  animation: pulse-slow 3s infinite;
}
</style>
