<template>
  <div class="unified-map-container">
    <!-- هدر مخصوص حالت نمایش مسیر -->
    <div v-if="mode === 'route'" class="route-header">
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

      <div class="device-header-info" v-if="selectedDevice">
        <div class="device-marker-container" :class="getDeviceStatus(selectedDevice)">
          <div class="device-marker-icon">
            <svg
              v-if="getDeviceStatus(selectedDevice) === 'online-moving'"
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
              v-else-if="getDeviceStatus(selectedDevice) === 'online-stopped'"
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
                d="M560-560q0-33-23.5-56.5T480-640q-10 0-19 2t-17 7l107 107q5-8 7-17t2-19Zm168 213-58-58q25-42 37.5-78.5T720-552q0-109-69.5-178.5T480-800q-44 0-82.5 13.5T328-747l-57-57q43-37 97-56.5T480-880q127 0 223.5 89T800-552q0 48-18 98.5T728-347Zm-157 71L244-603q-2 12-3 25t-1 26q0 71 59 162.5T480-186q26-23 48.5-45.5T571-276ZM819-28 627-220q-32 34-68 69t-79 71Q319-217 239.5-334.5T160-552q0-32 5-61t14-55L27-820l57-57L876-85l-57 57ZM408-439Zm91-137Z"
              />
            </svg>
          </div>
        </div>

        <div class="device-details">
          <div class="device-serial">Serial: {{ selectedDevice.serialnumber || "N/A" }}</div>
          <div class="device-last-seen">{{ selectedDevice.lastSeen || "Just now" }}</div>
        </div>
      </div>

      <div class="header-spacer"></div>
    </div>

    <!-- نقشه واحد -->
    <div ref="mapContainer" class="map-container"></div>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted, watch, computed } from "vue";
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
    default: () => [],
  },
  selectedDeviceId: {
    type: [Number, String],
    default: null,
  },
  routeData: {
    type: Array,
    default: () => [],
  },
  mode: {
    type: String,
    default: "devices", // 'devices' or 'route'
  },
});

const emit = defineEmits(["device-selected", "go-back"]);

// عناصر نقشه
const mapContainer = ref(null);
let map = null;
let markerClusterGroup = null;
let markers = new Map();
let selectedMarker = null;
let allDevicesBounds = null;

// داده‌های مسیر
let routeSegments = [];
let pointMarkers = [];
const selectedDevice = computed(() => {
  return props.devices.find(d => d.deviceid === props.selectedDeviceId);
});

// Status Icons
const onlineMovingSVG = `<svg xmlns="http://www.w3.org/2000/svg" height="40px" viewBox="0 -960 960 960" width="40px" fill="#1db4f9"><path d="M480-480q33 0 56.5-23.5T560-560q0-33-23.5-56.5T480-640q-33 0-56.5 23.5T400-560q0 33 23.5 56.5T480-480Zm0 294q122-112 181-203.5T720-552q0-109-69.5-178.5T480-800q-101 0-170.5 69.5T240-552q0 71 59 162.5T480-186Zm0 106Q319-217 239.5-334.5T160-552q0-150 96.5-239T480-880q127 0 223.5 89T800-552q0 100-79.5 217.5T480-80Zm0-480Z"/></svg>`;

const onlineStoppedSVG = `<svg xmlns="http://www.w3.org/2000/svg" height="40px" viewBox="0 -960 960 960" width="40px" fill="#ff6b30"><path d="M480-480q33 0 56.5-23.5T560-560q0-33-23.5-56.5T480-640q-33 0-56.5 23.5T400-560q0 33 23.5 56.5T480-480Zm0 294q122-112 181-203.5T720-552q0-109-69.5-178.5T480-800q-101 0-170.5 69.5T240-552q0 71 59 162.5T480-186Zm0 106Q319-217 239.5-334.5T160-552q0-150 96.5-239T480-880q127 0 223.5 89T800-552q0 100-79.5 217.5T480-80Zm0-480Z"/></svg>`;

