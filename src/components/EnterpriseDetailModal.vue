<script setup>
import { ref } from 'vue';

const props = defineProps({
  isOpen: Boolean,
  enterprise: Object
});

const emit = defineEmits(['close']);

const closeModal = () => {
  emit('close');
};

// Mock detailed inventory data
const inventory = [
  { id: 1, name: '防汛沙袋', spec: '25kg/袋', category: '防汛物资', quantity: 5000, unit: '袋', status: '充足' },
  { id: 2, name: '救生衣', spec: '成人通用', category: '救援装备', quantity: 2000, unit: '件', status: '充足' },
  { id: 3, name: '应急帐篷', spec: '12㎡/顶', category: '安置物资', quantity: 300, unit: '顶', status: '正常' },
  { id: 4, name: '折叠行军床', spec: '单人', category: '安置物资', quantity: 800, unit: '张', status: '正常' },
  { id: 5, name: '强光手电', spec: 'LED充电式', category: '照明设备', quantity: 1500, unit: '把', status: '充足' },
  { id: 6, name: '雨衣雨鞋', spec: '套装', category: '防护用品', quantity: 3000, unit: '套', status: '充足' },
];
</script>

<template>
  <div v-if="isOpen" class="fixed inset-0 z-[100] flex items-center justify-center p-4">
    <!-- Backdrop -->
    <div class="absolute inset-0 bg-black/80 backdrop-blur-sm" @click="closeModal"></div>
    
    <!-- Modal Content -->
    <div class="relative w-full max-w-4xl bg-gray-900 border border-blue-500/30 rounded-2xl shadow-2xl overflow-hidden flex flex-col max-h-[80vh] animate-scale-in">
      <!-- Header -->
      <div class="p-6 border-b border-white/10 flex justify-between items-center bg-gradient-to-r from-blue-900/20 to-transparent">
        <div>
          <h2 class="text-2xl font-bold text-white flex items-center gap-3">
            {{ enterprise?.name || '企业详情' }}
            <span class="text-xs px-2 py-1 rounded bg-blue-500/20 text-blue-300 border border-blue-500/30">
              {{ enterprise?.type || '物资储备' }}
            </span>
          </h2>
          <div class="text-sm text-gray-400 mt-1 flex gap-4">
            <span>负责人: {{ enterprise?.manager || '张经理' }}</span>
            <span>联系电话: {{ enterprise?.phone || '138-xxxx-xxxx' }}</span>
            <span>地址: {{ enterprise?.address || '杭州市萧山区xxx路' }}</span>
          </div>
        </div>
        <button @click="closeModal" class="text-gray-400 hover:text-white p-2 hover:bg-white/10 rounded-lg transition-colors">
          <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
          </svg>
        </button>
      </div>

      <!-- Inventory Table -->
      <div class="flex-1 overflow-auto p-6 bg-black/20">
        <div class="flex justify-between items-center mb-4">
          <h3 class="text-lg font-semibold text-blue-400 border-l-4 border-blue-500 pl-3">物资储备清单</h3>
          <div class="flex gap-2">
            <input type="text" placeholder="搜索物资名称..." class="bg-black/30 border border-white/10 rounded px-3 py-1.5 text-sm text-white focus:border-blue-500 outline-none w-64">
            <button class="px-4 py-1.5 bg-blue-600 hover:bg-blue-500 text-white rounded text-sm transition-colors">查询</button>
          </div>
        </div>

        <table class="w-full text-left border-collapse">
          <thead>
            <tr class="text-gray-400 text-sm border-b border-white/10 bg-white/5">
              <th class="p-3 font-medium">物资名称</th>
              <th class="p-3 font-medium">规格型号</th>
              <th class="p-3 font-medium">类别</th>
              <th class="p-3 font-medium text-right">库存数量</th>
              <th class="p-3 font-medium text-center">状态</th>
              <th class="p-3 font-medium text-right">操作</th>
            </tr>
          </thead>
          <tbody class="text-sm text-gray-300 divide-y divide-white/5">
            <tr v-for="item in inventory" :key="item.id" class="hover:bg-white/5 transition-colors group">
              <td class="p-3 font-medium text-white">{{ item.name }}</td>
              <td class="p-3">{{ item.spec }}</td>
              <td class="p-3">
                <span class="px-2 py-0.5 rounded-full bg-gray-700 text-gray-300 text-xs">{{ item.category }}</span>
              </td>
              <td class="p-3 text-right font-mono text-blue-300">{{ item.quantity }} {{ item.unit }}</td>
              <td class="p-3 text-center">
                <span class="px-2 py-0.5 rounded text-xs" 
                  :class="item.status === '充足' ? 'bg-green-900/50 text-green-400' : 'bg-blue-900/50 text-blue-400'">
                  {{ item.status }}
                </span>
              </td>
              <td class="p-3 text-right">
                <button class="text-blue-400 hover:text-blue-300 text-xs px-2 py-1 rounded hover:bg-blue-900/30 border border-transparent hover:border-blue-500/30">
                  预调拨
                </button>
              </td>
            </tr>
          </tbody>
        </table>
      </div>

      <!-- Footer -->
      <div class="p-4 border-t border-white/10 bg-gray-900/80 flex justify-end gap-3">
        <button @click="closeModal" class="px-4 py-2 bg-gray-700 hover:bg-gray-600 text-white rounded text-sm transition-colors">
          关闭
        </button>
        <button class="px-4 py-2 bg-blue-600 hover:bg-blue-500 text-white rounded text-sm transition-colors shadow-lg shadow-blue-500/20">
          导出清单
        </button>
      </div>
    </div>
  </div>
</template>

<style scoped>
.animate-scale-in {
  animation: scaleIn 0.2s ease-out;
}

@keyframes scaleIn {
  from { opacity: 0; transform: scale(0.95); }
  to { opacity: 1; transform: scale(1); }
}
</style>
