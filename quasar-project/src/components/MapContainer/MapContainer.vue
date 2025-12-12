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
  },
  selectedDeviceId: {
    type: [Number, String],
    default: null
  }
});

const emit = defineEmits(['device-selected']);

const mapContainer = ref(null);
let map = null;
let markerClusterGroup = null;
let markers = new Map();
let selectedMarker = null;
let allDevicesBounds = null;
let isZoomingFromClick = false;

// Status Icons
const onlineMovingSVG = `<svg xmlns="http://www.w3.org/2000/svg" height="40px" viewBox="0 -960 960 960" width="40px" fill="#1db4f9"><path d="M480-480q33 0 56.5-23.5T560-560q0-33-23.5-56.5T480-640q-33 0-56.5 23.5T400-560q0 33 23.5 56.5T480-480Zm0 294q122-112 181-203.5T720-552q0-109-69.5-178.5T480-800q-101 0-170.5 69.5T240-552q0 71 59 162.5T480-186Zm0 106Q319-217 239.5-334.5T160-552q0-150 96.5-239T480-880q127 0 223.5 89T800-552q0 100-79.5 217.5T480-80Zm0-480Z"/></svg>`;

const onlineStoppedSVG = `<svg xmlns="http://www.w3.org/2000/svg" height="40px" viewBox="0 -960 960 960" width="40px" fill="#ff6b30"><path d="M480-480q33 0 56.5-23.5T560-560q0-33-23.5-56.5T480-640q-33 0-56.5 23.5T400-560q0 33 23.5 56.5T480-480Zm0 294q122-112 181-203.5T720-552q0-109-69.5-178.5T480-800q-101 0-170.5 69.5T240-552q0 71 59 162.5T480-186Zm0 106Q319-217 239.5-334.5T160-552q0-150 96.5-239T480-880q127 0 223.5 89T800-552q0 100-79.5 217.5T480-80Zm0-480Z"/></svg>`;

const offlineSVG = `<svg xmlns="http://www.w3.org/2000/svg" height="40px" viewBox="0 -960 960 960" width="40px" fill="#ffffff"><path d="M560-560q0-33-23.5-56.5T480-640q-10 0-19 2t-17 7l107 107q5-8 7-17t2-19Zm168 213-58-58q25-42 37.5-78.5T720-552q0-109-69.5-178.5T480-800q-44 0-82.5 13.5T328-747l-57-57q43-37 97-56.5T480-880q127 0 223.5 89T800-552q0 48-18 98.5T728-347Zm-157 71L244-603q-2 12-3 25t-1 26q0 71 59 162.5T480-186q26-23 48.5-45.5T571-276ZM819-28 627-220q-32 34-68 69t-79 71Q319-217 239.5-334.5T160-552q0-32 5-61t14-55L27-820l57-57L876-85l-57 57ZM408-439Zm91-137Z"/></svg>`;

// تابع تشخیص وضعیت دستگاه
const getDeviceStatus = (device) => {
  if (!device.position) return "offline";

  const now = Math.floor(Date.now() / 1000);
  const lastUpdate = device.position.unixtime;
  const halfMinutes = 1800;  

  if (now - lastUpdate > halfMinutes) {
    return "offline";
  }
  if (device.position.flags.isStop) {
    return "online-stopped";
  } else {
    return "online-moving";
  }
};

const createCustomIcon = (device, isSelected = false) => {
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
  const selectedClass = isSelected ? "selected" : "";
  if (status === "online-moving") {
    html = `
      <div class="leaflet-custom-marker ${status} ${selectedClass}">
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
      <div class="leaflet-custom-marker ${status} ${selectedClass}">
        <div class="marker-main">
          <div class="marker-inner">
            ${svg}
          </div>
        </div>
      </div>
    `;
  }
  
  const size = isSelected ? [60, 60] : [52, 52];
  const anchor = isSelected ? [30, 30] : [26, 26];

  return L.divIcon({
    html: html,
    className: `leaflet-custom-marker-container ${status} ${selectedClass}`,
    iconSize: size,
    iconAnchor: anchor
  });
};

