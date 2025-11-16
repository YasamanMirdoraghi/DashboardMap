<template>
  <div ref="mapContainer" class="map-container"></div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from "vue";
import L from "leaflet";
import "leaflet/dist/leaflet.css";
import "leaflet.markercluster/dist/MarkerCluster.css";
import "leaflet.markercluster/dist/MarkerCluster.Default.css";
import "leaflet.markercluster";

// Fix Leaflet default marker icon issue
delete L.Icon.Default.prototype._getIconUrl;
L.Icon.Default.mergeOptions({
  iconRetinaUrl: "https://unpkg.com/leaflet@1.7.1/dist/images/marker-icon-2x.png",
  iconUrl: "https://unpkg.com/leaflet@1.7.1/dist/images/marker-icon.png",
  shadowUrl: "https://unpkg.com/leaflet@1.7.1/dist/images/marker-shadow.png",
});

const mapContainer = ref(null);
let map = null;
let markerClusterGroup = null;

const iotDevices = ref([
  {
    id: 1,
    name: "Camera A1",
    type: "camera",
    lat: 35.6892,
    lng: 51.3890,
    value: "Online",
    unit: "",
    status: "online-moving",
    location: "میدان آزادی",
  },
  {
    id: 2,
    name: "Camera B2",
    type: "camera",
    lat: 35.7440,
    lng: 51.3755,
    value: "Online",
    unit: "",
    status: "online-stopped",
    location: "تجریش",
  },
  {
    id: 3,
    name: "Sensor C3",
    type: "sensor",
    lat: 35.7153,
    lng: 51.4043,
    value: "Offline",
    unit: "",
    status: "offline",
    location: "پارک لاله",
  },
  {
    id: 4,
    name: "Camera D4",
    type: "camera",
    lat: 35.7004,
    lng: 51.3376,
    value: "Online",
    unit: "",
    status: "online-moving",
    location: "برج میلاد",
  },
  {
    id: 5,
    name: "Sensor E5",
    type: "sensor",
    lat: 35.7595,
    lng: 51.4381,
    value: "Offline",
    unit: "",
    status: "offline",
    location: "نیاوران",
  },
  // اضافه کردن دستگاه‌های بیشتر برای تست clustering
  {
    id: 6,
    name: "Camera F6",
    type: "camera",
    lat: 35.6920,
    lng: 51.3910,
    value: "Online",
    unit: "",
    status: "online-moving",
    location: "میدان آزادی",
  },
  {
    id: 7,
    name: "Sensor G7",
    type: "sensor",
    lat: 35.6900,
    lng: 51.3920,
    value: "Online",
    unit: "",
    status: "online-stopped",
    location: "میدان آزادی",
  },
  {
    id: 8,
    name: "Camera H8",
    type: "camera",
    lat: 35.7450,
    lng: 51.3760,
    value: "Offline",
    unit: "",
    status: "offline",
    location: "تجریش",
  },
]);

const emit = defineEmits(['device-selected']);

// آیکون‌های SVG برای هر وضعیت
const onlineSVG = `<svg xmlns="http://www.w3.org/2000/svg" height="40px" viewBox="0 -960 960 960" width="40px" fill="#ffffff"><path d="M480-480q33 0 56.5-23.5T560-560q0-33-23.5-56.5T480-640q-33 0-56.5 23.5T400-560q0 33 23.5 56.5T480-480Zm0 294q122-112 181-203.5T720-552q0-109-69.5-178.5T480-800q-101 0-170.5 69.5T240-552q0 71 59 162.5T480-186Zm0 106Q319-217 239.5-334.5T160-552q0-150 96.5-239T480-880q127 0 223.5 89T800-552q0 100-79.5 217.5T480-80Zm0-480Z"/></svg>`;

const offlineSVG = `<svg xmlns="http://www.w3.org/2000/svg" height="40px" viewBox="0 -960 960 960" width="40px" fill="#ffffff"><path d="M560-560q0-33-23.5-56.5T480-640q-10 0-19 2t-17 7l107 107q5-8 7-17t2-19Zm168 213-58-58q25-42 37.5-78.5T720-552q0-109-69.5-178.5T480-800q-44 0-82.5 13.5T328-747l-57-57q43-37 97-56.5T480-880q127 0 223.5 89T800-552q0 48-18 98.5T728-347Zm-157 71L244-603q-2 12-3 25t-1 26q0 71 59 162.5T480-186q26-23 48.5-45.5T571-276ZM819-28 627-220q-32 34-68 69t-79 71Q319-217 239.5-334.5T160-552q0-32 5-61t14-55L27-820l57-57L876-85l-57 57ZM408-439Zm91-137Z"/></svg>`;

