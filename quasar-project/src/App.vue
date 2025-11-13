<template>
  <!-- کانتینر اصلی با قاب و پدینگ -->
  <div class="main-frame">
    <div class="dashboard-container" :class="themeClass">
      <!-- نقشه -->
      <MapContainer @device-selected="handleDeviceSelected" />

      <!-- منوی داشبورد -->
      <DashboardMenu
        :is-dark-mode="isDarkMode"
        @toggle-dark-mode="isDarkMode = $event"
      />

      <!-- کارت های شناور -->
      <FloatingCards
        :selected-device="selectedDevice"
        @close-device="selectedDevice = null"
      />
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from "vue";
import MapContainer from './components/MapContainer/MapContainer.vue';
import DashboardMenu from './components/DashboardMenu/DashboardMenu.vue';
import FloatingCards from './components/FloatingCards/FloatingCards.vue';

// Reactive state
const selectedDevice = ref(null);
const isDarkMode = ref(true);

// Computed property برای تم
const themeClass = computed(() => ({
  "dark-theme": isDarkMode.value,
  "light-theme": !isDarkMode.value,
}));

// Methods
const handleDeviceSelected = (device) => {
  selectedDevice.value = device;
};
</script>

<style>
/* فونت مدرن */
@import url("https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap");

/* متغیرهای تم */
.dark-theme {
  --bg-primary: rgba(255, 255, 255, 0.201);
  --bg-secondary: rgba(156, 156, 156, 0.9);
  --bg-accent: rgba(162, 166, 168, 0.1);
  --text-primary: #f8fafc;
  --text-secondary: #cbd5e1;
  --text-muted: #64748b;
  --accent-color: #3b82f6;
  --accent-gradient: linear-gradient(135deg, #020407 0%, #000000 100%);
  --border-color: rgba(255, 255, 255, 0.1);
  --shadow-color: rgba(0, 0, 0, 0.3);
}

.light-theme {
  --bg-primary: rgba(255, 255, 255, 0.95);
  --bg-secondary: rgba(248, 250, 252, 0.9);
  --bg-accent: rgba(59, 130, 246, 0.05);
  --text-primary: #1e293b;
  --text-secondary: #475569;
  --text-muted: #64748b;
  --accent-color: #2563eb;
  --accent-gradient: linear-gradient(135deg, #2563eb 0%, #1d4ed8 100%);
  --border-color: rgba(0, 0, 0, 0.1);
  --shadow-color: rgba(0, 0, 0, 0.1);
}

/* قاب اصلی با پدینگ */
.main-frame {
  width: 100%;
  height: 100vh;
  background: linear-gradient(135deg, #063950 0%, #0a0a0a 100%);
  display: flex;
  align-items: center;
  justify-content: center;
}

.dashboard-container {
  width: 100%;
  height: 100%;
  position: relative;
  background: white;
  box-shadow: 0 25px 50px rgba(0, 0, 0, 0.25), 0 0 0 1px rgba(255, 255, 255, 0.1),
    inset 0 1px 0 rgba(255, 255, 255, 0.2);
  overflow: hidden;
  font-family: "Inter", sans-serif;
}
</style>