const updateMarkers = () => {
  if (!map || !markerClusterGroup) return;

  markerClusterGroup.clearLayers();
  markers.clear();

  // فیلتر دستگاه‌هایی که موقعیت معتبر دارند
  const validDevices = props.devices.filter(device =>
    device.position &&
    device.position.latitude &&
    device.position.longitude &&
    Math.abs(device.position.latitude) <= 90 &&
    Math.abs(device.position.longitude) <= 180
  );

  if (validDevices.length === 0) return;

  // ذخیره محدوده همه دستگاه‌ها
  const latLngs = validDevices.map(device => [
    device.position.latitude,
    device.position.longitude
  ]);
  allDevicesBounds = L.latLngBounds(latLngs);

  validDevices.forEach((device) => {
    const isSelected = props.selectedDeviceId === device.deviceid;
    const marker = L.marker(
      [device.position.latitude, device.position.longitude],
      {
        icon: createCustomIcon(device, isSelected),
        title: `Device ${device.deviceid}`,
        zIndexOffset: isSelected ? 1000 : 0
      }
    );
    
    // اضافه کردن marker به cluster group
    marker.addTo(markerClusterGroup);
    
    // ذخیره مارکر در Map
    markers.set(device.deviceid, marker);
    
    if (isSelected) {
      selectedMarker = marker;
    }
    
    // رویداد کلیک - جلوگیری از باز شدن خودکار cluster
    marker.on('click', (e) => {
      L.DomEvent.stopPropagation(e);
      
      // باز کردن خوشه اگر در یک خوشه است
      if (markerClusterGroup.hasLayer(marker)) {
        const visibleParent = markerClusterGroup.getVisibleParent(marker);
        if (visibleParent && visibleParent !== marker && visibleParent._childCount > 1) {
          // باز کردن خوشه
          markerClusterGroup.zoomToShowLayer(marker, () => {
            emit('device-selected', device);
          });
          return;
        }
      }
      
      emit('device-selected', device);
    });
  });

  // اگر دستگاه انتخاب شده داریم، روی آن زوم کن
  if (props.selectedDeviceId && selectedMarker) {
    zoomToMarker(selectedMarker);
  } else if (validDevices.length > 0) {
    // نمایش همه دستگاه‌ها
    fitToAllDevices();
  }
};

const zoomToMarker = (marker) => {
  if (!map || !marker) return;
  
  isZoomingFromClick = true;
  
  const latLng = marker.getLatLng();
  map.flyTo(latLng, 17, {
    duration: 0.8,
    easeLinearity: 0.25
  });
  
  // بعد از پایان انیمیشن، flag را بازنشانی کن
  setTimeout(() => {
    isZoomingFromClick = false;
    // اگر هنوز دستگاه انتخاب شده‌ای وجود ندارد، فیت تو مپ کن
    if (!props.selectedDeviceId) {
      fitToAllDevices();
    }
  }, 800);
};

const fitToAllDevices = () => {
  if (!map || !allDevicesBounds) {
    if (map) {
      map.setView([35.699, 51.369], 12);
    }
    return;
  }
  
  try {
    // بررسی اندازه bounds
    const boundsSize = allDevicesBounds.getNorthEast().distanceTo(allDevicesBounds.getSouthWest());
    
    if (boundsSize < 100) {
      const center = allDevicesBounds.getCenter();
      map.flyTo(center, 15, {
        duration: 0.8,
        easeLinearity: 0.25
      });
    } else {
      map.flyToBounds(allDevicesBounds, {
        padding: [50, 50],
        duration: 0.8,
        easeLinearity: 0.25,
        maxZoom: 15
      });
    }
  } catch (error) {
    console.error("Error fitting bounds:", error);
    if (map) {
      map.setView([35.699, 51.369], 12);
    }
  }
};

// تماشای تغییرات selectedDeviceId
watch(() => props.selectedDeviceId, (newDeviceId, oldDeviceId) => {
  if (!map) return;
  
  // حذف حالت انتخاب از مارکر قبلی
  if (oldDeviceId && markers.has(oldDeviceId)) {
    const oldMarker = markers.get(oldDeviceId);
    const oldDevice = props.devices.find(d => d.deviceid === oldDeviceId);
    if (oldMarker && oldDevice) {
      oldMarker.setIcon(createCustomIcon(oldDevice, false));
      oldMarker.setZIndexOffset(0);
    }
  }
  
  // اعمال حالت انتخاب به مارکر جدید
  if (newDeviceId && markers.has(newDeviceId)) {
    const newMarker = markers.get(newDeviceId);
    const newDevice = props.devices.find(d => d.deviceid === newDeviceId);
    if (newMarker && newDevice) {
      newMarker.setIcon(createCustomIcon(newDevice, true));
      newMarker.setZIndexOffset(1000);
      selectedMarker = newMarker;
      
      // باز کردن خوشه اگر در یک خوشه است
      if (markerClusterGroup.hasLayer(newMarker)) {
        const visibleParent = markerClusterGroup.getVisibleParent(newMarker);
        if (visibleParent && visibleParent !== newMarker) {
          markerClusterGroup.zoomToShowLayer(newMarker, () => {
            // پس از باز شدن خوشه، روی مارکر زوم کن
            setTimeout(() => {
              zoomToMarker(newMarker);
            }, 300);
          });
        } else {
          zoomToMarker(newMarker);
        }
      } else {
        zoomToMarker(newMarker);
      }
    }
  } else {
    // اگر دستگاه انتخاب شده نیست، از زوم خارج شو
    selectedMarker = null;
    
    // اگر در حال زوم نیستیم، بلافاصله فیت تو مپ کن
    if (!isZoomingFromClick) {
      fitToAllDevices();
    } else {
      // اگر در حال زوم هستیم، صبر کن تا زوم تمام شود
      // (تابع zoomToMarker پس از اتمام، خودش fitToAllDevices را صدا می‌زند)
    }
  }
}, { immediate: true });

