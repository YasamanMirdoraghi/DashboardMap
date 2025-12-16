<template>
  <div class="route-dashboard-container">
    <div class="route-header">
      <button class="back-button" @click="goBack">
        <svg
          xmlns="http://www.w3.org/2000/svg"
          height="24px"
          viewBox="0 -960 960 960"
          width="24px"
          fill="currentColor"
        >
          <path d="M400-80 0-480l400-400 71 71-329 329 329 329-71 71Z" />
        </svg>
      </button>

      <div class="device-header-info" v-if="device">
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
              <path
                d="M480-480q33 0 56.5-23.5T560-560q0-33-23.5-56.5T480-640q-33 0-56.5 23.5T400-560q0 33 23.5 56.5T480-480Zm0 294q122-112 181-203.5T720-552q0-109-69.5-178.5T480-800q-101 0-170.5 69.5T240-552q0 71 59 162.5T480-186Zm0 106Q319-217 239.5-334.5T160-552q0-150 96.5-239T480-880q127 0 223.5 89T800-552q0 100-79.5 217.5T480-80Zm0-480Z"
              />
            </svg>

            <svg
              v-else-if="getDeviceStatus(device) === 'online-stopped'"
              xmlns="http://www.w3.org/2000/svg"
              height="20px"
              viewBox="0 -960 960 960"
              width="20px"
              fill="#ff6b30"
            >
              <path
                d="M480-480q33 0 56.5-23.5T560-560q0-33-23.5-56.5T480-640q-33 0-56.5 23.5T400-560q0 33 23.5 56.5T480-480Zm0 294q122-112 181-203.5T720-552q0-109-69.5-178.5T480-800q-101 0-170.5 69.5T240-552q0 71 59 162.5T480-186Zm0 106Q319-217 239.5-334.5T160-552q0-150 96.5-239T480-880q127 0 223.5 89T800-552q0 100-79.5 217.5T480-80Zm0-480Z"
              />
            </svg>

            <svg
              v-else
              xmlns="http://www.w3.org/2000/svg"
              height="18px"
              viewBox="0 -960 960 960"
              width="18px"
              fill="#ffffff"
            >
              <path
                d="M560-560q0-33-23.5-56.5T480-640q-10 0-19 2t-17 7l107 107q5-8 7-17t2-19Zm168 213-58-58q25-42 37.5-78.5T720-552q0-109-69.5-178.5T480-800q-44 0-82.5 13.5T328-747l-57-57q43-37 97-56.5T480-880q127 0 223.5 89T800-552q0 48-18 98.5T728-347Zm-157 71L244-603q-2 
                12-3 25t-1 26q0 71 59 162.5T480-186q26-23 48.5-45.5T571-276ZM819-28 627-220q-32 34-68 69t-79 71Q319-217 239.5-334.5T160-552q0-32 5-61t14-55L27-820l57-57L876-85l-57 57ZM408-439Zm91-137Z"
              />
            </svg>
          </div>
        </div>

        <div class="device-details">
          <div class="device-serial">Serial: {{ device.serialnumber || "N/A" }}</div>
          <div class="device-last-seen">{{ device.lastSeen || "Just now" }}</div>
        </div>
      </div>

      <div class="header-spacer"></div>
    </div>

    <!-- نقشه -->
    <div ref="mapContainer" class="map-container"></div>

  </div>
</template>

<script setup>
import { ref, onMounted, watch } from "vue";
import L from "leaflet";
import "leaflet/dist/leaflet.css";

const props = defineProps({
  device: {
    type: Object,
    default: null,
  },
});

const emit = defineEmits(["go-back"]);

// عناصر نقشه
const mapContainer = ref(null);
let map = null;
let routeSegments = []; 
let pointMarkers = []; 


