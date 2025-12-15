<template>
  <div class="route-dashboard-container">
    <!-- هدر سفارشی -->
    <div class="route-header">
      <button class="back-button" @click="goBack">
        <!-- ایکون برگشت (فلش چپ) -->
        <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960" width="24px" fill="currentColor">
          <path d="M400-80 0-480l400-400 71 71-329 329 329 329-71 71Z"/>
        </svg>
      </button>
      
      <!-- اطلاعات دستگاه -->
      <div class="device-header-info" v-if="device">
        <!-- 🔥 مارکر دستگاه با وضعیت -->
        <div class="device-marker-container" :class="getDeviceStatus(device)">
          <div class="device-marker-icon">
            <!-- ایکون وضعیت دستگاه -->
            <svg 
              v-if="getDeviceStatus(device) === 'online-moving'"
              xmlns="http://www.w3.org/2000/svg" 
              height="20px" 
              viewBox="0 -960 960 960" 
              width="20px" 
              fill="#1db4f9"
            >
              <path d="M480-480q33 0 56.5-23.5T560-560q0-33-23.5-56.5T480-640q-33 0-56.5 23.5T400-560q0 33 23.5 56.5T480-480Zm0 294q122-112 181-203.5T720-552q0-109-69.5-178.5T480-800q-101 0-170.5 69.5T240-552q0 71 59 162.5T480-186Zm0 106Q319-217 239.5-334.5T160-552q0-150 96.5-239T480-880q127 0 223.5 89T800-552q0 100-79.5 217.5T480-80Zm0-480Z"/>
            </svg>
            
            <svg 
              v-else-if="getDeviceStatus(device) === 'online-stopped'"
              xmlns="http://www.w3.org/2000/svg" 
              height="20px" 
              viewBox="0 -960 960 960" 
              width="20px" 
              fill="#ff6b30"
            >
              <path d="M480-480q33 0 56.5-23.5T560-560q0-33-23.5-56.5T480-640q-33 0-56.5 23.5T400-560q0 33 23.5 56.5T480-480Zm0 294q122-112 181-203.5T720-552q0-109-69.5-178.5T480-800q-101 0-170.5 69.5T240-552q0 71 59 162.5T480-186Zm0 106Q319-217 239.5-334.5T160-552q0-150 96.5-239T480-880q127 0 223.5 89T800-552q0 100-79.5 217.5T480-80Zm0-480Z"/>
            </svg>
            
            <svg 
              v-else
              xmlns="http://www.w3.org/2000/svg" 
              height="18px" 
              viewBox="0 -960 960 960" 
              width="18px" 
              fill="#ffffff"
            >
              <path d="M560-560q0-33-23.5-56.5T480-640q-10 0-19 2t-17 7l107 107q5-8 7-17t2-19Zm168 213-58-58q25-42 37.5-78.5T720-552q0-109-69.5-178.5T480-800q-44 0-82.5 13.5T328-747l-57-57q43-37 97-56.5T480-880q127 0 223.5 89T800-552q0 48-18 98.5T728-347Zm-157 71L244-603q-2 12-3 25t-1 26q0 71 59 162.5T480-186q26-23 48.5-45.5T571-276ZM819-28 627-220q-32 34-68 69t-79 71Q319-217 239.5-334.5T160-552q0-32 5-61t14-55L27-820l57-57L876-85l-57 57ZM408-439Zm91-137Z"/>
            </svg>
          </div>
        </div>
        
        <div class="device-details">
          <div class="device-serial">Serial: {{ device.serialnumber || 'N/A' }}</div>
          <div class="device-last-seen">{{ device.lastSeen || 'Just now' }}</div>
        </div>
      </div>
      
      <div class="header-spacer"></div>
    </div>

    <!-- نقشه کامل‌صفحه -->
    <MapContainer 
      v-if="device && device.deviceid"
      :devices="[device]" 
      :selected-device-id="device.deviceid"
      @device-selected="handleDeviceSelected"
    />
    <div v-else class="no-device">
      <p>No device selected</p>
      <button @click="goBack">Go Back</button>
    </div>
  </div>
</template>

<script setup>
import {  watch } from 'vue'
import MapContainer from '../MapContainer/MapContainer.vue'

// دریافت دستگاه از props
const props = defineProps({
  device: {
    type: Object,
    default: null
  }
})

const emit = defineEmits(['go-back'])

// 🔥 تابع تشخیص وضعیت دستگاه (مشابه MapContainer)
const getDeviceStatus = (device) => {
  if (!device.position) return "offline";

  const now = Math.floor(Date.now() / 1000);
  const lastUpdate = device.position.unixtime;
  const halfMinutes = 1800;

  if (now - lastUpdate > halfMinutes) {
    return "offline";
  }
  if (device.position.flags?.isStop) {
    return "online-stopped";
  } else {
    return "online-moving";
  }
};

// وقتی prop device تغییر کرد، deviceInfo رو آپدیت کن
watch(() => props.device, (newDevice) => {
  if (newDevice) {
    console.log('Device status:', getDeviceStatus(newDevice)); // برای دیباگ
  }
}, { immediate: true })

const goBack = () => {
  emit('go-back')
}