// Lifecycle
onMounted(() => {
  map = L.map(mapContainer.value, {
    zoomControl: false,
    attributionControl: false,
    preferCanvas: true
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

  // گروه خوشه‌بندی با تنظیمات بهبود یافته
  markerClusterGroup = L.markerClusterGroup({
    chunkedLoading: true,
    maxClusterRadius: 60,
    spiderfyOnMaxZoom: true,
    showCoverageOnHover: false,
    zoomToBoundsOnClick: false,
    disableClusteringAtZoom: 16,
    iconCreateFunction: function(cluster) {
      const count = cluster.getChildCount();
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
  
  // اضافه کردن کنترل بازگشت به نمای کلی
  const customControl = L.control({ position: 'topright' });
  customControl.onAdd = function() {
    const div = L.DomUtil.create('div', 'custom-map-control');
    div.innerHTML = `
      <button class="reset-view-btn" title="نمای کلی">
        <svg xmlns="http://www.w3.org/2000/svg" height="20px" viewBox="0 -960 960 960" width="20px" fill="currentColor">
          <path d="M480-80q-83 0-156-31.5T197-197q-54-54-85.5-127T80-480q0-83 31.5-156T197-763q54-54 127-85.5T480-880q83 0 156 31.5T763-763q54 54 85.5 127T880-480q0 83-31.5 156T763-197q-54 54-127 85.5T480-80Zm0-80q134 0 227-93t93-227q0-134-93-227t-227-93q-134 0-227 93t-93 227q0 134 93 227t227 93Zm0-320Z"/>
        </svg>
      </button>
    `;
    
    div.addEventListener('click', () => {
      emit('device-selected', null);
      fitToAllDevices();
    });
    
    return div;
  };
  customControl.addTo(map);
  
  // رویداد کلیک روی نقشه برای deselect
  map.on('click', (e) => {
    if (e.originalEvent && e.originalEvent.target && 
        !e.originalEvent.target.closest('.leaflet-marker-icon') &&
        !e.originalEvent.target.closest('.leaflet-cluster-custom') &&
        !e.originalEvent.target.closest('.custom-map-control')) {
      emit('device-selected', null);
    }
  });
  
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
/* استایل‌ها همانند قبل بدون تغییر */
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
  transition: all 0.3s ease;
}

:deep(.leaflet-custom-marker.selected) {
  width: 60px;
  height: 60px;
  z-index: 1000 !important;
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

:deep(.leaflet-custom-marker.selected .marker-main) {
  width: 60px;
  height: 60px;
  border-width: 4px;
  box-shadow:
    0 6px 20px rgba(0, 0, 0, 0.6),
    0 0 0 3px rgba(255, 255, 255, 0.3),
    0 0 0 6px rgba(255, 107, 48, 0.2);
}

:deep(.leaflet-custom-marker.online-moving .marker-main) {
  border-color: #1db4f9;
}

:deep(.leaflet-custom-marker.selected.online-moving .marker-main) {
  border-color: #1db4f9;
  box-shadow:
    0 6px 20px rgba(0, 0, 0, 0.6),
    0 0 0 3px rgba(255, 255, 255, 0.3),
    0 0 0 6px rgba(29, 180, 249, 0.2);
}

:deep(.leaflet-custom-marker.online-stopped .marker-main) {
  border-color: #ff6b30;
}

:deep(.leaflet-custom-marker.selected.online-stopped .marker-main) {
  border-color: #ff6b30;
  box-shadow:
    0 6px 20px rgba(0, 0, 0, 0.6),
    0 0 0 3px rgba(255, 255, 255, 0.3),
    0 0 0 6px rgba(255, 107, 48, 0.2);
}

:deep(.leaflet-custom-marker.offline .marker-main) {
  border-color: #ffffff;
}

:deep(.leaflet-custom-marker.selected.offline .marker-main) {
  border-color: #ffffff;
  box-shadow:
    0 6px 20px rgba(0, 0, 0, 0.6),
    0 0 0 3px rgba(255, 255, 255, 0.3),
    0 0 0 6px rgba(255, 255, 255, 0.2);
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

:deep(.leaflet-custom-marker.selected .marker-inner) {
  width: 42px;
  height: 42px;
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

:deep(.leaflet-custom-marker.selected.online-moving .marker-inner svg),
:deep(.leaflet-custom-marker.selected.online-stopped .marker-inner svg) {
  width: 24px;
  height: 24px;
}

:deep(.leaflet-custom-marker.offline .marker-inner svg) {
  width: 18px;
  height: 18px;
}

:deep(.leaflet-custom-marker.selected.offline .marker-inner svg) {
  width: 22px;
  height: 22px;
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

:deep(.leaflet-custom-marker.selected .marker-pulse-ring) {
  width: 60px;
  height: 60px;
  border-width: 4px;
}

:deep(.leaflet-custom-marker.selected.online-stopped .marker-pulse-ring) {
  border-color: #ff6b30;
}

:deep(.leaflet-custom-marker.selected.offline .marker-pulse-ring) {
  border-color: #ffffff;
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

:deep(.leaflet-custom-marker-container.selected:hover .marker-main) {
  transform: scale(1.2);
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
  border: 3px solid #ffffff !important;
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
  border: 3px solid #000000;
  animation: leaflet-pulse 2s infinite;
  opacity: 0.8;
  z-index: 5;
  width: 52px !important;
  height: 52px !important;
}

:deep(.cluster-pulse-ring.delay-1) {
  animation-delay: 0.7s;
  border-color: #000000;
}

:deep(.cluster-pulse-ring.delay-2) {
  animation-delay: 1.4s;
  border-color: #000000;
}

:deep(.leaflet-cluster-custom:hover .cluster-main) {
  transform: scale(1.15);
  box-shadow:
    0 6px 20px rgba(0, 0, 0, 0.6),
    0 0 0 3px rgba(255, 255, 255, 0.2);
}

/* دکمه بازگشت به نمای کلی */
:deep(.custom-map-control) {
  background: rgba(0, 0, 0, 0.8);
  border-radius: 4px;
  padding: 2px;
  margin-top: 65px !important;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.3);
}

:deep(.reset-view-btn) {
  background: rgba(255, 255, 255, 0.95);
  border: none;
  border-radius: 4px;
  width: 32px;
  height: 32px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: all 0.2s ease;
  color: #333;
}

:deep(.reset-view-btn:hover) {
  background: white;
  transform: scale(1.05);
  color: #ff6b30;
}

:deep(.reset-view-btn svg) {
  width: 20px;
  height: 20px;
}

/* Responsive */
@media (max-width: 768px) {
  :deep(.leaflet-custom-marker) {
    width: 44px;
    height: 44px;
  }

  :deep(.leaflet-custom-marker.selected) {
    width: 52px;
    height: 52px;
  }

  :deep(.marker-main) {
    width: 44px;
    height: 44px;
  }

  :deep(.leaflet-custom-marker.selected .marker-main) {
    width: 52px;
    height: 52px;
  }

  :deep(.marker-inner) {
    width: 32px;
    height: 32px;
  }

  :deep(.leaflet-custom-marker.selected .marker-inner) {
    width: 38px;
    height: 38px;
  }

  :deep(.leaflet-custom-marker.online-moving .marker-inner svg),
  :deep(.leaflet-custom-marker.online-stopped .marker-inner svg) {
    width: 18px;
    height: 18px;
  }

  :deep(.leaflet-custom-marker.selected.online-moving .marker-inner svg),
  :deep(.leaflet-custom-marker.selected.online-stopped .marker-inner svg) {
    width: 22px;
    height: 22px;
  }

  :deep(.leaflet-custom-marker.offline .marker-inner svg) {
    width: 16px;
    height: 16px;
  }

  :deep(.leaflet-custom-marker.selected.offline .marker-inner svg) {
    width: 20px;
    height: 20px;
  }

  :deep(.marker-pulse-ring) {
    width: 44px;
    height: 44px;
  }

  :deep(.leaflet-custom-marker.selected .marker-pulse-ring) {
    width: 52px;
    height: 52px;
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