const routeData = ref([
  {
    alarm: "Unknown Alarm: 230.",
    alarm_code: 230,
    batt_v: 3023,
    bearing: 51,
    cid: 61899,
    flags: {
      isCIDValid: true,
      isCharging: false,
      isCoilActive: false,
      isFixed: false,
      isMechanicClosed: true,
      isRopeClosed: true,
      isSimTwo: false,
      isStop: true,
      isUnlockAllowed: false,
    },
    geofenceStatus: 256,
    gsm_sig: 94,
    humidity: 24,
    id: 1381,
    lac: 42218,
    latitude: 35.623432,
    longitude: 60.41451,
    pdop: 1.3,
    positionUnixtime: 1765190776,
    satellites: 8,
    speed: 26,
    technology: "4G",
    temperature: 19,
    unixtime: 1765203376,
  },
  {
    alarm: "Unknown Alarm: 231.",
    alarm_code: 231,
    batt_v: 3010,
    bearing: 257,
    cid: 61899,
    flags: {
      isCIDValid: true,
      isCharging: false,
      isCoilActive: false,
      isFixed: true,
      isMechanicClosed: true,
      isRopeClosed: true,
      isSimTwo: false,
      isStop: true,
      isUnlockAllowed: false,
    },
    geofenceStatus: 256,
    gsm_sig: 94,
    humidity: 24,
    id: 1380,
    lac: 42218,
    latitude: 36.623386,
    longitude: 57.414528,
    pdop: 1.34,
    positionUnixtime: 1765190045,
    satellites: 8,
    speed: 36,
    technology: "4G",
    temperature: 19,
    unixtime: 1765202645,
  },
  {
    alarm: "Unknown Alarm: 230.",
    alarm_code: 230,
    batt_v: 3010,
    bearing: 257,
    cid: 61899,
    flags: {
      isCIDValid: true,
      isCharging: false,
      isCoilActive: false,
      isFixed: false,
      isMechanicClosed: true,
      isRopeClosed: true,
      isSimTwo: false,
      isStop: true,
      isUnlockAllowed: false,
    },
    geofenceStatus: 256,
    gsm_sig: 94,
    humidity: 24,
    id: 1379,
    lac: 42218,
    latitude: 35.623367,
    longitude: 58.414593,
    pdop: 1.58,
    positionUnixtime: 1765190041,
    satellites: 6,
    speed: 45,
    technology: "4G",
    temperature: 19,
    unixtime: 1765202641,
  },
  {
    alarm: "Unknown Alarm: 221.",
    alarm_code: 221,
    batt_v: 2991,
    bearing: 257,
    cid: 61899,
    flags: {
      isCIDValid: true,
      isCharging: false,
      isCoilActive: false,
      isFixed: false,
      isMechanicClosed: true,
      isRopeClosed: true,
      isSimTwo: false,
      isStop: true,
      isUnlockAllowed: false,
    },
    geofenceStatus: 256,
    gsm_sig: 91,
    humidity: 24,
    id: 1378,
    lac: 42218,
    latitude: 35.6233,
    longitude: 59.414516,
    pdop: 1.24,
    positionUnixtime: 1765189004,
    satellites: 9,
    speed: 1,
    technology: "4G",
    temperature: 21,
    unixtime: 1765201970,
  },
  {
    alarm: "STOP DETECTED",
    alarm_code: 207,
    batt_v: 2965,
    bearing: 257,
    cid: 61899,
    flags: {
      isCIDValid: true,
      isCharging: false,
      isCoilActive: false,
      isFixed: true,
      isMechanicClosed: true,
      isRopeClosed: true,
      isSimTwo: false,
      isStop: true,
      isUnlockAllowed: false,
    },
    geofenceStatus: 256,
    gsm_sig: 97,
    humidity: 25,
    id: 1376,
    lac: 42218,
    latitude: 33.623318,
    longitude: 55.414482,
    pdop: 1.33,
    positionUnixtime: 1765188882,
    satellites: 8,
    speed: 66,
    technology: "4G",
    temperature: 22,
    unixtime: 1765201482,
  },
  {
    alarm: "STOP DETECTED",
    alarm_code: 207,
    batt_v: 2965,
    bearing: 257,
    cid: 61899,
    flags: {
      isCIDValid: true,
      isCharging: false,
      isCoilActive: false,
      isFixed: true,
      isMechanicClosed: true,
      isRopeClosed: true,
      isSimTwo: false,
      isStop: true,
      isUnlockAllowed: false,
    },
    geofenceStatus: 256,
    gsm_sig: 97,
    humidity: 25,
    id: 1377,
    lac: 42218,
    latitude: 36.623318,
    longitude: 51.414482,
    pdop: 1.33,
    positionUnixtime: 1765188882,
    satellites: 8,
    speed: 55,
    technology: "4G",
    temperature: 22,
    unixtime: 1765201482,
  },
  {
    alarm: "Unknown Alarm: 228.",
    alarm_code: 228,
    batt_v: 2971,
    bearing: 47,
    cid: 61899,
    flags: {
      isCIDValid: true,
      isCharging: false,
      isCoilActive: false,
      isFixed: true,
      isMechanicClosed: true,
      isRopeClosed: true,
      isSimTwo: false,
      isStop: false,
      isUnlockAllowed: false,
    },
    geofenceStatus: 256,
    gsm_sig: 91,
    humidity: 25,
    id: 1375,
    lac: 42218,
    latitude: 35.623306,
    longitude: 51.414536,
    pdop: 1.63,
    positionUnixtime: 1765188783,
    satellites: 7,
    speed: 2,
    technology: "4G",
    temperature: 22,
    unixtime: 1765201383,
  },
  {
    alarm: "COURSE CHANGE DETECTED",
    alarm_code: 201,
    batt_v: 2970,
    bearing: 3,
    cid: 61899,
    flags: {
      isCIDValid: true,
      isCharging: false,
      isCoilActive: false,
      isFixed: true,
      isMechanicClosed: true,
      isRopeClosed: true,
      isSimTwo: false,
      isStop: false,
      isUnlockAllowed: false,
    },
    geofenceStatus: 256,
    gsm_sig: 91,
    humidity: 25,
    id: 1374,
    lac: 42218,
    latitude: 33.62324,
    longitude: 52.414574,
    pdop: 1.19,
    positionUnixtime: 1765188761,
    satellites: 9,
    speed: 120,
    technology: "4G",
    temperature: 22,
    unixtime: 1765201361,
  },
  {
    alarm: "COURSE CHANGE DETECTED",
    alarm_code: 201,
    batt_v: 2970,
    bearing: 3,
    cid: 61899,
    flags: {
      isCIDValid: true,
      isCharging: false,
      isCoilActive: false,
      isFixed: true,
      isMechanicClosed: true,
      isRopeClosed: true,
      isSimTwo: false,
      isStop: false,
      isUnlockAllowed: false,
    },
    geofenceStatus: 256,
    gsm_sig: 91,
    humidity: 25,
    id: 1374,
    lac: 42218,
    latitude: 31.62324,
    longitude: 52.414574,
    pdop: 1.19,
    positionUnixtime: 1765188761,
    satellites: 9,
    speed: 120,
    technology: "4G",
    temperature: 22,
    unixtime: 1765201361,
  },
]);

