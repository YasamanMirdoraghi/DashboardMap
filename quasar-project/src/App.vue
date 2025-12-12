<template>
  <!-- کانتینر اصلی با قاب و پدینگ -->
  <div class="main-frame">
    <div class="dashboard-container" :class="themeClass">
      <!-- نقشه -->
      <MapContainer @device-selected="handleDeviceSelected" :devices="devices" />

      <!-- منوی داشبورد -->
      <DashboardMenu
        :is-dark-mode="isDarkMode"
        :devices="devices"
        @toggle-dark-mode="isDarkMode = $event"
        @device-selected="handleDeviceSelected"
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
import { ref, computed, onMounted, onBeforeUnmount } from "vue";
import MapContainer from "./components/MapContainer/MapContainer.vue";
import DashboardMenu from "./components/DashboardMenu/DashboardMenu.vue";
import FloatingCards from "./components/FloatingCards/FloatingCards.vue";

// Reactive state
const selectedDevice = ref(null);
const isDarkMode = ref(true);
const devices = ref([]);
let updateInterval = null;

// تابع برای محاسبه زمان گذشته از lastUpdate تا الآن (انگلیسی)
const calculateLastSeen = (unixtime) => {
  const now = Math.floor(Date.now() / 1000);
  const diffInSeconds = now - unixtime;

  if (diffInSeconds < 5) {
    return "Just now";
  } else if (diffInSeconds < 60) {
    return `${diffInSeconds} seconds ago`;
  } else if (diffInSeconds < 120) {
    return "1 minute ago";
  } else if (diffInSeconds < 3600) {
    const minutes = Math.floor(diffInSeconds / 60);
    return `${minutes} minutes ago`;
  } else if (diffInSeconds < 7200) {
    return "1 hour ago";
  } else if (diffInSeconds < 86400) {
    const hours = Math.floor(diffInSeconds / 3600);
    return `${hours} hours ago`;
  } else if (diffInSeconds < 172800) {
    return "1 day ago";
  } else if (diffInSeconds < 2592000) {
    // 30 روز
    const days = Math.floor(diffInSeconds / 86400);
    return `${days} days ago`;
  } else if (diffInSeconds < 5184000) {
    // 60 روز
    return "1 month ago";
  } else if (diffInSeconds < 31536000) {
    // 365 روز
    const months = Math.floor(diffInSeconds / 2592000);
    return `${months} months ago`;
  } else if (diffInSeconds < 63072000) {
    // 2 سال
    return "1 year ago";
  } else {
    const years = Math.floor(diffInSeconds / 31536000);
    return `${years} years ago`;
  }
};

// تابع برای به‌روزرسانی lastSeen همه دستگاه‌ها
const updateLastSeen = () => {
  devices.value = devices.value.map((device) => ({
    ...device,
    lastSeen: calculateLastSeen(device.position.unixtime),
  }));
};

// Computed property برای تم
const themeClass = computed(() => ({
  "dark-theme": isDarkMode.value,
  "light-theme": !isDarkMode.value,
}));

// Methods
const handleDeviceSelected = (device) => {
  selectedDevice.value = device;
};

