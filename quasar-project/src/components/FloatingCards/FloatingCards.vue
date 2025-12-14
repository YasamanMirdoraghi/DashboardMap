<template>
  <!-- Glass Device Card Popup -->
  <transition name="slide-up">
    <div v-if="selectedDevice" class="glass-device-card">
      <!-- Header with gradient -->
      <div class="glass-header">
        <div class="header-content">
          <div class="device-main-info">
            <div class="status-indicator" :class="getDeviceStatus(selectedDevice)">
              <q-icon :name="getStatusIcon(getDeviceStatus(selectedDevice))" />
            </div>
            <div class="device-title-section">
              <h3 class="device-name">Device {{ selectedDevice.deviceid }}</h3>
              <div class="device-id">Serial: {{ selectedDevice.serialnumber }}</div>
              <div class="device-type" v-if="selectedDevice.type">
                {{ formatType(selectedDevice.type) }}
              </div>
            </div>
          </div>

          <div class="header-actions">
            <q-btn
              flat
              round
              dense
              icon="close"
              size="sm"
              class="glass-btn"
              @click="closeCard"
            />
          </div>
        </div>
      </div>

      <!-- Card Content -->
      <div class="glass-content">
        <!-- Status & Last Seen -->
        <div class="status-section">
          <div class="status-badge" :class="getDeviceStatus(selectedDevice)">
            <span class="status-text">{{ getStatusText(getDeviceStatus(selectedDevice)) }}</span>
            <span class="last-seen">{{ selectedDevice.lastSeen }}</span>
          </div>
          <div class="last-update">
            <q-icon name="schedule" size="14px" />
            <span>{{ formatDate(selectedDevice.position.unixtime) }}</span>
          </div>
        </div>

        <!-- Location Info -->
        <div class="info-section location-info">
          <div class="section-title">
            <q-icon name="location_on" />
            <span>Location</span>
          </div>
          <div class="coordinates">
            <div class="coord-item">
              <span class="coord-label">Lat:</span>
              <span class="coord-value">{{ selectedDevice.position.latitude?.toFixed(6) || 'N/A' }}</span>
            </div>
            <div class="coord-item">
              <span class="coord-label">Lng:</span>
              <span class="coord-value">{{ selectedDevice.position.longitude?.toFixed(6) || 'N/A' }}</span>
            </div>
          </div>
          <div class="address-info" v-if="selectedDevice.position.address">
            <q-icon name="place" size="12px" />
            <span>{{ selectedDevice.position.address }}</span>
          </div>
          <div class="fixed-status" v-if="selectedDevice.position.flags">
            <div class="fixed-badge" :class="getFixedStatus(selectedDevice.position.flags)">
              <q-icon :name="selectedDevice.position.flags.isFixed ? 'check_circle' : 'gps_off'" size="12px" />
              <span>{{ selectedDevice.position.flags.isFixed ? 'Fixed' : 'Not Fixed' }}</span>
            </div>
          </div>
        </div>

        <!-- Device Stats Grid -->
        <div class="stats-grid">
          <!-- Battery -->
          <div class="stat-card" :class="getBatteryStatus(selectedDevice.position.batt_v)">
            <div class="stat-icon">
              <q-icon name="battery_full" />
            </div>
            <div class="stat-info">
              <div class="stat-label">Battery</div>
              <div class="stat-value">{{ formatBattery(selectedDevice.position.batt_v) }}</div>
            </div>
          </div>

          <!-- Speed -->
          <div class="stat-card" :class="getSpeedStatus(selectedDevice.position.speed)">
            <div class="stat-icon">
              <q-icon name="speed" />
            </div>
            <div class="stat-info">
              <div class="stat-label">Speed</div>
              <div class="stat-value">{{ selectedDevice.position.speed || 0 }} km/h</div>
            </div>
          </div>

          <!-- Lock -->
          <div class="stat-card" :class="getLockStatus(selectedDevice.position.flags)">
            <div class="stat-icon">
              <q-icon :name="selectedDevice.position.flags?.isCoilActive ? 'lock' : 'lock_open'" />
            </div>
            <div class="stat-info">
              <div class="stat-label">Lock</div>
              <div class="stat-value">{{ getLockText(selectedDevice.position.flags) }}</div>
            </div>
          </div>

          <!-- GSM -->
          <div class="stat-card" :class="getGSMStatus(selectedDevice.position.gsm_sig)">
            <div class="stat-icon">
              <q-icon name="signal_cellular_alt" />
            </div>
            <div class="stat-info">
              <div class="stat-label">GSM</div>
              <div class="stat-value">{{ formatGSM(selectedDevice.position.gsm_sig) }}</div>
            </div>
          </div>

          <!-- GNSS -->
          <div class="stat-card" :class="getGNSSStatus(selectedDevice.position.satellites, selectedDevice.position.pdop)">
            <div class="stat-icon">
              <q-icon name="satellite" />
            </div>
            <div class="stat-info">
              <div class="stat-label">GNSS</div>
              <div class="stat-value">{{ formatGNSS(selectedDevice.position.pdop) }}</div>
            </div>
          </div>

          <!-- Temperature -->
          <div class="stat-card" :class="getTemperatureStatus(selectedDevice.position.temperature)">
            <div class="stat-icon">
              <q-icon name="thermostat" />
            </div>
            <div class="stat-info">
              <div class="stat-label">Temperature</div>
              <div class="stat-value">{{ selectedDevice.position.temperature || 0 }}°C</div>
            </div>
          </div>

          <!-- Humidity -->
          <div class="stat-card" :class="getHumidityStatus(selectedDevice.position.humidity)">
            <div class="stat-icon">
              <q-icon name="water_drop" />
            </div>
            <div class="stat-info">
              <div class="stat-label">Humidity</div>
              <div class="stat-value">{{ selectedDevice.position.humidity || 0 }}%</div>
            </div>
          </div>

          <!-- Network -->
          <div class="stat-card network">
            <div class="stat-icon">
              <q-icon name="network_check" />
            </div>
            <div class="stat-info">
              <div class="stat-label">Network</div>
              <div class="stat-value">LAC:{{ selectedDevice.position.lac || 'N/A' }}</div>
            </div>
          </div>
        </div>

        <!-- Rope Status -->
        <div class="rope-section">
          <div class="section-title">
            <q-icon name="cable" />
            <span>Rope Status</span>
          </div>
          <div class="rope-status" :class="getRopeStatus(selectedDevice.position.flags)">
            <q-icon :name="getRopeIcon(selectedDevice.position.flags)" />
            <span>{{ getRopeText(selectedDevice.position.flags) }}</span>
          </div>
        </div>

        <!-- Alarm Status -->
        <div v-if="selectedDevice.position.alarm" class="alarm-section">
          <div class="section-title">
            <q-icon name="notifications" />
            <span>Alarm</span>
          </div>
          <div class="alarm-status">
            <q-icon name="warning" color="orange" />
            <span>{{ selectedDevice.position.alarm }}</span>
          </div>
        </div>

        <!-- Action Buttons -->
        <div class="action-buttons">
          <q-btn
            flat
            icon="history"
            label="Details"
            class="glass-action-btn"
            @click="Details"
          />
        </div>
      </div>
    </div>
  </transition>
