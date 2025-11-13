<template>
  <div ref="mapContainer" class="map-container"></div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from "vue";
import L from "leaflet";
import "leaflet/dist/leaflet.css";

// Fix Leaflet default marker icon issue
delete L.Icon.Default.prototype._getIconUrl;
L.Icon.Default.mergeOptions({
  iconRetinaUrl: "https://unpkg.com/leaflet@1.7.1/dist/images/marker-icon-2x.png",
  iconUrl: "https://unpkg.com/leaflet@1.7.1/dist/images/marker-icon.png",
  shadowUrl: "https://unpkg.com/leaflet@1.7.1/dist/images/marker-shadow.png",
});

const mapContainer = ref(null);
let map = null;

const iotDevices = ref([
  {
    id: 1,
    name: "Camera A1",
    type: "camera",
    lat: 40.7128,
    lng: -74.006,
    value: "Online",
    unit: "",
    status: "online",
    location: "Main Building",
  },
  {
    id: 2,
    name: "Camera B2",
    type: "camera",
    lat: 40.758,
    lng: -73.9855,
    value: "Online",
    unit: "",
    status: "online",
    location: "Lab Room",
  },
  {
    id: 3,
    name: "Sensor C3",
    type: "sensor",
    lat: 40.7489,
    lng: -73.968,
    value: "Offline",
    unit: "",
    status: "offline",
    location: "Entrance",
  },
  {
    id: 4,
    name: "Camera D4",
    type: "camera",
    lat: 40.7289,
    lng: -73.978,
    value: "Online",
    unit: "",
    status: "online",
    location: "Storage Room",
  },
  {
    id: 5,
    name: "Sensor E5",
    type: "sensor",
    lat: 40.7389,
    lng: -73.988,
    value: "Offline",
    unit: "",
    status: "offline",
    location: "Control Room",
  },
]);

const emit = defineEmits(['device-selected']);

// آیکون‌های SVG برای هر وضعیت
const onlineSVG = `<svg xmlns="http://www.w3.org/2000/svg" height="40px" viewBox="0 -960 960 960" width="40px" fill="#ffffff"><path d="M480-480q33 0 56.5-23.5T560-560q0-33-23.5-56.5T480-640q-33 0-56.5 23.5T400-560q0 33 23.5 56.5T480-480Zm0 294q122-112 181-203.5T720-552q0-109-69.5-178.5T480-800q-101 0-170.5 69.5T240-552q0 71 59 162.5T480-186Zm0 106Q319-217 239.5-334.5T160-552q0-150 96.5-239T480-880q127 0 223.5 89T800-552q0 100-79.5 217.5T480-80Zm0-480Z"/></svg>`;

const offlineSVG = `<svg xmlns="http://www.w3.org/2000/svg" height="40px" viewBox="0 -960 960 960" width="40px" fill="#ffffff"><path d="M560-560q0-33-23.5-56.5T480-640q-10 0-19 2t-17 7l107 107q5-8 7-17t2-19Zm168 213-58-58q25-42 37.5-78.5T720-552q0-109-69.5-178.5T480-800q-44 0-82.5 13.5T328-747l-57-57q43-37 97-56.5T480-880q127 0 223.5 89T800-552q0 48-18 98.5T728-347Zm-157 71L244-603q-2 12-3 25t-1 26q0 71 59 162.5T480-186q26-23 48.5-45.5T571-276ZM819-28 627-220q-32 34-68 69t-79 71Q319-217 239.5-334.5T160-552q0-32 5-61t14-55L27-820l57-57L876-85l-57 57ZM408-439Zm91-137Z"/></svg>`;

// ایجاد آیکون سفارشی با Leaflet
const createCustomIcon = (device) => {
  const { status } = device;

  // ایجاد HTML برای مارکر
  let html;
  if (status === "online") {
    html = `
      <div class="leaflet-custom-marker ${status}">
        <div class="marker-pulse-ring"></div>
        <div class="marker-pulse-ring delay-1"></div>
        <div class="marker-pulse-ring delay-2"></div>
        <div class="marker-main">
          <div class="marker-inner">
            ${onlineSVG}
          </div>
        </div>
      </div>
    `;
  } else {
    html = `
      <div class="leaflet-custom-marker ${status}">
        <div class="marker-main">
          <div class="marker-inner">
            ${offlineSVG}
          </div>
        </div>
      </div>
    `;
  }

  const size = status === "online" ? [52, 52] : [46, 46];
  const anchor = status === "online" ? [26, 26] : [23, 23];

  return L.divIcon({
    html: html,
    className: `leaflet-custom-marker-container ${status}`,
    iconSize: size,
    iconAnchor: anchor
  });
};

