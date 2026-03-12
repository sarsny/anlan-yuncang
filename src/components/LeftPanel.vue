<script setup>
import { ref, computed } from 'vue';
import DoubleRingChart from './charts/DoubleRingChart.vue';
import SurveillanceModal from './SurveillanceModal.vue';

const materials = [
  { name: '生活保障类', gov: 30, social: 70 },
  { name: '医疗防护类', gov: 20, social: 80 },
  { name: '防汛救灾类', gov: 40, social: 60 },
];

const currentLevel = ref('district'); // 'district' | 'street'

const districtEnterprises = [
  { name: '极邦应急', type: '物资', stock: '20万件', status: '正常', hasCamera: true },
  { name: '思为科技', type: '技术', stock: '5万套', status: '正常', hasCamera: false },
  { name: '战神救援', type: '装备', stock: '8万件', status: '待命', hasCamera: true },
  { name: '华联商超', type: '生活', stock: '12万件', status: '正常', hasCamera: false },
  { name: '京东物流', type: '运输', stock: '50车', status: '活跃', hasCamera: true },
  { name: '顺丰速运', type: '运输', stock: '30车', status: '活跃', hasCamera: false },
];

const streetEnterprises = [
  { name: '小营街道社区仓', type: '生活', stock: '2000件', status: '正常', hasCamera: true },
  { name: '湖滨街道前置仓', type: '物资', stock: '1500件', status: '正常', hasCamera: false },
  { name: '清波街道物资点', type: '急救', stock: '800件', status: '正常', hasCamera: true },
  { name: '望江街道应急柜', type: '装备', stock: '300套', status: '待命', hasCamera: false },
  { name: '南星街道服务站', type: '生活', stock: '5000件', status: '正常', hasCamera: true },
  { name: '紫阳街道储备点', type: '物资', stock: '1200件', status: '正常', hasCamera: false },
  { name: '闸弄口街道分仓', type: '食品', stock: '3000份', status: '活跃', hasCamera: true },
  { name: '凯旋街道供应站', type: '水', stock: '1000箱', status: '正常', hasCamera: false },
  { name: '采荷街道便民点', type: '生活', stock: '2500件', status: '正常', hasCamera: true },
  { name: '四季青街道中转', type: '运输', stock: '5车', status: '活跃', hasCamera: false },
  { name: '笕桥街道投放点', type: '物资', stock: '1800件', status: '正常', hasCamera: true },
  { name: '彭埠街道物资站', type: '装备', stock: '600套', status: '正常', hasCamera: false },
  { name: '丁兰街道社区库', type: '生活', stock: '4000件', status: '正常', hasCamera: true },
  { name: '九堡街道应急点', type: '急救', stock: '500包', status: '待命', hasCamera: false },
  { name: '笕桥花园社区仓', type: '生活', stock: '1200件', status: '正常', hasCamera: true },
  { name: '南星水澄桥库', type: '物资', stock: '900件', status: '正常', hasCamera: false },
  { name: '望江近江家园点', type: '食品', stock: '2000份', status: '活跃', hasCamera: true },
  { name: '四季青钱江苑站', type: '水', stock: '800箱', status: '正常', hasCamera: false },
  { name: '采荷洁莲社区点', type: '生活', stock: '1500件', status: '正常', hasCamera: true },
  { name: '闸弄口天杭社区', type: '装备', stock: '400套', status: '待命', hasCamera: false },
];

const displayEnterprises = computed(() => {
  return currentLevel.value === 'district' ? districtEnterprises : streetEnterprises;
});

const isCamOpen = ref(false);
const camSource = ref('');

const openCamera = (name) => {
  camSource.value = name;
  isCamOpen.value = true;
};
</script>

