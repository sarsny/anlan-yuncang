<script setup>
import { ref } from 'vue';
import Header from './components/Header.vue';
import LeftPanel from './components/LeftPanel.vue';
import CenterPanel from './components/CenterPanel.vue';
import RightPanel from './components/RightPanel.vue';
import AIAssistant from './components/AIAssistant.vue';
import EnterpriseDetailModal from './components/EnterpriseDetailModal.vue';

const centerPanelRef = ref(null);
const isModalOpen = ref(false);
const modalEnterprise = ref({});

const handleHighlightRoute = () => {
  if (centerPanelRef.value) {
    centerPanelRef.value.highlightRoute();
  }
};

const handleOpenModal = (enterprise) => {
  modalEnterprise.value = enterprise;
  isModalOpen.value = true;
};
</script>

<template>
  <div class="h-screen w-screen bg-[#0a0a0a] text-white overflow-hidden flex flex-col font-sans">
    <!-- Header -->
    <Header />

    <!-- Main Content -->
    <main class="flex-1 p-4 grid grid-cols-4 gap-4 h-[calc(100vh-64px)]">
      <!-- Left Column (25%) -->
      <div class="col-span-1 h-full">
        <LeftPanel @open-modal="handleOpenModal" />
      </div>

      <!-- Center Column (50%) -->
      <div class="col-span-2 h-full">
        <CenterPanel ref="centerPanelRef" @open-modal="handleOpenModal" />
      </div>

      <!-- Right Column (25%) -->
      <div class="col-span-1 h-full">
        <RightPanel />
      </div>
    </main>

    <!-- AI Assistant -->
    <AIAssistant @highlight-route="handleHighlightRoute" />

    <!-- Detail Modal -->
    <EnterpriseDetailModal 
      :isOpen="isModalOpen" 
      :enterprise="modalEnterprise" 
      @close="isModalOpen = false" 
    />
  </div>
</template>

<style>
/* Global scrollbar styling if needed */
::-webkit-scrollbar {
  width: 6px;
  height: 6px;
}
::-webkit-scrollbar-track {
  background: #0a0a0a;
}
::-webkit-scrollbar-thumb {
  background: #333;
  border-radius: 3px;
}
::-webkit-scrollbar-thumb:hover {
  background: #555;
}
</style>