// Lifecycle
onMounted(() => {
  map = L.map(mapContainer.value).setView([40.7489, -73.968], 12);

L.tileLayer('https://{s}.basemaps.cartocdn.com/rastertiles/voyager/{z}/{x}/{y}{r}.png', {
  attribution: '&copy; OpenStreetMap &copy; CARTO'
}).addTo(map);



  // اضافه کردن مارکرهای دستگاه‌ها - بدون پاپ‌آپ
  iotDevices.value.forEach((device) => {
    const marker = L.marker([device.lat, device.lng], {
      icon: createCustomIcon(device),
    }).addTo(map);

    marker.on('click', () => {
      emit('device-selected', device);
    });
  });
});

onUnmounted(() => {
  if (map) {
    map.remove();
  }
});
</script>

<style scoped>
.map-container {
  width: 100%;
  height: 100%;
  background: #f8f9fa;
}

/* استایل‌های اصلی برای مارکرهای Leaflet */
:deep(.leaflet-custom-marker-container) {
  background: transparent !important;
  border: none !important;
}

:deep(.leaflet-custom-marker) {
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
}

:deep(.marker-main) {
  border-radius: 50%;
  background: rgba(0, 0, 0, 0.412);
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow:
    0 4px 12px rgba(0, 0, 0, 0.4),
    0 0 0 2px rgba(255, 255, 255, 0.1);
  border: 3px solid;
  position: relative;
  z-index: 10;
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
}

:deep(.leaflet-custom-marker.online .marker-main) {
  width: 52px;
  height: 52px;
  border-color: #ffffff;
}

:deep(.leaflet-custom-marker.offline .marker-main) {
  width: 46px;
  height: 46px;
  border-color: #ffffff;
}

:deep(.marker-inner) {
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 50%;
  transition: all 0.3s ease;
}

:deep(.leaflet-custom-marker.online .marker-inner) {
  width: 36px;
  height: 36px;
  background: linear-gradient(135deg, #040404c3, #0000006f);
}

:deep(.leaflet-custom-marker.offline .marker-inner) {
  width: 32px;
  height: 32px;
  background: linear-gradient(135deg, #040404c3, #0000006f);
}

:deep(.marker-inner svg) {
  filter: drop-shadow(0 1px 2px rgba(0, 0, 0, 0.4));
}

:deep(.leaflet-custom-marker.online .marker-inner svg) {
  width: 24px;
  height: 24px;
}

:deep(.leaflet-custom-marker.offline .marker-inner svg) {
  width: 22px;
  height: 22px;
}

/* حلقه‌های پالس با رنگ‌های روشن برای کنتراست */
:deep(.marker-pulse-ring) {
  position: absolute;
  top: 0;
  left: 0;
  border-radius: 50%;
  border: 3px solid;
  animation: leaflet-pulse 2s infinite;
  opacity: 0.8;
  z-index: 5;
}

:deep(.leaflet-custom-marker.online .marker-pulse-ring) {
  width: 52px;
  height: 52px;
  border-color: #0000006f;
}

:deep(.leaflet-custom-marker.offline .marker-pulse-ring) {
  width: 46px;
  height: 46px;
  border-color: #0000006f;
}

:deep(.marker-pulse-ring.delay-1) {
  animation-delay: 0.7s;
}

:deep(.marker-pulse-ring.delay-2) {
  animation-delay: 1.4s;
}

@keyframes leaflet-pulse {
  0% {
    transform: scale(0.8);
    opacity: 0.8;
  }
  50% {
    opacity: 0.4;
  }
  100% {
    transform: scale(2.0);
    opacity: 0;
  }
}

/* غیرفعال کردن انیمیشن برای دستگاه‌های آفلاین */
:deep(.leaflet-custom-marker.offline .marker-pulse-ring) {
  display: none;
}

/* استایل‌های leaflet */
:deep(.leaflet-container) {
  background: #ffffff;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
}

/* hover effects */
:deep(.leaflet-custom-marker-container:hover .marker-main) {
  transform: scale(1.15);
  box-shadow:
    0 6px 20px rgba(0, 0, 0, 0.6),
    0 0 0 3px rgba(255, 255, 255, 0.2);
}

:deep(.leaflet-custom-marker-container:hover .marker-inner) {
  transform: scale(1.1);
}

/* بهبود برای موبایل */
@media (max-width: 768px) {
  :deep(.leaflet-custom-marker.online .marker-main) {
    width: 48px;
    height: 48px;
  }

  :deep(.leaflet-custom-marker.offline .marker-main) {
    width: 42px;
    height: 42px;
  }

  :deep(.leaflet-custom-marker.online .marker-inner) {
    width: 32px;
    height: 32px;
  }

  :deep(.leaflet-custom-marker.offline .marker-inner) {
    width: 28px;
    height: 28px;
  }

  :deep(.leaflet-custom-marker.online .marker-inner svg) {
    width: 20px;
    height: 20px;
  }

  :deep(.leaflet-custom-marker.offline .marker-inner svg) {
    width: 18px;
    height: 18px;
  }

  :deep(.leaflet-custom-marker.online .marker-pulse-ring) {
    width: 48px;
    height: 48px;
  }
}
</style>