const offlineSVG = `<svg xmlns="http://www.w3.org/2000/svg" height="40px" viewBox="0 -960 960 960" width="40px" fill="#ffffff"><path d="M560-560q0-33-23.5-56.5T480-640q-10 0-19 2t-17 7l107 107q5-8 7-17t2-19Zm168 213-58-58q25-42 37.5-78.5T720-552q0-109-69.5-178.5T480-800q-44 0-82.5 13.5T328-747l-57-57q43-37 97-56.5T480-880q127 0 223.5 89T800-552q0 48-18 98.5T728-347Zm-157 71L244-603q-2 12-3 25t-1 26q0 71 59 162.5T480-186q26-23 48.5-45.5T571-276ZM819-28 627-220q-32 34-68 69t-79 71Q319-217 239.5-334.5T160-552q0-32 5-61t14-55L27-820l57-57L876-85l-57 57ZM408-439Zm91-137Z"/></svg>`;

// توابع کمکی
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

const getSpeedColor = (speed) => {
  if (speed === 0) return "#16a34a";
  if (speed <= 20) return "#16a34a";
  if (speed <= 40) return "#22c55e";
  if (speed <= 60) return "#fbbf24";
  if (speed <= 80) return "#f97316";
  return "#dc2626";
};

// ایجاد ایکون سفارشی برای دستگاه‌ها
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
    iconAnchor: anchor,
  });
};

// ایجاد ایکون نقطه برای مسیر
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

// محاسبه فاصله
const calculateDistance = (lat1, lon1, lat2, lon2) => {
  const R = 6371;
  const dLat = (lat2 - lat1) * Math.PI / 180;
  const dLon = (lon2 - lon1) * Math.PI / 180;
  const a = 
    Math.sin(dLat/2) * Math.sin(dLat/2) +
    Math.cos(lat1 * Math.PI / 180) * Math.cos(lat2 * Math.PI / 180) * 
    Math.sin(dLon/2) * Math.sin(dLon/2);
  const c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1-a));
  return R * c;
};

