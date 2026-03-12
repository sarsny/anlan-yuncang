<script setup>
const props = defineProps({
  isOpen: Boolean,
  source: String
});

const emit = defineEmits(['close']);

const closeModal = () => {
  emit('close');
};
</script>

<template>
  <div v-if="isOpen" class="fixed inset-0 z-[100] flex items-center justify-center p-4">
    <!-- Backdrop -->
    <div class="absolute inset-0 bg-black/90 backdrop-blur-sm" @click="closeModal"></div>
    
    <!-- Modal Content -->
    <div class="relative w-full max-w-3xl bg-gray-900 border border-white/20 rounded-xl shadow-2xl overflow-hidden animate-zoom-in">
      <div class="bg-black p-3 border-b border-white/10 flex justify-between items-center">
        <div class="flex items-center gap-2">
          <div class="w-3 h-3 rounded-full bg-red-500 animate-pulse"></div>
          <h3 class="text-white font-mono text-sm">LIVE MONITORING - {{ source }}</h3>
        </div>
        <button @click="closeModal" class="text-gray-400 hover:text-white">
          <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
          </svg>
        </button>
      </div>
      
      <!-- Video Placeholder -->
      <div class="aspect-video bg-black relative flex items-center justify-center overflow-hidden">
        <!-- Mock Grid Overlay -->
        <div class="absolute inset-0 grid grid-cols-4 grid-rows-4 pointer-events-none opacity-20">
          <div v-for="i in 16" :key="i" class="border border-white/30"></div>
        </div>
        
        <!-- Placeholder Content -->
        <div class="text-center">
          <svg xmlns="http://www.w3.org/2000/svg" class="h-16 w-16 text-gray-700 mx-auto mb-2" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1" d="M15 10l4.553-2.276A1 1 0 0121 8.618v6.764a1 1 0 01-1.447.894L15 14M5 18h8a2 2 0 002-2V8a2 2 0 00-2-2H5a2 2 0 00-2 2v8a2 2 0 002 2z" />
          </svg>
          <div class="text-gray-500 font-mono text-sm">SIGNAL ESTABLISHED</div>
          <div class="text-xs text-gray-700 mt-1">CAMERA_ID: CAM-0042-A</div>
        </div>

        <!-- Timestamp -->
        <div class="absolute top-4 right-4 text-green-500 font-mono text-xs bg-black/50 px-2 py-1 rounded">
          REC ● {{ new Date().toLocaleTimeString() }}
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.animate-zoom-in {
  animation: zoomIn 0.2s ease-out;
}

@keyframes zoomIn {
  from { opacity: 0; transform: scale(0.9); }
  to { opacity: 1; transform: scale(1); }
}
</style>
