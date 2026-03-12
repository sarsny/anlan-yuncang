<script setup>
import { ref, nextTick } from 'vue';

const emit = defineEmits(['highlight-route']);
const isOpen = ref(false);
const showTooltip = ref(false);
const inputMessage = ref('');
const messages = ref([
  { id: 1, type: 'ai', text: '您好！我是您的应急资源数智参谋。当前已锁定社会化物资 65 万件。' },
  { id: 2, type: 'ai', text: '检测到近期进入汛期，建议预调拨极邦仓的防汛沙袋至闸弄口街道前置点。' }
]);
const messagesContainer = ref(null);

const toggleChat = () => {
  isOpen.value = !isOpen.value;
  showTooltip.value = false;
  if (isOpen.value) {
    scrollToBottom();
  }
};

const scrollToBottom = async () => {
  await nextTick();
  if (messagesContainer.value) {
    messagesContainer.value.scrollTop = messagesContainer.value.scrollHeight;
  }
};

const sendMessage = () => {
  if (!inputMessage.value.trim()) return;

  // User message
  messages.value.push({
    id: Date.now(),
    type: 'user',
    text: inputMessage.value
  });

  const userText = inputMessage.value;
  inputMessage.value = '';
  scrollToBottom();

  // Mock AI response
  setTimeout(() => {
    let responseText = '收到您的指令。正在分析全域资源...';
    
    if (userText.includes('调拨') || userText.includes('方案')) {
      responseText = '已为您生成最优调拨方案：建议从【极邦应急仓】调拨 500 件物资，预计 15 分钟送达。';
      // Trigger map highlight
      emit('highlight-route');
    } else if (userText.includes('库存')) {
      responseText = '当前上城区政府库库存充足，社会化协议库存占比 79%。';
    }

    messages.value.push({
      id: Date.now() + 1,
      type: 'ai',
      text: responseText
    });
    scrollToBottom();
  }, 1000);
};

const handleHighlight = () => {
  emit('highlight-route');
};
</script>