</template>

<script setup>
const props = defineProps({
  selectedDevice: Object
});

const emit = defineEmits(['close-device', 'view-history']);

const closeCard = () => {
  emit('close-device');
};

// Status Helper Functions - مطابق DashboardMenu
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

const getStatusIcon = (status) => {
  const icons = {
    'online-moving': 'directions_car',
    'online-stopped': 'pause_circle',
    'offline': 'signal_wifi_off'
  };
  return icons[status] || 'device_unknown';
};

const getStatusText = (status) => {
  const texts = {
    'online-moving': 'Moving',
    'online-stopped': 'Stopped',
    'offline': 'Offline'
  };
  return texts[status] || 'Unknown';
};

// Format Functions - مطابق DashboardMenu
const formatGSM = (gsm_sig) => {
  return `${gsm_sig}%`;
};

const formatGNSS = (pdop) => {
  return `${pdop}`;
};

const formatType = (type) => {
  if (type == 1) return "MCI";
  return type || "Unknown";
};

const formatBattery = (batt_v) => {
  // batt_v in millivolts
  const voltage = batt_v / 1000;
  return `${voltage.toFixed(2)}V`;
};

const getLockText = (flags) => {
  if (!flags) return "Unknown";
  return flags.isCoilActive ? "Locked" : "Unlocked";
};

const getRopeText = (flags) => {
  if (!flags) return "Unknown";
  if (flags.isRopeClosed && flags.isMechanicClosed) {
    return "Closed";
  } else if (!flags.isRopeClosed && !flags.isMechanicClosed) {
    return "Open";
  } else {
    return "Partially";
  }
};