// تابع تشخیص وضعیت دستگاه
const getDeviceStatus = (device) => {
  if (!device.position) return "offline";
  const now = Math.floor(Date.now() / 1000);
  const lastUpdate = device.position.unixtime;
  const halfMinutes = 1800;

  if (now - lastUpdate > halfMinutes) return "offline";
  if (device.position.flags?.isStop) return "online-stopped";
  return "online-moving";
};

// تابع برای تعیین رنگ بر اساس سرعت
const getSpeedColor = (speed) => {
  if (speed === 0) return "#16a34a"; 
  if (speed <= 20) return "#16a34a"; 
  if (speed <= 40) return "#22c55e";
  if (speed <= 60) return "#fbbf24";
  if (speed <= 80) return "#f97316"; 
  return "#dc2626"; 
};

// تابع برای ایجاد ایکون نقطه (دایره با عدد داخل)
const createPointIcon = (point, index) => {
  const speedColor = getSpeedColor(point.speed);
  
  const html = `
    <div class="point-marker">
      <div class="point-circle" style="background: ${speedColor};">
        ${index}
      </div>
    </div>
  `;
  
  return L.divIcon({
    html,
    className: 'point-marker-icon',
    iconSize: [36, 36],
    iconAnchor: [18, 18]
  });
};

