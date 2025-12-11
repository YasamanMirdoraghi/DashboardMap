<template>
  <div ref="mapContainer" class="map-container"></div>
</template>

<script setup>
import { ref, onMounted, onUnmounted, watch } from "vue";
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

const props = defineProps({
  devices: {
    type: Array,
    default: () => []
  }
});

const emit = defineEmits(['device-selected']);

const mapContainer = ref(null);
let map = null;
let markerClusterGroup = null;

// Status Icons
const onlineMovingSVG = `<svg xmlns="http://www.w3.org/2000/svg" height="40px" viewBox="0 -960 960 960" width="40px" fill="#1db4f9"><path d="M480-480q33 0 56.5-23.5T560-560q0-33-23.5-56.5T480-640q-33 0-56.5 23.5T400-560q0 33 23.5 56.5T480-480Zm0 294q122-112 181-203.5T720-552q0-109-69.5-178.5T480-800q-101 0-170.5 69.5T240-552q0 71 59 162.5T480-186Zm0 106Q319-217 239.5-334.5T160-552q0-150 96.5-239T480-880q127 0 223.5 89T800-552q0 100-79.5 217.5T480-80Zm0-480Z"/></svg>`;

const onlineStoppedSVG = `<svg xmlns="http://www.w3.org/2000/svg" height="40px" viewBox="0 -960 960 960" width="40px" fill="#ff6b30"><path d="M480-480q33 0 56.5-23.5T560-560q0-33-23.5-56.5T480-640q-33 0-56.5 23.5T400-560q0 33 23.5 56.5T480-480Zm0 294q122-112 181-203.5T720-552q0-109-69.5-178.5T480-800q-101 0-170.5 69.5T240-552q0 71 59 162.5T480-186Zm0 106Q319-217 239.5-334.5T160-552q0-150 96.5-239T480-880q127 0 223.5 89T800-552q0 100-79.5 217.5T480-80Zm0-480Z"/></svg>`;

const offlineSVG = `<svg xmlns="http://www.w3.org/2000/svg" height="40px" viewBox="0 -960 960 960" width="40px" fill="#ffffff"><path d="M560-560q0-33-23.5-56.5T480-640q-10 0-19 2t-17 7l107 107q5-8 7-17t2-19Zm168 213-58-58q25-42 37.5-78.5T720-552q0-109-69.5-178.5T480-800q-44 0-82.5 13.5T328-747l-57-57q43-37 97-56.5T480-880q127 0 223.5 89T800-552q0 48-18 98.5T728-347Zm-157 71L244-603q-2 12-3 25t-1 26q0 71 59 162.5T480-186q26-23 48.5-45.5T571-276ZM819-28 627-220q-32 34-68 69t-79 71Q319-217 239.5-334.5T160-552q0-32 5-61t14-55L27-820l57-57L876-85l-57 57ZM408-439Zm91-137Z"/></svg>`;

// تابع تشخیص وضعیت دستگاه
const getDeviceStatus = (device) => {
  if (!device.position) return "offline";

  // بررسی آنلاین/آفلاین بودن بر اساس آخرین ارتباط
  const now = Math.floor(Date.now() / 1000);
  const lastUpdate = device.position.unixtime;
  const fiveMinutes = 300; // 5 minutes in seconds

  if (now - lastUpdate > fiveMinutes) {
    return "offline";
  }

  // بررسی حرکت دستگاه
  if (device.position.speed > 0) {
    return "online-moving";
  } else {
    return "online-stopped";
  }
};

const createCustomIcon = (device) => {
  const status = getDeviceStatus(device);

  let svg;
  if (status === "online-moving") {
    svg = onlineMovingSVG;
  } else if (status === "online-stopped") {
    svg = onlineStoppedSVG;
  } else {
    svg = offlineSVG;
  }

  let html;
  if (status === "online-moving") {
    html = `
      <div class="leaflet-custom-marker ${status}">
        <div class="marker-pulse-ring"></div>
        <div class="marker-pulse-ring delay-1"></div>
        <div class="marker-pulse-ring delay-2"></div>
        <div class="marker-main">
          <div class="marker-inner">
            ${svg}
          </div>
        </div>
      </div>
    `;
  } else {
    html = `
      <div class="leaflet-custom-marker ${status}">
        <div class="marker-main">
          <div class="marker-inner">
            ${svg}
          </div>
        </div>
      </div>
    `;
  }
  const size = [52, 52];
  const anchor = [26, 26];

  return L.divIcon({
    html: html,
    className: `leaflet-custom-marker-container ${status}`,
    iconSize: size,
    iconAnchor: anchor
  });
};