const handleDeviceSelected = (selectedDevice) => {
  if (selectedDevice) {
    // در این صفحه فقط یک دستگاه نمایش داده می‌شود
    // اگر نیاز به آپدیت دستگاه داشتیم، می‌توانیم emit کنیم
  }
}
</script>

<style scoped>
.route-dashboard-container {
  width: 100%;
  height: 100%;
  position: relative;
  background: #000000;
}

.route-header {
  position: absolute;
  top: 10px;
  left: 10px;
  right: 10px;
  height: 70px;
  background: linear-gradient(135deg, #020407 0%, #000000 100%);
  backdrop-filter: blur(20px);
  -webkit-backdrop-filter: blur(20px);
  border-radius: 10px;
  border: 1px solid rgba(255, 255, 255, 0.1);
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 0 20px;
  z-index: 1000;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.3);
}

.back-button {
  background: rgba(255, 255, 255, 0.1);
  border: none;
  border-radius: 50%;
  width: 40px;
  height: 40px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: all 0.2s ease;
  color: white;
}

.back-button:hover {
  background: rgba(255, 255, 255, 0.2);
  transform: scale(1.05);
}

.back-button svg {
  width: 24px;
  height: 24px;
}

.device-header-info {
  display: flex;
  align-items: center;
  gap: 12px;
  flex: 1;
  margin-left: 20px;
}

/* 🔥 استایل جدید برای مارکر دستگاه */
.device-marker-container {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 40px;
  height: 40px;
  border-radius: 50%;
  background: rgba(0, 0, 0, 0.8);
  position: relative;
  border: 3px solid;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.4), 0 0 0 2px rgba(255, 255, 255, 0.1);
}

/* 🔥 وضعیت دستگاه‌های متحرک */
.device-marker-container.online-moving {
  border-color: #1db4f9;
  animation: pulse-moving 2s infinite;
}

/* 🔥 وضعیت دستگاه‌های متوقف */
.device-marker-container.online-stopped {
  border-color: #ff6b30;
  animation: pulse-stopped 2s infinite;
}

/* 🔥 وضعیت دستگاه‌های آفلاین */
.device-marker-container.offline {
  border-color: #ffffff;
}

.device-marker-icon {
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 50%;
  width: 24px;
  height: 24px;
  background: linear-gradient(135deg, #1f1f1f, #141414);
}

.device-marker-icon svg {
  filter: drop-shadow(0 1px 2px rgba(0, 0, 0, 0.4));
}

.device-details {
  display: flex;
  flex-direction: column;
}

.device-serial {
  font-size: 14px;
  font-weight: 600;
  color: white;
}

.device-last-seen {
  font-size: 12px;
  color: rgba(255, 255, 255, 0.7);
}

.header-spacer {
  width: 40px;
}

.no-device {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  color: white;
  text-align: center;
}

.no-device button {
  margin-top: 20px;
  padding: 10px 20px;
  background: #ff6b30;
  border: none;
  border-radius: 5px;
  color: white;
  cursor: pointer;
}

/* 🔥 انیمیشن‌ها برای مارکر */
@keyframes pulse-moving {
  0% {
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.4), 
                0 0 0 2px rgba(255, 255, 255, 0.1),
                0 0 0 0 rgba(29, 180, 249, 0.4);
  }
  70% {
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.4), 
                0 0 0 2px rgba(255, 255, 255, 0.1),
                0 0 0 10px rgba(29, 180, 249, 0);
  }
  100% {
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.4), 
                0 0 0 2px rgba(255, 255, 255, 0.1),
                0 0 0 0 rgba(29, 180, 249, 0);
  }
}

@keyframes pulse-stopped {
  0% {
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.4), 
                0 0 0 2px rgba(255, 255, 255, 0.1),
                0 0 0 0 rgba(255, 107, 48, 0.4);
  }
  70% {
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.4), 
                0 0 0 2px rgba(255, 255, 255, 0.1),
                0 0 0 10px rgba(255, 107, 48, 0);
  }
  100% {
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.4), 
                0 0 0 2px rgba(255, 255, 255, 0.1),
                0 0 0 0 rgba(255, 107, 48, 0);
  }
}

/* استایل‌های ریسپانسیو */
@media (max-width: 768px) {
  .route-header {
    left: 5px;
    right: 5px;
    padding: 0 15px;
    height: 60px;
  }
  
  .device-marker-container {
    width: 36px;
    height: 36px;
  }
  
  .device-marker-icon {
    width: 22px;
    height: 22px;
  }
  
  .device-marker-icon svg {
    width: 16px;
    height: 16px;
  }
  
  .device-serial {
    font-size: 13px;
  }
  
  .device-last-seen {
    font-size: 11px;
  }
}

@media (max-width: 480px) {
  .device-header-info {
    gap: 8px;
    margin-left: 10px;
  }
  
  .device-marker-container {
    width: 32px;
    height: 32px;
    border-width: 2px;
  }
  
  .device-marker-icon {
    width: 20px;
    height: 20px;
  }
  
  .device-marker-icon svg {
    width: 14px;
    height: 14px;
  }
  
  .device-details {
    min-width: 0;
  }
  
  .device-serial {
    font-size: 12px;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
    max-width: 120px;
  }
  
  .device-last-seen {
    font-size: 10px;
  }
}
</style>