<template>
  <!-- Floating Device Info -->
  <q-card v-if="selectedDevice" class="floating-device-card" elevated>
    <div class="device-card-header">
      <div class="device-title">
        <h4>{{ selectedDevice.name }}</h4>
        <p>Device ID: {{ selectedDevice.id }}</p>
      </div>
      <q-btn round flat dense icon="close" @click="$emit('close-device')" />
    </div>

    <div class="device-reading">
      <span class="reading-label">Current Reading</span>
      <span class="reading-value"
        >{{ selectedDevice.value }} {{ selectedDevice.unit }}</span
      >
    </div>

    <div class="device-actions">
      <q-btn outline label="View Details" class="action-btn" />
      <q-btn label="Configure" color="primary" class="action-btn" />
    </div>
  </q-card>

  <!-- Top Status Bar -->
  <q-card class="status-bar" elevated>
    <div class="status-content">
      <div class="status-indicator">
        <q-icon name="wifi" color="positive" />
        <span>All Systems Online</span>
      </div>
      <div class="status-time">
        {{ currentTime }}
      </div>
    </div>
  </q-card>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from "vue";

defineProps({
  selectedDevice: Object
});

defineEmits(['close-device']);

const currentTime = ref("");

onMounted(() => {
  currentTime.value = new Date().toLocaleTimeString();
  const timeInterval = setInterval(() => {
    currentTime.value = new Date().toLocaleTimeString();
  }, 1000);

  onUnmounted(() => {
    clearInterval(timeInterval);
  });
});
</script>

<style scoped>
/* Status Bar */
.status-bar {
  position: absolute;
  top: 20px;
  right: 20px;
  background: var(--bg-primary);
  backdrop-filter: blur(20px);
  border: 1px solid var(--border-color);
  border-radius: 8px;
  z-index: 1000;
}

.status-content {
  display: flex;
  align-items: center;
  gap: 16px;
  padding: 8px 16px;
}

.status-indicator {
  display: flex;
  align-items: center;
  gap: 8px;
  color: white;
  font-size: 14px;
}

.status-time {
  color: #9ca3af;
  font-size: 14px;
}

/* Floating Device Card */
.floating-device-card {
  position: absolute;
  bottom: 20px;
  right: 20px;
  width: 320px;
  background: var(--bg-primary);
  backdrop-filter: blur(20px);
  -webkit-backdrop-filter: blur(20px);
  border: 1px solid var(--border-color);
  border-radius: 16px;
  z-index: 1000;
  box-shadow: 0 15px 35px var(--shadow-color);
}

.device-card-header {
  display: flex;
  align-items: flex-start;
  justify-content: space-between;
  padding: 20px 20px 0;
}

.device-title h4 {
  margin: 0 0 6px 0;
  color: var(--text-primary);
  font-size: 16px;
  font-weight: 600;
  text-align: left;
}

.device-title p {
  margin: 0;
  color: var(--text-secondary);
  font-size: 13px;
  font-weight: 500;
  text-align: left;
}

.device-reading {
  padding: 20px;
  background: var(--accent-gradient);
  margin: 16px 20px;
  border-radius: 12px;
  text-align: center;
}

.reading-label {
  display: block;
  color: rgba(255, 255, 255, 0.9);
  font-size: 14px;
  font-weight: 500;
  margin-bottom: 6px;
}

.reading-value {
  display: block;
  color: white;
  font-size: 24px;
  font-weight: 700;
  letter-spacing: -0.02em;
}

.device-actions {
  display: flex;
  gap: 8px;
  padding: 0 20px 20px;
}

.action-btn {
  flex: 1;
  border-radius: 10px;
  height: 40px;
  font-weight: 600;
}
</style>