// Status Detection Functions - مطابق DashboardMenu
const getBatteryStatus = (batt_v) => {
  if (!batt_v) return "status-critical";
  const voltage = batt_v;
  if (voltage >= 3400) return "status-good";
  if (voltage >= 3200) return "status-warning";
  return "status-critical";
};

const getLockStatus = (flags) => {
  if (!flags) return "status-critical";
  return flags.isCoilActive ? "status-critical" : "status-good";
};

const getGSMStatus = (gsm_sig) => {
  if (!gsm_sig) return "status-critical";
  if (gsm_sig >= 70) return "status-good";
  if (gsm_sig >= 40) return "status-warning";
  return "status-critical";
};

const getSpeedStatus = (speed) => {
  if (speed === undefined || speed === null) return "status-good";
  if (speed >= 0 && speed <= 60) return "status-good";
  if (speed > 60 && speed <= 90) return "status-warning";
  return "status-critical";
};

const getRopeStatus = (flags) => {
  if (!flags) return "status-critical";
  if (flags.isRopeClosed && flags.isMechanicClosed) {
    return "status-good";
  } else if (!flags.isRopeClosed && !flags.isMechanicClosed) {
    return "status-critical";
  } else {
    return "status-warning";
  }
};

const getRopeIcon = (flags) => {
  if (!flags) return "help";
  if (flags.isRopeClosed && flags.isMechanicClosed) {
    return 'check_circle';
  } else if (!flags.isRopeClosed && !flags.isMechanicClosed) {
    return 'cancel';
  } else {
    return 'warning';
  }
};

const getGNSSStatus = (satellites, pdop) => {
  if (!satellites || !pdop) return "status-critical";
  if (satellites >= 7 && pdop <= 2) return "status-good";
  if (satellites >= 4 && pdop <= 5) return "status-warning";
  return "status-critical";
};

const getTemperatureStatus = (temperature) => {
  if (temperature === undefined || temperature === null) return "status-good";
  if (temperature >= 15 && temperature <= 30) return "status-good";
  if ((temperature >= 10 && temperature < 15) || (temperature > 30 && temperature <= 35))
    return "status-warning";
  return "status-critical";
};

const getHumidityStatus = (humidity) => {
  if (humidity === undefined || humidity === null) return "status-good";
  if (humidity >= 30 && humidity <= 60) return "status-good";
  if ((humidity >= 20 && humidity < 30) || (humidity > 60 && humidity <= 70))
    return "status-warning";
  return "status-critical";
};

const getFixedStatus = (flags) => {
  if (!flags) return "status-critical";
  return flags.isFixed ? "status-good" : "status-critical";
};

const formatDate = (unixtime) => {
  if (!unixtime) return 'N/A';
  const date = new Date(unixtime * 1000);
  return date.toLocaleString('en-US', {
    year: 'numeric',
    month: 'short',
    day: 'numeric',
    hour: '2-digit',
    minute: '2-digit',
    second: '2-digit'
  });
};

const Details = () => {
  emit('view-history', props.selectedDevice);
};
</script>

<style scoped>
/* Glass Morphism Device Card - Base Styles */
.glass-device-card {
  position: fixed;
  bottom: 5px;
  right: 20px;
  width: 420px;
  /* max-height: 95vh; */
  background: rgba(37, 37, 36, 0.95);
  backdrop-filter: blur(20px) saturate(180%);
  -webkit-backdrop-filter: blur(20px) saturate(180%);
  border-radius: 20px;
  border: 1px solid rgba(255, 255, 255, 0.2);
  box-shadow:
    0 8px 32px rgba(0, 0, 0, 0.3),
    0 1px 0 rgba(255, 255, 255, 0.1) inset;
  z-index: 1000;
  overflow: hidden;
  font-family: 'Inter', -apple-system, BlinkMacSystemFont, sans-serif;
  display: flex;
  flex-direction: column;
}

.glass-content {
  padding: 15px;
  background: rgba(255, 255, 255, 0.05);
  overflow-y: auto;
  flex: 1;
}

/* Header with gradient */
.glass-header {
  background: linear-gradient(135deg, rgba(0, 0, 0, 0.95), rgba(255, 255, 255, 0.15));
  padding: 16px 20px;
  border-bottom: 1px solid rgba(255, 255, 255, 0.1);
  flex-shrink: 0;
}