// ایجاد نقشه
const initializeMap = () => {
  if (!mapContainer.value) return;

  map = L.map(mapContainer.value, {
    zoomControl: false, 
  }).setView([35.6234, 51.4145], 16);

  L.tileLayer("https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png", {
    attribution: "© OpenStreetMap contributors",
  }).addTo(map);
};

const calculateDistance = (lat1, lon1, lat2, lon2) => {
  const R = 6371; // شعاع زمین در کیلومتر
  const dLat = (lat2 - lat1) * Math.PI / 180;
  const dLon = (lon2 - lon1) * Math.PI / 180;
  const a = 
    Math.sin(dLat/2) * Math.sin(dLat/2) +
    Math.cos(lat1 * Math.PI / 180) * Math.cos(lat2 * Math.PI / 180) * 
    Math.sin(dLon/2) * Math.sin(dLon/2);
  const c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1-a));
  return R * c;
};

const drawRoute = () => {

  routeSegments.forEach((segment) => {
    if (segment && map) map.removeLayer(segment);
  });
  routeSegments = [];


  pointMarkers.forEach((marker) => {
    if (marker && map) map.removeLayer(marker);
  });
  pointMarkers = [];

  if (routeData.value.length === 0) return;


  for (let i = 0; i < routeData.value.length - 1; i++) {
    const currentPoint = routeData.value[i];
    const nextPoint = routeData.value[i + 1];

    const speed = currentPoint.speed;
    const color = getSpeedColor(speed);

    const segment = L.polyline([
      [currentPoint.latitude, currentPoint.longitude],
      [nextPoint.latitude, nextPoint.longitude]
    ], {
      color: color,
      weight: 4,
      opacity: 0.8,
      lineJoin: "round",
      lineCap: "round",
      className: "route-segment"
    }).addTo(map);
    

    segment.bindPopup(`
      <div class="route-segment-popup">
        <strong>قطعه ${i + 1} → ${i + 2}</strong><br>
        سرعت: <span style="color: ${color}; font-weight: bold">${speed} km/h</span><br>
        فاصله: ${calculateDistance(
          currentPoint.latitude,
          currentPoint.longitude,
          nextPoint.latitude,
          nextPoint.longitude
        ).toFixed(2)} کیلومتر
      </div>
    `);
    
    routeSegments.push(segment);
  }


  routeData.value.forEach((point, index) => {
    const pointIcon = createPointIcon(point, index + 1);
    const marker = L.marker([point.latitude, point.longitude], { 
      icon: pointIcon,
      interactive: true
    });
    
    // اضافه کردن popup به نقاط
    const speedColor = getSpeedColor(point.speed);
    marker.bindPopup(`
      <div class="point-popup">
        <strong>نقطه ${index + 1}</strong><br>
        سرعت: <span style="color: ${speedColor}">${point.speed} km/h</span><br>
        زمان: ${new Date(point.unixtime * 1000).toLocaleTimeString('fa-IR')}<br>
        وضعیت: ${point.alarm}<br>
        باتری: ${(point.batt_v / 1000).toFixed(2)}V
      </div>
    `);
    
    marker.addTo(map);
    pointMarkers.push(marker);
  });

  // fit bounds به کل مسیر
  const points = routeData.value.map((point) => [point.latitude, point.longitude]);
  if (points.length > 0) {
    const bounds = L.latLngBounds(points);
    map.fitBounds(bounds, { padding: [50, 50] });
  }
};