// داده‌های نمونه برای دستگاه‌ها با ساختار صحیح
const initializeDevices = () => {
  const now = Math.floor(Date.now() / 1000);
  const oneHourAgo = now - 3600;
  const twoHoursAgo = now - 7200;
  const oneDayAgo = now - 86400;
  const threeDaysAgo = now - 259200;

  devices.value = [
    {
      deviceid: 1,
      position: {
        alarm: "Unknown Alarm: 231.",
        alarm_code: 231,
        batt_v: 3500,
        bearing: 336,
        cid: 5771,
        flags: {
          isCIDValid: true,
          isCharging: false,
          isCoilActive: false,
          isFixed: true,
          isMechanicClosed: true,
          isRopeClosed: true,
          isSimTwo: false,
          isStop: false,
          isUnlockAllowed: true,
        },
        geofenceStatus: 272,
        gsm_sig: 85,
        humidity: 45,
        id: 652,
        lac: 45223,
        latitude: 35.6892,
        longitude: 51.3890,
        pdop: 1.04,
        positionUnixtime: now,
        satellites: 12,
        speed: 90,
        technology: "4G",
        temperature: 25,
        unixtime: now,
      },
      serialnumber: "123456",
      sim1_id: null,
      sim2_id: null,
      type: 1,
      operator: "MCI",

    },
    {
      deviceid: 2,
      position: {
        alarm: "Unknown Alarm: 231.",
        alarm_code: 231,
        batt_v: 3300,
        bearing: 45,
        cid: 5987,
        flags: {
          isCIDValid: true,
          isCharging: true,
          isCoilActive: true,
          isFixed: true,
          isMechanicClosed: false,
          isRopeClosed: false,
          isSimTwo: false,
          isStop: true,
          isUnlockAllowed: false,
        },
        geofenceStatus: 272,
        gsm_sig: 75,
        humidity: 55,
        id: 653,
        lac: 45224,
        latitude: 35.7440,
        longitude: 51.3755,
        pdop: 1.8,
        positionUnixtime: oneHourAgo,
        satellites: 8,
        speed: 0,
        technology: "4G",
        temperature: 22,
        unixtime: oneHourAgo,
      },
      serialnumber: "123457",
      sim1_id: null,
      sim2_id: null,
      type: 1,
      operator: "MCI",
    },
    {
      deviceid: 3,
      position: {
        alarm: "Unknown Alarm: 231.",
        alarm_code: 231,
        batt_v: 3000,
        bearing: 120,
        cid: 5987,
        flags: {
          isCIDValid: false,
          isCharging: false,
          isCoilActive: false,
          isFixed: false,
          isMechanicClosed: true,
          isRopeClosed: true,
          isSimTwo: false,
          isStop: true,
          isUnlockAllowed: true,
        },
        geofenceStatus: 272,
        gsm_sig: 25,
        humidity: 60,
        id: 654,
        lac: 45225,
        latitude: 35.7153,
        longitude: 51.4043,
        pdop: 5.2,
        positionUnixtime: twoHoursAgo,
        satellites: 3,
        speed: 0,
        technology: "3G",
        temperature: 28,
        unixtime: twoHoursAgo,
      },
      serialnumber: "123458",
      sim1_id: null,
      sim2_id: null,
      type: 1,
    },
    {
      deviceid: 4,
      position: {
        alarm: "Unknown Alarm: 231.",
        alarm_code: 231,
        batt_v: 3100,
        bearing: 270,
        cid: 5987,
        flags: {
          isCIDValid: true,
          isCharging: false,
          isCoilActive: true,
          isFixed: true,
          isMechanicClosed: false,
          isRopeClosed: false,
          isSimTwo: false,
          isStop: false,
          isUnlockAllowed: false,
        },
        geofenceStatus: 272,
        gsm_sig: 65,
        humidity: 35,
        id: 655,
        lac: 45226,
        latitude: 35.7004,
        longitude: 51.3376,
        pdop: 2.1,
        positionUnixtime: now,
        satellites: 6,
        speed: 45,
        technology: "4G",
        temperature: 30,
        unixtime: now,
      },
      serialnumber: "123459",
      sim1_id: null,
      sim2_id: null,
      type: 1,
    },
    {
      deviceid: 5,
      position: {
        alarm: "Unknown Alarm: 231.",
        alarm_code: 231,
        batt_v: 2900,
        bearing: 180,
        cid: 5987,
        flags: {
          isCIDValid: false,
          isCharging: false,
          isCoilActive: false,
          isFixed: false,
          isMechanicClosed: true,
          isRopeClosed: true,
          isSimTwo: false,
          isStop: true,
          isUnlockAllowed: true,
        },
        geofenceStatus: 272,
        gsm_sig: 15,
        humidity: 70,
        id: 656,
        lac: 45227,
        latitude: 35.7595,
        longitude: 51.4381,
        pdop: 7.5,
        positionUnixtime: oneDayAgo,
        satellites: 2,
        speed: 0,
        technology: "2G",
        temperature: 18,
        unixtime: oneDayAgo,
      },
      serialnumber: "123450",
      sim1_id: null,
      sim2_id: null,
      type: 1,
    },
    {
      deviceid: 6,
      position: {
        alarm: "Unknown Alarm: 231.",
        alarm_code: 231,
        batt_v: 3400,
        bearing: 90,
        cid: 5987,
        flags: {
          isCIDValid: true,
          isCharging: true,
          isCoilActive: true,
          isFixed: true,
          isMechanicClosed: false,
          isRopeClosed: false,
          isSimTwo: false,
          isStop: false,
          isUnlockAllowed: false,
        },
        geofenceStatus: 272,
        gsm_sig: 55,
        humidity: 40,
        id: 657,
        lac: 45228,
        latitude: 35.6920,
        longitude: 51.3910,
        pdop: 1.5,
        positionUnixtime: now,
        satellites: 10,
        speed: 80,
        technology: "3G",
        temperature: 27,
        unixtime: now,
      },
      serialnumber: "123453",
      sim1_id: null,
      sim2_id: null,
      type: 1,
    },
    {
      deviceid: 7,
      position: {
        alarm: "Unknown Alarm: 231.",
        alarm_code: 231,
        batt_v: 3200,
        bearing: 315,
        cid: 5987,
        flags: {
          isCIDValid: true,
          isCharging: false,
          isCoilActive: true,
          isFixed: true,
          isMechanicClosed: true,
          isRopeClosed: true,
          isSimTwo: false,
          isStop: true,
          isUnlockAllowed: false,
        },
        geofenceStatus: 272,
        gsm_sig: 80,
        humidity: 50,
        id: 658,
        lac: 45229,
        latitude: 35.6900,
        longitude: 51.3920,
        pdop: 1.2,
        positionUnixtime: now,
        satellites: 9,
        speed: 0,
        technology: "4G",
        temperature: 23,
        unixtime: now,
      },
      serialnumber: "123451",
      sim1_id: null,
      sim2_id: null,
      type: 1,
    },
    {
      deviceid: 8,
      position: {
        alarm: "Unknown Alarm: 231.",
        alarm_code: 231,
        batt_v: 3600,
        bearing: 225,
        cid: 5987,
        flags: {
          isCIDValid: true,
          isCharging: true,
          isCoilActive: false,
          isFixed: true,
          isMechanicClosed: true,
          isRopeClosed: true,
          isSimTwo: false,
          isStop: true,
          isUnlockAllowed: true,
        },
        geofenceStatus: 272,
        gsm_sig: 90,
        humidity: 65,
        id: 659,
        lac: 45230,
        latitude: 35.7450,
        longitude: 51.3760,
        pdop: 1.1,
        positionUnixtime: threeDaysAgo,
        satellites: 11,
        speed: 0,
        technology: "2G",
        temperature: 20,
        unixtime: threeDaysAgo,
      },
      serialnumber: "123452",
      sim1_id: null,
      sim2_id: null,
      type: 1,
    },
  ].map((device) => ({
    ...device,
    lastSeen: calculateLastSeen(device.position.unixtime),
  }));
};

onMounted(() => {
  initializeDevices();

  // هر 30 ثانیه lastSeen را به‌روز کنید
  updateInterval = setInterval(updateLastSeen, 30000);
});

onBeforeUnmount(() => {
  // حذف interval وقتی کامپوننت از بین می‌رود
  if (updateInterval) {
    clearInterval(updateInterval);
  }
});
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
  padding: 0;
  margin: 0;
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
  transition: background-color 0.3s ease, color 0.3s ease;
}

/* حالت‌های تم */
.dashboard-container.dark-theme {
  background: var(--bg-primary);
  color: var(--text-primary);
}

.dashboard-container.light-theme {
  background: var(--bg-primary);
  color: var(--text-primary);
}

/* Responsive Design */
@media (max-width: 768px) {
  .main-frame {
    padding: 0;
  }

  .dashboard-container {
    border-radius: 0;
  }
}
</style>