<template>
  <div class="h-full w-full bg-black/20 backdrop-blur-md rounded-xl border border-white/10 p-4 flex flex-col overflow-hidden">
    <h2 class="text-xl font-semibold text-white/90 border-l-4 border-blue-500 pl-3 mb-4 shrink-0">资源一张网</h2>
    
    <div class="flex-1 min-h-0 flex flex-col space-y-3">
      <!-- Top Section (Charts & Materials) - Flex Weight 4 -->
      <div class="flex-[4] flex flex-col space-y-3 min-h-0 overflow-hidden pb-2">
        <!-- Chart Section -->
        <div class="flex-1 min-h-0 relative">
          <DoubleRingChart :govData="170754" :socialData="652370" />
          <div class="absolute inset-0 flex items-center justify-center pointer-events-none">
            <div class="text-center mt-6">
              <div class="text-xs text-gray-400">总储备量</div>
              <div class="text-xl font-bold text-white">82.3万</div>
            </div>
          </div>
        </div>

        <!-- Material List -->
        <div class="space-y-2 flex-1 min-h-0 overflow-y-auto custom-scrollbar">
          <h3 class="text-sm font-medium text-gray-300 flex items-center gap-2 sticky top-0 bg-[#0a0a0a]/90 backdrop-blur z-10 py-1">
            <span class="w-1 h-1 bg-white rounded-full"></span>
            物资分类占比 (社会化库存)
          </h3>
          <div v-for="item in materials" :key="item.name" class="space-y-1 px-1">
            <div class="flex justify-between text-xs text-gray-400">
              <span>{{ item.name }}</span>
              <span>{{ item.social }}%</span>
            </div>
            <div class="w-full h-1.5 bg-gray-700 rounded-full overflow-hidden">
              <div class="h-full bg-gradient-to-r from-orange-500 to-red-500 rounded-full" :style="{ width: item.social + '%' }"></div>
            </div>
          </div>
        </div>
      </div>

      <!-- Bottom Section (Enterprises) - Flex Weight 6 -->
      <div class="flex-[6] pt-4 border-t border-white/10 flex flex-col overflow-hidden min-h-0">
        <div class="flex justify-between items-center mb-3 shrink-0">
          <h3 class="text-sm font-medium text-gray-300">一云多仓 ({{ displayEnterprises.length }} 家)</h3>
          <div class="flex bg-white/10 rounded p-0.5">
            <button 
              @click="currentLevel = 'district'" 
              class="px-2 py-0.5 text-[10px] rounded transition-colors"
              :class="currentLevel === 'district' ? 'bg-blue-600 text-white' : 'text-gray-400 hover:text-white'"
            >
              区级
            </button>
            <button 
              @click="currentLevel = 'street'" 
              class="px-2 py-0.5 text-[10px] rounded transition-colors"
              :class="currentLevel === 'street' ? 'bg-blue-600 text-white' : 'text-gray-400 hover:text-white'"
            >
              街道/网格
            </button>
          </div>
        </div>
        <div class="grid grid-cols-2 gap-3 overflow-y-auto custom-scrollbar pr-1 flex-1 min-h-0 content-start">
          <div v-for="ent in displayEnterprises" :key="ent.name" @click="$emit('open-modal', ent)" class="bg-white/5 border border-white/5 p-3 rounded-lg text-xs text-gray-300 flex flex-col gap-2 hover:bg-white/10 transition-colors cursor-pointer group h-[80px]">
            <div class="flex justify-between items-center w-full">
              <div class="flex items-center gap-1 overflow-hidden">
                <span class="font-bold text-white group-hover:text-blue-300 transition-colors truncate" :title="ent.name">{{ ent.name }}</span>
                <button v-if="ent.hasCamera" @click.stop="openCamera(ent.name)" class="text-gray-500 hover:text-blue-400 transition-colors p-0.5 shrink-0" title="查看监控">
                  <svg xmlns="http://www.w3.org/2000/svg" class="h-3 w-3" viewBox="0 0 20 20" fill="currentColor">
                    <path d="M2 6a2 2 0 012-2h6a2 2 0 012 2v8a2 2 0 01-2 2H4a2 2 0 01-2-2V6zM14.553 7.106A1 1 0 0014 8v4a1 1 0 00.553.894l2 1A1 1 0 0018 13V7a1 1 0 00-1.447-.894l-2 1z" />
                  </svg>
                </button>
              </div>
              <span class="text-[10px] bg-blue-900/50 text-blue-300 px-1.5 py-0.5 rounded group-hover:bg-blue-800 whitespace-nowrap shrink-0">{{ ent.type }}</span>
            </div>
            <div class="flex justify-between text-[10px] text-gray-500 mt-auto">
               <span>{{ ent.stock }}</span>
               <span>{{ ent.status }}</span>
            </div>
          </div>
        </div>
      </div>
    </div>
    
    <SurveillanceModal
      :isOpen="isCamOpen"
      :source="camSource"
      @close="isCamOpen = false"
    />
  </div>
</template>

<style scoped>
.custom-scrollbar::-webkit-scrollbar {
  width: 4px;
}
.custom-scrollbar::-webkit-scrollbar-track {
  background: transparent;
}
.custom-scrollbar::-webkit-scrollbar-thumb {
  background: #374151;
  border-radius: 2px;
}
.custom-scrollbar::-webkit-scrollbar-thumb:hover {
  background: #4b5563;
}
</style>