const goBack = () => {
  emit("go-back");
};

// Lifecycle
onMounted(() => {
  initializeMap();
  drawRoute();
});

watch(
  () => props.device,
  () => {
    if (props.device && map) {
      drawRoute();
    }
  },
  { immediate: true }
);
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
  width: 400px; /*  change this to your desired width */
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

.device-marker-container.online-moving {
  border-color: #1db4f9;
}

.device-marker-container.online-stopped {
  border-color: #ff6b30;
}

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

.map-container {
  width: 100%;
  height: 100%;
}

.simple-controls {
  position: absolute;
  bottom: 20px;
  left: 20px;
  z-index: 1000;
  display: flex;
  gap: 12px;
}

.control-btn {
  background: rgba(0, 0, 0, 0.85);
  color: white;
  border: 1px solid rgba(29, 180, 249, 0.3);
  padding: 10px 18px;
  border-radius: 8px;
  cursor: pointer;
  font-size: 14px;
  font-family: system-ui, -apple-system, sans-serif;
  display: flex;
  align-items: center;
  gap: 8px;
  transition: all 0.3s ease;
  backdrop-filter: blur(10px);
  min-width: 120px;
}

.control-btn:hover {
  background: rgba(29, 180, 249, 0.2);
  border-color: rgba(29, 180, 249, 0.6);
  transform: translateY(-2px);
  box-shadow: 0 4px 15px rgba(29, 180, 249, 0.3);
}

.control-icon {
  width: 20px;
  height: 20px;
}

/* استایل‌های جدید برای نقاط */
:deep(.point-marker-icon) {
  background: transparent !important;
  border: none !important;
}

:deep(.point-marker) {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 36px;
  height: 36px;
  cursor: pointer;
}

:deep(.point-circle) {
  width: 36px;
  height: 36px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  color: white;
  font-weight: bold;
  font-size: 14px;
  border: 3px solid white;
  box-shadow: 0 3px 8px rgba(0, 0, 0, 0.5);
  transition: all 0.3s ease;
}

:deep(.point-circle:hover) {
  transform: scale(1.2);
  box-shadow: 0 5px 15px rgba(0, 0, 0, 0.7);
}

:deep(.route-segment) {
  filter: drop-shadow(0 1px 3px rgba(0, 0, 0, 0.3));
  transition: all 0.3s ease;
}

:deep(.route-segment:hover) {
  filter: drop-shadow(0 2px 5px rgba(0, 0, 0, 0.5));
  opacity: 1;
}

:deep(.route-segment-popup),
:deep(.point-popup) {
  font-family: system-ui, -apple-system, sans-serif;
  padding: 12px;
  min-width: 220px;
  background: rgba(0, 0, 0, 0.95);
  color: white;
  border-radius: 8px;
  border: 1px solid rgba(255, 255, 255, 0.1);
  backdrop-filter: blur(10px);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.3);
}

:deep(.route-segment-popup strong),
:deep(.point-popup strong) {
  color: #1db4f9;
  display: block;
  margin-bottom: 8px;
  border-bottom: 1px solid rgba(255, 255, 255, 0.1);
  padding-bottom: 4px;
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

  .simple-controls {
    bottom: 10px;
    left: 10px;
    flex-direction: column;
  }

  .control-btn {
    min-width: 100px;
    padding: 8px 14px;
    font-size: 13px;
  }

  :deep(.point-circle) {
    width: 32px;
    height: 32px;
    font-size: 13px;
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

  .control-btn {
    min-width: 90px;
    padding: 6px 12px;
    font-size: 12px;
  }

  .control-icon {
    width: 16px;
    height: 16px;
  }

  :deep(.point-circle) {
    width: 28px;
    height: 28px;
    font-size: 12px;
  }
}
</style>