// رسم مسیر
const drawRoute = () => {
  // حذف مسیر قبلی
  clearRoute();
  
  if (props.routeData.length === 0) return;

  // رسم خطوط مسیر
  for (let i = 0; i < props.routeData.length - 1; i++) {
    const currentPoint = props.routeData[i];
    const nextPoint = props.routeData[i + 1];

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

  // رسم نقاط مسیر
  props.routeData.forEach((point, index) => {
    const pointIcon = createPointIcon(point, index + 1);
    const marker = L.marker([point.latitude, point.longitude], { 
      icon: pointIcon,
      interactive: true
    });
    
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

  // تنظیم محدوده نقشه بر اساس مسیر
  const points = props.routeData.map((point) => [point.latitude, point.longitude]);
  if (points.length > 0) {
    const bounds = L.latLngBounds(points);
    map.fitBounds(bounds, { padding: [50, 50] });
  }
};

// پاک کردن مسیر
const clearRoute = () => {
  routeSegments.forEach((segment) => {
    if (segment && map) map.removeLayer(segment);
  });
  routeSegments = [];

  pointMarkers.forEach((marker) => {
    if (marker && map) map.removeLayer(marker);
  });
  pointMarkers = [];
};

// به‌روزرسانی مارکرها (حالت devices)
const updateMarkers = () => {
  if (!map || !markerClusterGroup) return;

  markerClusterGroup.clearLayers();
  markers.clear();
  clearRoute();

  // فقط در حالت devices مارکرها را نمایش بده
  if (props.mode !== 'devices') return;

  // فیلتر دستگاه‌هایی که موقعیت معتبر دارند
  const validDevices = props.devices.filter(
    (device) =>
      device.position &&
      device.position.latitude &&
      device.position.longitude &&
      Math.abs(device.position.latitude) <= 90 &&
      Math.abs(device.position.longitude) <= 180
  );

  if (validDevices.length === 0) return;

  // ذخیره محدوده همه دستگاه‌ها
  const latLngs = validDevices.map((device) => [
    device.position.latitude,
    device.position.longitude,
  ]);
  allDevicesBounds = L.latLngBounds(latLngs);

  validDevices.forEach((device) => {
    const isSelected = props.selectedDeviceId === device.deviceid;
    const marker = L.marker([device.position.latitude, device.position.longitude], {
      icon: createCustomIcon(device, isSelected),
      title: `Device ${device.deviceid}`,
      zIndexOffset: isSelected ? 1000 : 0,
    });

    marker.addTo(markerClusterGroup);
    markers.set(device.deviceid, marker);

    if (isSelected) {
      selectedMarker = marker;
    }

    marker.on("click", (e) => {
      L.DomEvent.stopPropagation(e);

      if (props.selectedDeviceId === device.deviceid) {
        emit("device-selected", null);
        return;
      }

      if (markerClusterGroup.hasLayer(marker)) {
        const visibleParent = markerClusterGroup.getVisibleParent(marker);
        if (visibleParent && visibleParent !== marker && visibleParent._childCount > 1) {
          markerClusterGroup.zoomToShowLayer(marker, () => {
            emit("device-selected", device);
          });
          return;
        }
      }

      emit("device-selected", device);
    });
  });

  if (props.selectedDeviceId && selectedMarker) {
    zoomToMarker(selectedMarker);
  } else if (validDevices.length > 0) {
    fitToAllDevices();
  }
};

const zoomToMarker = (marker) => {
  if (!map || !marker) return;
  const latLng = marker.getLatLng();
  map.flyTo(latLng, 17, {
    duration: 0.8,
    easeLinearity: 0.25
  });
};

const fitToAllDevices = () => {
  if (!map || !allDevicesBounds) {
    if (map) {
      map.setView([35.699, 51.369], 12);
    }
    return;
  }

  try {
    const boundsSize = allDevicesBounds
      .getNorthEast()
      .distanceTo(allDevicesBounds.getSouthWest());

    if (boundsSize < 100) {
      const center = allDevicesBounds.getCenter();
      map.flyTo(center, 15, {
        duration: 0.8,
        easeLinearity: 0.25,
      });
    } else {
      map.flyToBounds(allDevicesBounds, {
        padding: [50, 50],
        duration: 0.8,
        easeLinearity: 0.25,
        maxZoom: 15,
      });
    }
  } catch (error) {
    console.error("Error fitting bounds:", error);
    if (map) {
      map.setView([35.699, 51.369], 12);
    }
  }
};

const goBack = () => {
  emit("go-back");
};

// واچرها
watch(
  () => props.mode,
  (newMode) => {
    if (!map) return;
    
    if (newMode === 'route') {
      // پاک کردن مارکرهای دستگاه‌ها
      if (markerClusterGroup) {
        markerClusterGroup.clearLayers();
      }
      // رسم مسیر
      drawRoute();
    } else {
      // پاک کردن مسیر و نمایش دستگاه‌ها
      clearRoute();
      updateMarkers();
    }
  },
  { immediate: true }
);

watch(
  () => props.selectedDeviceId,
  (newDeviceId, oldDeviceId) => {
    if (!map || props.mode !== 'devices') return;

    if (oldDeviceId && markers.has(oldDeviceId)) {
      const oldMarker = markers.get(oldDeviceId);
      const oldDevice = props.devices.find((d) => d.deviceid === oldDeviceId);
      if (oldMarker && oldDevice) {
        oldMarker.setIcon(createCustomIcon(oldDevice, false));
        oldMarker.setZIndexOffset(0);
      }
    }

    if (newDeviceId && markers.has(newDeviceId)) {
      const newMarker = markers.get(newDeviceId);
      const newDevice = props.devices.find((d) => d.deviceid === newDeviceId);
      if (newMarker && newDevice) {
        newMarker.setIcon(createCustomIcon(newDevice, true));
        newMarker.setZIndexOffset(1000);
        selectedMarker = newMarker;

        if (markerClusterGroup.hasLayer(newMarker)) {
          const visibleParent = markerClusterGroup.getVisibleParent(newMarker);
          if (visibleParent && visibleParent !== newMarker) {
            markerClusterGroup.zoomToShowLayer(newMarker, () => {
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
      selectedMarker = null;
      fitToAllDevices();
    }
  },
  { immediate: true }
);

watch(
  () => props.routeData,
  () => {
    if (props.mode === 'route' && map) {
      drawRoute();
    }
  },
  { deep: true }
);

watch(
  () => props.devices,
  () => {
    if (props.mode === 'devices') {
      updateMarkers();
    }
  },
  { deep: true }
);

// Lifecycle
onMounted(() => {
  map = L.map(mapContainer.value, {
    zoomControl: false,
    attributionControl: false,
    preferCanvas: true,
  }).setView([35.699, 51.369], 12);

  // اضافه کردن کنترل zoom سفارشی
  L.control
    .zoom({
      position: "topright",
    })
    .addTo(map);

  // اضافه کردن attribution
  L.control
    .attribution({
      position: "bottomright",
      prefix: '© <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a>',
    })
    .addTo(map);

//  L.tileLayer(
//   "https://{s}.basemaps.cartocdn.com/dark_all/{z}/{x}/{y}{r}.png",
//   {
//     attribution:
//       '© <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors © <a href="https://carto.com/attributions">CARTO</a>',
//     subdomains: "abcd",
//     maxZoom: 19,
//   }
// ).addTo(map);
  // تایل لایر
  L.tileLayer(
    "https://{s}.basemaps.cartocdn.com/rastertiles/voyager/{z}/{x}/{y}{r}.png",
    {
      attribution:
        '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> &copy; <a href="https://carto.com/attributions">CARTO</a>',
      subdomains: "abcd",
      maxZoom: 19,
    }
  ).addTo(map);

  // گروه خوشه‌بندی
  markerClusterGroup = L.markerClusterGroup({
    chunkedLoading: true,
    maxClusterRadius: 60,
    spiderfyOnMaxZoom: true,
    showCoverageOnHover: false,
    zoomToBoundsOnClick: false,
    disableClusteringAtZoom: 16,
    iconCreateFunction: function (cluster) {
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
        className: "leaflet-cluster-custom",
        iconSize: L.point(52, 52),
        iconAnchor: [26, 26],
      });
    },
  });

  // فقط در حالت devices خوشه‌بندی را اضافه کن
  if (props.mode === 'devices') {
    map.addLayer(markerClusterGroup);
  }

  markerClusterGroup.on('clusterclick', function (cluster) {
    cluster.layer.zoomToBounds();
  });

  // اضافه کردن کنترل بازگشت به نمای کلی (فقط در حالت devices)
  if (props.mode === 'devices') {
    const customControl = L.control({ position: "topright" });
    customControl.onAdd = function () {
      const div = L.DomUtil.create("div", "custom-map-control");
      div.innerHTML = `
        <button class="reset-view-btn" title="نمای کلی">
          <svg xmlns="http://www.w3.org/2000/svg" height="20px" viewBox="0 -960 960 960" width="20px" fill="currentColor">
            <path d="M480-80q-83 0-156-31.5T197-197q-54-54-85.5-127T80-480q0-83 31.5-156T197-763q54-54 127-85.5T480-880q83 0 156 31.5T763-763q54 54 85.5 127T880-480q0 83-31.5 156T763-197q-54 54-127 85.5T480-80Zm0-80q134 0 227-93t93-227q0-134-93-227t-227-93q-134 0-227 93t-93 227q0 134 93 227t227 93Zm0-320Z"/>
          </svg>
        </button>
      `;

      div.addEventListener("click", () => {
        emit("device-selected", null);
        fitToAllDevices();
      });

      return div;
    };
    customControl.addTo(map);
  }

  // رویداد کلیک روی نقشه
  map.on("click", (e) => {
    if (
      e.originalEvent &&
      e.originalEvent.target &&
      !e.originalEvent.target.closest(".leaflet-marker-icon") &&
      !e.originalEvent.target.closest(".leaflet-cluster-custom") &&
      !e.originalEvent.target.closest(".custom-map-control") &&
      !e.originalEvent.target.closest(".route-header")
    ) {
      if (props.mode === 'devices') {
        emit("device-selected", null);
      }
    }
  });

  // راه‌اندازی اولیه بر اساس حالت
  if (props.mode === 'route') {
    drawRoute();
  } else {
    updateMarkers();
  }
});

onUnmounted(() => {
  if (map) {
    map.remove();
  }
});
</script>

<style scoped>
.unified-map-container {
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
  width: 400px;
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
  background: #f8f9fa;
}

/* استایل‌های مربوط به حالت devices (از کد اول) */
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
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.4), 0 0 0 2px rgba(255, 255, 255, 0.1);
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
  box-shadow: 0 6px 20px rgba(0, 0, 0, 0.6), 0 0 0 3px rgba(255, 255, 255, 0.3),
    0 0 0 6px rgba(255, 107, 48, 0.2);
}

:deep(.leaflet-custom-marker.online-moving .marker-main) {
  border-color: #1db4f9;
}

:deep(.leaflet-custom-marker.selected.online-moving .marker-main) {
  border-color: #1db4f9;
  box-shadow: 0 6px 20px rgba(0, 0, 0, 0.6), 0 0 0 3px rgba(255, 255, 255, 0.3),
    0 0 0 6px rgba(29, 180, 249, 0.2);
}

:deep(.leaflet-custom-marker.online-stopped .marker-main) {
  border-color: #ff6b30;
}

:deep(.leaflet-custom-marker.selected.online-stopped .marker-main) {
  border-color: #ff6b30;
  box-shadow: 0 6px 20px rgba(0, 0, 0, 0.6), 0 0 0 3px rgba(255, 255, 255, 0.3),
    0 0 0 6px rgba(255, 107, 48, 0.2);
}

:deep(.leaflet-custom-marker.offline .marker-main) {
  border-color: #ffffff;
}

:deep(.leaflet-custom-marker.selected.offline .marker-main) {
  border-color: #ffffff;
  box-shadow: 0 6px 20px rgba(0, 0, 0, 0.6), 0 0 0 3px rgba(255, 255, 255, 0.3),
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
    transform: translate(-50%, -50%) scale(2);
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
  font-family: "Inter", sans-serif;
}

:deep(.leaflet-custom-marker-container:hover .marker-main) {
  transform: scale(1.15);
  box-shadow: 0 6px 20px rgba(0, 0, 0, 0.6), 0 0 0 3px rgba(255, 255, 255, 0.2);
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
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.4), 0 0 0 2px rgba(255, 255, 255, 0.1);
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
  font-family: "Inter", sans-serif;
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
  box-shadow: 0 6px 20px rgba(0, 0, 0, 0.6), 0 0 0 3px rgba(255, 255, 255, 0.2);
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

/* استایل‌های مربوط به حالت route (از کد دوم) */
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

/* Responsive */
@media (max-width: 768px) {
  .route-header {
    left: 5px;
    right: 5px;
    padding: 0 15px;
    height: 60px;
    width: auto;
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

  :deep(.point-circle) {
    width: 28px;
    height: 28px;
    font-size: 12px;
  }
}

:deep(.cluster-custom-marker),
:deep(.cluster-main),
:deep(.cluster-inner) {
  border-radius: 50% !important;
}
</style>