.header-content {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.device-main-info {
  display: flex;
  align-items: center;
  gap: 12px;
}

.status-indicator {
  width: 40px;
  height: 40px;
  border-radius: 12px;
  display: flex;
  align-items: center;
  justify-content: center;
  color: white;
  background: rgba(255, 255, 255, 0.2);
  backdrop-filter: blur(5px);
}

.status-indicator.online-moving {
  background: linear-gradient(135deg, rgba(29, 180, 249, 0.8), rgba(23, 144, 199, 0.8));
}

.status-indicator.online-stopped {
  background: linear-gradient(135deg, rgba(255, 107, 48, 0.8), rgba(230, 96, 43, 0.8));
}

.status-indicator.offline {
  background: linear-gradient(135deg, rgba(255, 255, 255, 0.8), rgba(200, 200, 200, 0.8));
}

.device-title-section {
  color: white;
}

.device-name {
  margin: 0;
  font-size: 18px;
  font-weight: 700;
  letter-spacing: -0.3px;
}

.device-id {
  font-size: 12px;
  opacity: 0.9;
  font-weight: 500;
  color: rgba(255, 255, 255, 0.8);
}

.device-type {
  font-size: 11px;
  color: rgba(255, 255, 255, 0.7);
  background: rgba(255, 255, 255, 0.1);
  padding: 2px 6px;
  border-radius: 4px;
  margin-top: 2px;
  display: inline-block;
}

.header-actions {
  display: flex;
  gap: 8px;
}

.glass-btn {
  color: white;
  background: rgba(255, 255, 255, 0.1);
  backdrop-filter: blur(5px);
  width: 32px;
  height: 32px;
  min-height: 32px;
}

.glass-btn:hover {
  background: rgba(255, 255, 255, 0.2);
}

/* Status Section */
.status-section {
  margin-bottom: 20px;
}

.status-badge {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  padding: 8px 16px;
  border-radius: 12px;
  background: rgba(255, 255, 255, 0.1);
  backdrop-filter: blur(5px);
  border: 1px solid rgba(255, 255, 255, 0.1);
  margin-bottom: 8px;
}

.status-badge.online-moving {
  background: rgba(29, 180, 249, 0.1);
  border-color: rgba(29, 180, 249, 0.3);
}

.status-badge.online-stopped {
  background: rgba(255, 107, 48, 0.1);
  border-color: rgba(255, 107, 48, 0.3);
}

.status-badge.offline {
  background: rgba(255, 255, 255, 0.1);
  border-color: rgba(255, 255, 255, 0.3);
}

.status-text {
  font-weight: 600;
  font-size: 14px;
  color: #ffff;
}

.last-seen {
  font-size: 12px;
  color: #ffff;
  opacity: 0.8;
}

.last-update {
  display: flex;
  align-items: center;
  gap: 6px;
  font-size: 12px;
  color: rgba(255, 255, 255, 0.7);
}

/* Info Sections */
.info-section {
  background: rgba(255, 255, 255, 0.05);
  border-radius: 12px;
  padding: 12px 16px;
  margin-bottom: 16px;
  border: 1px solid rgba(255, 255, 255, 0.1);
}

.section-title {
  display: flex;
  align-items: center;
  gap: 8px;
  font-weight: 600;
  color: #ffff;
  margin-bottom: 12px;
  font-size: 14px;
}

.coordinates {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 12px;
  margin-bottom: 8px;
}

.coord-item {
  display: flex;
  flex-direction: column;
}

.coord-label {
  font-size: 11px;
  color: rgba(255, 255, 255, 0.7);
  text-transform: uppercase;
  letter-spacing: 0.5px;
  margin-bottom: 2px;
}

.coord-value {
  font-size: 14px;
  font-weight: 600;
  color: #ffff;
  font-family: 'JetBrains Mono', monospace;
}

.address-info {
  display: flex;
  align-items: center;
  gap: 6px;
  font-size: 12px;
  color: rgba(255, 255, 255, 0.8);
  padding-top: 8px;
  border-top: 1px solid rgba(255, 255, 255, 0.1);
}

.fixed-status {
  margin-top: 8px;
}

.fixed-badge {
  display: inline-flex;
  align-items: center;
  gap: 6px;
  padding: 4px 8px;
  border-radius: 6px;
  font-size: 12px;
  font-weight: 500;
}

.fixed-badge.status-good {
  background: rgba(76, 175, 80, 0.1);
  color: #4caf50;
  border: 1px solid rgba(76, 175, 80, 0.2);
}

.fixed-badge.status-critical {
  background: rgba(219, 0, 0, 0.1);
  color: #db0000;
  border: 1px solid rgba(219, 0, 0, 0.2);
}

/* Stats Grid */
.stats-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 12px;
  margin-bottom: 20px;
}