// ایجاد آیکون سفارشی با Leaflet
const createCustomIcon = (device) => {
  const { status } = device;

  // ایجاد HTML برای مارکر بر اساس وضعیت
  let html;
  if (status === "online-moving") {
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
  } else if (status === "online-stopped") {
    html = `
      <div class="leaflet-custom-marker ${status}">
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

  // تنظیم سایز و انکر بر اساس وضعیت
  let size, anchor;
  if (status === "online-moving") {
    size = [52, 52];
    anchor = [26, 26];
  } else if (status === "online-stopped") {
    size = [48, 48];
    anchor = [24, 24];
  } else {
    size = [46, 46];
    anchor = [23, 23];
  }

  return L.divIcon({
    html: html,
    className: `leaflet-custom-marker-container ${status}`,
    iconSize: size,
    iconAnchor: anchor
  });
};

// Lifecycle
onMounted(() => {
  // سنتر نقشه روی تهران با سطح زوم 11
  map = L.map(mapContainer.value).setView([35.6892, 51.3890], 11);

  L.tileLayer('https://{s}.basemaps.cartocdn.com/rastertiles/voyager/{z}/{x}/{y}{r}.png', {
    attribution: '&copy; OpenStreetMap &copy; CARTO'
  }).addTo(map);

  // ایجاد گروه خوشه‌بندی
  markerClusterGroup = L.markerClusterGroup({
    chunkedLoading: true,
    maxClusterRadius: 80, // شعاع خوشه‌بندی (پیکسل)
    spiderfyOnMaxZoom: true,
    showCoverageOnHover: false,
    zoomToBoundsOnClick: true,

    // استایل سفارشی برای خوشه‌ها
  iconCreateFunction: function(cluster) {
  const count = cluster.getChildCount();
  let size = 'small';

  if (count > 10) {
    size = 'large';
  } else if (count > 5) {
    size = 'medium';
  }

  return L.divIcon({
    html: `
      <div class="cluster-container">
        <div class="cluster-pulse-ring"></div>
        <div class="cluster-pulse-ring"></div>
        <div class="cluster-pulse-ring"></div>
        <div class="cluster-marker ${size}">${count}</div>
      </div>
    `,
    className: 'marker-cluster-custom',
    iconSize: L.point(80, 80) // افزایش سایز برای جا دادن حلقه‌ها
  });
}
  });

  // اضافه کردن مارکرهای دستگاه‌ها به گروه خوشه‌بندی
  iotDevices.value.forEach((device) => {
    const marker = L.marker([device.lat, device.lng], {
      icon: createCustomIcon(device),
    }).addTo(markerClusterGroup);

    marker.on('click', () => {
      emit('device-selected', device);
    });
  });

  // اضافه کردن گروه خوشه‌بندی به نقشه
  map.addLayer(markerClusterGroup);
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

/* استایل برای آنلاین و در حال حرکت (آبی با انیمیشن) */
:deep(.leaflet-custom-marker.online-moving .marker-main) {
  width: 52px;
  height: 52px;
  border-color: #1db4f9;
}

:deep(.leaflet-custom-marker.online-stopped .marker-main) {
  width: 48px;
  height: 48px;
  border-color: #ff6b30;
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

:deep(.leaflet-custom-marker.online-moving .marker-inner) {
  width: 36px;
  height: 36px;
  background: linear-gradient(135deg, #1f1f1fd1, #141414a3);
}

:deep(.leaflet-custom-marker.online-stopped .marker-inner) {
  width: 34px;
  height: 34px;
  background: linear-gradient(135deg, #1f1f1fd1, #141414a3);
}

:deep(.leaflet-custom-marker.offline .marker-inner) {
  width: 32px;
  height: 32px;
  background: linear-gradient(135deg, #1f1f1fd1, #141414a3);
}

:deep(.marker-inner svg) {
  filter: drop-shadow(0 1px 2px rgba(0, 0, 0, 0.4));
}

:deep(.leaflet-custom-marker.online-moving .marker-inner svg) {
  width: 24px;
  height: 24px;
}

:deep(.leaflet-custom-marker.online-stopped .marker-inner svg) {
  width: 22px;
  height: 22px;
}

:deep(.leaflet-custom-marker.offline .marker-inner svg) {
  width: 20px;
  height: 20px;
}

/* حلقه‌های پالس برای آنلاین و در حال حرکت */
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

:deep(.leaflet-custom-marker.online-moving .marker-pulse-ring) {
  width: 52px;
  height: 52px;
  border-color: #1db4f9;
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

:deep(.leaflet-custom-marker.online-stopped .marker-pulse-ring),
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

/* استایل‌های سفارشی برای خوشه‌ها */
/* استایل‌های سفارشی برای خوشه‌ها */
:deep(.marker-cluster-custom) {
  background: transparent !important;
  border: none !important;
}

:deep(.cluster-container) {
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
}

:deep(.cluster-marker) {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  background: linear-gradient(135deg, #4caf50, #2e7d32);
  color: white;
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: bold;
  font-size: 14px;
  box-shadow:
    0 4px 12px rgba(29, 249, 66, 0.4),
    0 0 0 3px rgba(255, 255, 255, 0.8);
  border: 2px solid #fff;
  transition: all 0.3s ease;
  position: relative;
  z-index: 10;
}

:deep(.cluster-marker.medium) {
  width: 50px;
  height: 50px;
  font-size: 16px;
  background: linear-gradient(135deg, #4caf50, #2e7d32);
}

:deep(.cluster-marker.large) {
  width: 60px;
  height: 60px;
  font-size: 18px;
  background: linear-gradient(135deg, #4caf50, #2e7d32);
}

/* حلقه‌های انیمیشنی برای خوشه‌ها */
:deep(.cluster-pulse-ring) {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  border-radius: 50%;
  border: 2px solid;
  animation: cluster-pulse 2.5s infinite;
  opacity: 0.6;
  z-index: 5;
}

:deep(.cluster-container .cluster-pulse-ring:nth-child(1)) {
  width: 50px;
  height: 50px;
  border-color: #4caf50;
  animation-delay: 0s;
}

:deep(.cluster-container .cluster-pulse-ring:nth-child(2)) {
  width: 60px;
  height: 60px;
  border-color: #4caf50;
  animation-delay: 0.8s;
}

:deep(.cluster-container .cluster-pulse-ring:nth-child(3)) {
  width: 70px;
  height: 70px;
  border-color: #4caf50;
  animation-delay: 1.6s;
}

/* برای خوشه‌های medium */
:deep(.cluster-container .cluster-marker.medium ~ .cluster-pulse-ring:nth-child(1)) {
  width: 60px;
  height: 60px;
  border-color: #4caf50;
}

:deep(.cluster-container .cluster-marker.medium ~ .cluster-pulse-ring:nth-child(2)) {
  width: 70px;
  height: 70px;
  border-color: #4caf50;
}

:deep(.cluster-container .cluster-marker.medium ~ .cluster-pulse-ring:nth-child(3)) {
  width: 80px;
  height: 80px;
  border-color: #4caf50;
}

/* برای خوشه‌های large */
:deep(.cluster-container .cluster-marker.large ~ .cluster-pulse-ring:nth-child(1)) {
  width: 70px;
  height: 70px;
  border-color: #4caf50;
}

:deep(.cluster-container .cluster-marker.large ~ .cluster-pulse-ring:nth-child(2)) {
  width: 80px;
  height: 80px;
  border-color: #4caf50;
}

:deep(.cluster-container .cluster-marker.large ~ .cluster-pulse-ring:nth-child(3)) {
  width: 90px;
  height: 90px;
  border-color: #4caf50;
}

@keyframes cluster-pulse {
  0% {
    transform: translate(-50%, -50%) scale(0.8);
    opacity: 0.6;
  }
  50% {
    opacity: 0.3;
  }
  100% {
    transform: translate(-50%, -50%) scale(1.4);
    opacity: 0;
  }
}

:deep(.cluster-marker:hover) {
  transform: scale(1.15);
  box-shadow:
    0 6px 20px rgba(29, 249, 33, 0.6),
    0 0 0 4px rgba(255, 255, 255, 0.9);
}

/* انیمیشن برای زمانی که خوشه hover می‌شود */
:deep(.cluster-container:hover .cluster-pulse-ring) {
  animation-duration: 1.5s;
}

:deep(.cluster-container:hover .cluster-marker) {
  animation: cluster-bounce 0.5s ease;
}

@keyframes cluster-bounce {
  0%, 100% {
    transform: scale(1);
  }
  50% {
    transform: scale(1.2);
  }
}

/* بهبود برای موبایل */
@media (max-width: 768px) {
  :deep(.leaflet-custom-marker.online-moving .marker-main) {
    width: 48px;
    height: 48px;
  }

  :deep(.leaflet-custom-marker.online-stopped .marker-main) {
    width: 44px;
    height: 44px;
  }

  :deep(.leaflet-custom-marker.offline .marker-main) {
    width: 42px;
    height: 42px;
  }

  :deep(.leaflet-custom-marker.online-moving .marker-inner) {
    width: 32px;
    height: 32px;
  }

  :deep(.leaflet-custom-marker.online-stopped .marker-inner) {
    width: 30px;
    height: 30px;
  }

  :deep(.leaflet-custom-marker.offline .marker-inner) {
    width: 28px;
    height: 28px;
  }

  :deep(.leaflet-custom-marker.online-moving .marker-inner svg) {
    width: 20px;
    height: 20px;
  }

  :deep(.leaflet-custom-marker.online-stopped .marker-inner svg) {
    width: 18px;
    height: 18px;
  }

  :deep(.leaflet-custom-marker.offline .marker-inner svg) {
    width: 16px;
    height: 16px;
  }

  :deep(.leaflet-custom-marker.online-moving .marker-pulse-ring) {
    width: 48px;
    height: 48px;
  }

  :deep(.cluster-marker) {
    width: 35px;
    height: 35px;
    font-size: 12px;
  }

  :deep(.cluster-marker.medium) {
    width: 45px;
    height: 45px;
    font-size: 14px;
  }

  :deep(.cluster-marker.large) {
    width: 55px;
    height: 55px;
    font-size: 16px;
  }
}
</style>