const updateMarkers = () => {
  if (!map || !markerClusterGroup) return;

  markerClusterGroup.clearLayers();

  // فیلتر دستگاه‌هایی که موقعیت معتبر دارند
  const validDevices = props.devices.filter(device =>
    device.position &&
    device.position.latitude &&
    device.position.longitude &&
    Math.abs(device.position.latitude) <= 90 &&
    Math.abs(device.position.longitude) <= 180
  );

  if (validDevices.length === 0) return;

  validDevices.forEach((device) => {
    const marker = L.marker(
      [device.position.latitude, device.position.longitude],
      {
        icon: createCustomIcon(device),
        title: `Device ${device.deviceid}`
      }
    ).addTo(markerClusterGroup);

    // فقط رویداد کلیک - بدون پاپ‌آپ
    marker.on('click', () => {
      emit('device-selected', device);
    });
  });

  // اگر فقط یک دستگاه داریم، روی آن زوم کن
  if (validDevices.length === 1) {
    const device = validDevices[0];
    map.setView([device.position.latitude, device.position.longitude], 15);
  } else if (validDevices.length > 0) {
    // ایجاد bounds برای همه دستگاه‌ها
    const bounds = L.latLngBounds(
      validDevices.map(device => [device.position.latitude, device.position.longitude])
    );
    map.fitBounds(bounds, { padding: [50, 50] });
  }
};

// Lifecycle
onMounted(() => {
  map = L.map(mapContainer.value, {
    zoomControl: false,
    attributionControl: false
  }).setView([35.699, 51.369], 12);

  // اضافه کردن کنترل zoom سفارشی
  L.control.zoom({
    position: 'topright'
  }).addTo(map);

  // اضافه کردن attribution
  L.control.attribution({
    position: 'bottomright',
    prefix: '© <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a>'
  }).addTo(map);

  // تایل لایر
  L.tileLayer('https://{s}.basemaps.cartocdn.com/rastertiles/voyager/{z}/{x}/{y}{r}.png', {
    attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> &copy; <a href="https://carto.com/attributions">CARTO</a>',
    subdomains: 'abcd',
    maxZoom: 19,
  }).addTo(map);

  // گروه خوشه‌بندی
  markerClusterGroup = L.markerClusterGroup({
    chunkedLoading: true,
    maxClusterRadius: 80,
    spiderfyOnMaxZoom: true,
    showCoverageOnHover: false,
    zoomToBoundsOnClick: true,
    iconCreateFunction: function(cluster) {
      const count = cluster.getChildCount();

      // رنگ مشکی برای همه کلاسترها
      const borderColor = "#000000";

      return L.divIcon({
        html: `
          <div class="cluster-custom-marker">
            <div class="cluster-pulse-ring" style="border-color: ${borderColor}"></div>
            <div class="cluster-pulse-ring delay-1" style="border-color: ${borderColor}"></div>
            <div class="cluster-pulse-ring delay-2" style="border-color: ${borderColor}"></div>
            <div class="cluster-main" style="border-color: ${borderColor}">
              <div class="cluster-inner">
                <span class="cluster-count">${count}</span>
              </div>
            </div>
          </div>
        `,
        className: 'leaflet-cluster-custom',
        iconSize: L.point(52, 52),
        iconAnchor: [26, 26]
      });
    }
  });

  map.addLayer(markerClusterGroup);
  updateMarkers();
});

watch(() => props.devices, () => {
  updateMarkers();
}, { deep: true });

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

:deep(.leaflet-custom-marker-container) {
  background: transparent !important;
  border: none !important;
}

:deep(.leaflet-custom-marker) {
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 52px;
  height: 52px;
}

:deep(.marker-main) {
  border-radius: 50%;
  background: rgba(0, 0, 0, 0.8);
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow:
    0 4px 12px rgba(0, 0, 0, 0.4),
    0 0 0 2px rgba(255, 255, 255, 0.1);
  position: relative;
  z-index: 10;
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  width: 52px;
  height: 52px;
  border: 3px solid;
  transform-origin: center;
}

:deep(.leaflet-custom-marker.online-moving .marker-main) {
  border-color: #1db4f9;
}

:deep(.leaflet-custom-marker.online-stopped .marker-main) {
  border-color: #ff6b30;
}

:deep(.leaflet-custom-marker.offline .marker-main) {
  border-color: #ffffff;
}