.stat-card {
  background: rgba(255, 255, 255, 0.08);
  border-radius: 12px;
  padding: 12px;
  border: 1px solid rgba(255, 255, 255, 0.1);
  transition: all 0.2s ease;
  display: flex;
  align-items: center;
  gap: 10px;
  backdrop-filter: blur(5px);
}

.stat-card:hover {
  transform: translateY(-2px);
  background: rgba(255, 255, 255, 0.12);
  border-color: rgba(255, 255, 255, 0.2);
}

.stat-icon {
  width: 32px;
  height: 32px;
  border-radius: 8px;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
  background: rgba(255, 255, 255, 0.1);
}

.stat-info {
  flex: 1;
  min-width: 0;
}

.stat-label {
  font-size: 11px;
  color: rgba(255, 255, 255, 0.7);
  text-transform: uppercase;
  letter-spacing: 0.5px;
  margin-bottom: 2px;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.stat-value {
  font-size: 14px;
  font-weight: 700;
  color: #ffff;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

/* Status Classes */
.status-good .stat-icon {
  background: rgba(76, 175, 80, 0.15);
  color: #4caf50;
}

.status-warning .stat-icon {
  background: rgba(235, 172, 0, 0.15);
  color: #ebac00;
}

.status-critical .stat-icon {
  background: rgba(219, 0, 0, 0.15);
  color: #db0000;
}

.network .stat-icon {
  background: rgba(139, 92, 246, 0.15);
  color: #8b5cf6;
}

/* Rope Section */
.rope-section {
  background: rgba(255, 255, 255, 0.05);
  border-radius: 12px;
  padding: 12px 16px;
  margin-bottom: 16px;
  border: 1px solid rgba(255, 255, 255, 0.1);
}

.rope-status {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  padding: 8px 16px;
  border-radius: 8px;
  font-weight: 600;
  font-size: 14px;
}

.rope-status.status-good {
  background: rgba(76, 175, 80, 0.1);
  color: #4caf50;
  border: 1px solid rgba(76, 175, 80, 0.2);
}

.rope-status.status-warning {
  background: rgba(235, 172, 0, 0.1);
  color: #ebac00;
  border: 1px solid rgba(235, 172, 0, 0.2);
}

.rope-status.status-critical {
  background: rgba(219, 0, 0, 0.1);
  color: #db0000;
  border: 1px solid rgba(219, 0, 0, 0.2);
}

/* Alarm Section */
.alarm-section {
  background: rgba(255, 165, 0, 0.05);
  border-radius: 12px;
  padding: 12px 16px;
  margin-bottom: 20px;
  border: 1px solid rgba(255, 165, 0, 0.2);
}

.alarm-status {
  display: flex;
  align-items: center;
  gap: 8px;
  color: orange;
  font-size: 14px;
  font-weight: 500;
}

/* Action Buttons */
.action-buttons {
  display: grid;
  grid-template-columns: 1fr;
  gap: 12px;
}

.glass-action-btn {
  background: rgba(255, 255, 255, 0.1);
  backdrop-filter: blur(5px);
  border: 1px solid rgba(255, 255, 255, 0.2);
  border-radius: 10px;
  color: #ffff;
  font-weight: 600;
  font-size: 13px;
  text-transform: uppercase;
  letter-spacing: 0.3px;
  height: 40px;
  transition: all 0.2s ease;
}

.glass-action-btn:hover {
  background: rgba(255, 255, 255, 0.2);
  border-color: rgba(255, 255, 255, 0.3);
  transform: translateY(-1px);
}

/* Animations */
.slide-up-enter-active,
.slide-up-leave-active {
  transition: all 0.4s cubic-bezier(0.34, 1.56, 0.64, 1);
}

.slide-up-enter-from {
  opacity: 0;
  transform: translateY(30px) scale(0.95);
}

.slide-up-leave-to {
  opacity: 0;
  transform: translateY(20px) scale(0.98);
}

/* Responsive Design */

/* لپ‌تاپ متوسط */
@media (max-width: 1200px) {
  .glass-device-card {
    width: 380px;
  }
}

/* تبلت و لپ‌تاپ کوچک */
@media (max-width: 992px) {
  .glass-device-card {
    width: 350px;
    right: 15px;
    bottom: 15px;
  }
  
  .stats-grid {
    grid-template-columns: repeat(2, 1fr);
    gap: 10px;
  }
  
  .stat-card {
    padding: 10px;
  }
  
  .stat-value {
    font-size: 13px;
  }
}

/* تبلت کوچک */
@media (max-width: 768px) {
  .glass-device-card {
    width: 320px;
    right: 10px;
    bottom: 10px;
  }
  
  .glass-header {
    padding: 14px 16px;
  }
  
  .glass-content {
    padding: 16px;
  }
  
  .device-name {
    font-size: 16px;
  }
  
  .status-indicator {
    width: 36px;
    height: 36px;
  }
  
  .stats-grid {
    grid-template-columns: repeat(2, 1fr);
    gap: 8px;
  }
  
  .stat-icon {
    width: 28px;
    height: 28px;
  }
  
  .stat-label {
    font-size: 10px;
  }
  
  .stat-value {
    font-size: 12px;
  }
}

/* موبایل بزرگ */
@media (max-width: 576px) {
  .glass-device-card {
    position: fixed;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    width: 90%;
    max-width: 400px;
    max-height: 85vh;
    bottom: auto;
    right: auto;
    border-radius: 16px;
  }
  
  .glass-header {
    padding: 12px 14px;
  }
  
  .header-content {
    flex-wrap: wrap;
  }
  
  .device-main-info {
    flex: 1;
    min-width: 200px;
  }
  
  .device-name {
    font-size: 15px;
  }
  
  .device-id {
    font-size: 11px;
  }
  
  .glass-content {
    padding: 14px;
    max-height: calc(85vh - 60px);
  }
  
  .stats-grid {
    grid-template-columns: repeat(2, 1fr);
  }
  
  .section-title {
    font-size: 13px;
  }
  
  .action-buttons {
    grid-template-columns: 1fr;
  }
  
  .glass-action-btn {
    font-size: 12px;
    height: 38px;
  }
}

/* موبایل کوچک */
@media (max-width: 480px) {
  .glass-device-card {
    width: 95%;
    max-height: 90vh;
    border-radius: 14px;
  }
  
  .glass-header {
    padding: 10px 12px;
  }
  
  .status-indicator {
    width: 32px;
    height: 32px;
  }
  
  .glass-content {
    padding: 12px;
    max-height: calc(90vh - 52px);
  }
  
  .stats-grid {
    grid-template-columns: repeat(2, 1fr);
    gap: 6px;
  }
  
  .stat-card {
    padding: 8px;
  }
  
  .stat-icon {
    width: 24px;
    height: 24px;
  }
  
  .stat-info {
    min-width: 0;
  }
  
  .stat-label {
    font-size: 9px;
  }
  
  .stat-value {
    font-size: 11px;
  }
  
  .coordinates {
    grid-template-columns: 1fr;
    gap: 8px;
  }
  
  .coord-label {
    font-size: 10px;
  }
  
  .coord-value {
    font-size: 12px;
  }
  
  .rope-status,
  .alarm-status {
    font-size: 12px;
  }
  
  .glass-action-btn {
    font-size: 11px;
    height: 36px;
  }
}

/* موبایل خیلی کوچک */
@media (max-width: 360px) {
  .glass-device-card {
    width: 98%;
    border-radius: 12px;
  }
  
  .device-main-info {
    min-width: 150px;
  }
  
  .device-name {
    font-size: 14px;
  }
  
  .device-id {
    font-size: 10px;
  }
  
  .stats-grid {
    grid-template-columns: 1fr;
  }
  
  .stat-card {
    padding: 10px;
  }
  
  .stat-icon {
    width: 28px;
    height: 28px;
  }
  
  .stat-label {
    font-size: 10px;
  }
  
  .stat-value {
    font-size: 12px;
  }
  
  .action-buttons {
    grid-template-columns: 1fr;
  }
}

/* جهت landscape */
@media (max-height: 600px) and (orientation: landscape) {
  .glass-device-card {
    max-height: 85vh;
  }
  
  .glass-content {
    max-height: calc(85vh - 60px);
    overflow-y: auto;
  }
  
  .stats-grid {
    grid-template-columns: repeat(3, 1fr);
  }
}
</style>