<template>
  <div class="fixed bottom-8 right-8 z-50 flex flex-col items-end font-sans">
    <!-- Chat Window -->
    <div v-if="isOpen" class="mb-4 w-96 h-[500px] bg-gray-900/95 backdrop-blur-xl border border-cyan-500/30 rounded-2xl shadow-2xl flex flex-col overflow-hidden animate-fade-in-up">
      <!-- Header -->
      <div class="flex justify-between items-center p-4 border-b border-white/10 bg-gradient-to-r from-gray-900 to-gray-800">
        <div class="flex items-center gap-2">
          <div class="w-2 h-2 rounded-full bg-green-500 animate-pulse"></div>
          <h3 class="text-cyan-400 font-bold tracking-wide">数智参谋</h3>
        </div>
        <button @click="toggleChat" class="text-gray-400 hover:text-white transition-colors">
          <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor">
            <path fill-rule="evenodd" d="M4.293 4.293a1 1 0 011.414 0L10 8.586l4.293-4.293a1 1 0 111.414 1.414L11.414 10l4.293 4.293a1 1 0 01-1.414 1.414L10 11.414l-4.293 4.293a1 1 0 01-1.414-1.414L8.586 10 4.293 5.707a1 1 0 010-1.414z" clip-rule="evenodd" />
          </svg>
        </button>
      </div>

      <!-- Messages Area -->
      <div ref="messagesContainer" class="flex-1 overflow-y-auto p-4 space-y-4 custom-scrollbar bg-black/20">
        <div v-for="msg in messages" :key="msg.id" class="flex" :class="msg.type === 'user' ? 'justify-end' : 'justify-start'">
          <div 
            class="max-w-[80%] p-3 rounded-lg text-sm leading-relaxed shadow-sm"
            :class="msg.type === 'user' ? 'bg-blue-600 text-white rounded-br-none' : 'bg-gray-800 text-gray-200 border border-white/5 rounded-bl-none'"
          >
            {{ msg.text }}
          </div>
        </div>
      </div>

      <!-- Quick Actions -->
      <div class="px-4 py-2 border-t border-white/5 bg-gray-900/50 flex gap-2 overflow-x-auto">
        <button @click="handleHighlight" class="whitespace-nowrap px-3 py-1.5 bg-cyan-900/30 border border-cyan-500/30 text-cyan-300 text-xs rounded-full hover:bg-cyan-900/50 transition-colors">
          生成调拨方案
        </button>
        <button class="whitespace-nowrap px-3 py-1.5 bg-purple-900/30 border border-purple-500/30 text-purple-300 text-xs rounded-full hover:bg-purple-900/50 transition-colors">
          库存预警分析
        </button>
      </div>

      <!-- Input Area -->
      <div class="p-4 bg-gray-900 border-t border-white/10">
        <div class="flex items-center gap-2 bg-black/40 rounded-xl p-1 border border-white/10 focus-within:border-cyan-500/50 transition-colors">
          <input 
            v-model="inputMessage" 
            @keyup.enter="sendMessage"
            type="text" 
            placeholder="请输入指令，如：调拨物资..." 
            class="flex-1 bg-transparent text-sm text-white px-3 py-2 focus:outline-none placeholder-gray-600"
          >
          <button @click="sendMessage" class="p-2 bg-cyan-600 hover:bg-cyan-500 text-white rounded-lg transition-colors">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" viewBox="0 0 20 20" fill="currentColor">
              <path d="M10.894 2.553a1 1 0 00-1.788 0l-7 14a1 1 0 001.169 1.409l5-1.429A1 1 0 009 15.571V11a1 1 0 112 0v4.571a1 1 0 00.725.962l5 1.428a1 1 0 001.17-1.408l-7-14z" />
            </svg>
          </button>
        </div>
      </div>
    </div>

    <!-- Tooltip -->
    <div v-if="showTooltip && !isOpen" class="mb-2 mr-2 bg-cyan-900/90 backdrop-blur text-cyan-100 text-xs px-3 py-1.5 rounded-lg border border-cyan-500/30 whitespace-nowrap animate-fade-in shadow-lg">
      本月社会化资源调用成功率 100%
    </div>

    <!-- Floating Robot Orb -->
    <button 
      @click="toggleChat" 
      @mouseenter="showTooltip = true"
      @mouseleave="showTooltip = false"
      class="relative group"
    >
      <div class="absolute inset-0 bg-cyan-500 rounded-full blur opacity-40 group-hover:opacity-60 transition-opacity animate-pulse"></div>
      <div class="relative w-16 h-16 bg-gradient-to-br from-gray-800 to-gray-900 rounded-full flex items-center justify-center shadow-lg border-2 border-cyan-400/30 hover:scale-110 transition-transform hover:border-cyan-400">
        <!-- Robot Icon -->
        <svg xmlns="http://www.w3.org/2000/svg" class="h-9 w-9 text-cyan-400 group-hover:text-cyan-300 transition-colors" viewBox="0 0 24 24" fill="currentColor">
          <path d="M12 2a2 2 0 0 1 2 2c0 .74-.4 1.39-1 1.73V7h1a7 7 0 0 1 7 7h1a1 1 0 0 1 1 1v3a1 1 0 0 1-1 1h-1v1a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2v-1H2a1 1 0 0 1-1-1v-3a1 1 0 0 1 1-1h1a7 7 0 0 1 7-7h1V5.73c-.6-.34-1-.99-1-1.73a2 2 0 0 1 2-2M7.5 13A2.5 2.5 0 0 0 5 15.5A2.5 2.5 0 0 0 7.5 18A2.5 2.5 0 0 0 10 15.5A2.5 2.5 0 0 0 7.5 13m9 0a2.5 2.5 0 0 0-2.5 2.5a2.5 2.5 0 0 0 2.5 2.5a2.5 2.5 0 0 0 2.5-2.5a2.5 2.5 0 0 0-2.5-2.5"/>
        </svg>
      </div>
    </button>
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

.animate-fade-in-up {
  animation: fadeInUp 0.3s ease-out;
}

.animate-fade-in {
  animation: fadeIn 0.2s ease-out;
}

@keyframes fadeInUp {
  from {
    opacity: 0;
    transform: translateY(20px) scale(0.95);
  }
  to {
    opacity: 1;
    transform: translateY(0) scale(1);
  }
}

@keyframes fadeIn {
  from { opacity: 0; }
  to { opacity: 1; }
}
</style>