:deep(.marker-inner) {
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 50%;
  width: 36px;
  height: 36px;
  background: linear-gradient(135deg, #1f1f1f, #141414);
}

:deep(.marker-inner svg) {
  filter: drop-shadow(0 1px 2px rgba(0, 0, 0, 0.4));
  display: block;
}

:deep(.leaflet-custom-marker.online-moving .marker-inner svg),
:deep(.leaflet-custom-marker.online-stopped .marker-inner svg) {
  width: 20px;
  height: 20px;
}

:deep(.leaflet-custom-marker.offline .marker-inner svg) {
  width: 18px;
  height: 18px;
}

:deep(.marker-pulse-ring) {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  border-radius: 50%;
  border: 3px solid;
  animation: leaflet-pulse 2s infinite;
  opacity: 0.8;
  z-index: 5;
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
    transform: translate(-50%, -50%) scale(0.8);
    opacity: 0.8;
  }
  50% {
    opacity: 0.4;
  }
  100% {
    transform: translate(-50%, -50%) scale(2.0);
    opacity: 0;
  }
}

:deep(.leaflet-custom-marker.online-stopped .marker-pulse-ring) {
  border-color: #ff6b30;
}

:deep(.leaflet-custom-marker.online-stopped .marker-pulse-ring),
:deep(.leaflet-custom-marker.offline .marker-pulse-ring) {
  display: none;
}

:deep(.leaflet-container) {
  background: #ffffff;
  font-family: 'Inter', sans-serif;
}

:deep(.leaflet-custom-marker-container:hover .marker-main) {
  transform: scale(1.15);
  box-shadow:
    0 6px 20px rgba(0, 0, 0, 0.6),
    0 0 0 3px rgba(255, 255, 255, 0.2);
}

/* خوشه‌بندی */
:deep(.marker-cluster),
:deep(.marker-cluster-small),
:deep(.marker-cluster-medium),
:deep(.marker-cluster-large),
:deep(.marker-cluster div) {
  background: transparent !important;
  border: none !important;
  width: auto !important;
  height: auto !important;
  margin: 0 !important;
  text-align: center !important;
  font: inherit !important;
}

:deep(.leaflet-cluster-custom) {
  background: transparent !important;
  border: none !important;
}

:deep(.cluster-custom-marker) {
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 100%;
  height: 100%;
}

:deep(.cluster-main) {
  border-radius: 50% !important;
  background: rgba(0, 0, 0, 0.8);
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow:
    0 4px 12px rgba(0, 0, 0, 0.4),
    0 0 0 2px rgba(255, 255, 255, 0.1);
  position: relative;
  z-index: 10;
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  width: 52px !important;
  height: 52px !important;
  border: 3px solid #ffffff !important; /* مشکی */
  transform-origin: center;
  margin: 0 auto;
}

:deep(.cluster-inner) {
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 50% !important;
  width: 36px !important;
  height: 36px !important;
  background: linear-gradient(135deg, #1f1f1f, #141414);
  font-weight: 600;
  color: white;
  font-size: 14px !important;
  font-family: 'Inter', sans-serif;
  line-height: 1;
}

:deep(.cluster-pulse-ring) {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  border-radius: 50% !important;
  border: 3px solid #000000; /* مشکی */
  animation: leaflet-pulse 2s infinite;
  opacity: 0.8;
  z-index: 5;
  width: 52px !important;
  height: 52px !important;
}

:deep(.cluster-pulse-ring.delay-1) {
  animation-delay: 0.7s;
  border-color: #000000; /* مشکی */
}

:deep(.cluster-pulse-ring.delay-2) {
  animation-delay: 1.4s;
  border-color: #000000; /* مشکی */
}

:deep(.leaflet-cluster-custom:hover .cluster-main) {
  transform: scale(1.15);
  box-shadow:
    0 6px 20px rgba(0, 0, 0, 0.6),
    0 0 0 3px rgba(255, 255, 255, 0.2);
}

/* Responsive */
@media (max-width: 768px) {
  :deep(.leaflet-custom-marker) {
    width: 44px;
    height: 44px;
  }

  :deep(.marker-main) {
    width: 44px;
    height: 44px;
  }

  :deep(.marker-inner) {
    width: 32px;
    height: 32px;
  }

  :deep(.leaflet-custom-marker.online-moving .marker-inner svg),
  :deep(.leaflet-custom-marker.online-stopped .marker-inner svg) {
    width: 18px;
    height: 18px;
  }

  :deep(.leaflet-custom-marker.offline .marker-inner svg) {
    width: 16px;
    height: 16px;
  }

  :deep(.marker-pulse-ring) {
    width: 44px;
    height: 44px;
  }

  :deep(.cluster-main) {
    width: 44px !important;
    height: 44px !important;
  }

  :deep(.cluster-inner) {
    width: 32px !important;
    height: 32px !important;
    font-size: 12px !important;
  }

  :deep(.cluster-pulse-ring) {
    width: 44px !important;
    height: 44px !important;
  }
}

:deep(.cluster-custom-marker),
:deep(.cluster-main),
:deep(.cluster-inner) {
  border-radius: 50% !important;
}
</style>
