<script setup>
import { ref, onMounted, onUnmounted } from 'vue';

const currentTime = ref('');
const weather = ref('晴 24°C');
const status = ref('绿色');

const updateTime = () => {
  const now = new Date();
  currentTime.value = now.toLocaleString('zh-CN', {
    year: 'numeric',
    month: '2-digit',
    day: '2-digit',
    hour: '2-digit',
    minute: '2-digit',
    second: '2-digit',
    hour12: false
  });
};

let timer;
onMounted(() => {
  updateTime();
  timer = setInterval(updateTime, 1000);
});

onUnmounted(() => {
  clearInterval(timer);
});
</script>

<template>
  <header class="w-full h-16 bg-black/30 backdrop-blur-md border-b border-white/10 flex items-center justify-between px-6 z-50">
    <div class="flex items-center space-x-4">
      <div class="text-sm text-gray-400">{{ currentTime }}</div>
      <div class="text-sm text-gray-400">{{ weather }}</div>
    </div>
    
    <h1 class="text-2xl font-bold bg-gradient-to-r from-blue-400 to-cyan-300 bg-clip-text text-transparent tracking-widest">
      上城区应急云仓数字化指挥中心
    </h1>

    <div class="flex items-center">
      <div class="flex items-center space-x-2 bg-green-900/30 border border-green-500/50 px-3 py-1 rounded-full">
        <span class="w-2 h-2 rounded-full bg-green-500 animate-pulse"></span>
        <span class="text-green-400 text-sm font-medium">当前响应等级：{{ status }}</span>
      </div>
    </div>
  </header>
</template>
