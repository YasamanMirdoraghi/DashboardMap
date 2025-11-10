<template>
  <!-- کانتینر اصلی با قاب و پدینگ -->
  <div class="main-frame">
    <div class="dashboard-container">
      <!-- نقشه -->
      <div ref="mapContainer" class="map-container"></div>

      <!-- Sidebar با آیکون‌ها -->
      <div class="sidebar-container">
        <!-- نوار آیکون‌ها -->
        <q-card class="icon-sidebar" elevated>
          <div class="sidebar-header">
            <q-btn
              round
              dense
              flat
              icon="menu"
              class="collapse-btn"
              @click="toggleSidebar"
            />
          </div>

          <div class="icon-items">
            <q-btn
              v-for="item in menuItems"
              :key="item.id"
              round
              flat
              :icon="item.icon"
              :class="{ 'active': activePanel === item.id }"
              class="icon-item"
              @click="togglePanel(item.id)"
            >
              <q-badge
                v-if="item.badge"
                floating
                color="negative"
                class="badge"
              >
                {{ item.badge }}
              </q-badge>

              <q-tooltip anchor="center right" self="center left">
                {{ item.title }}
              </q-tooltip>
            </q-btn>
          </div>
        </q-card>

        <!-- پنل‌های کشویی -->
        <transition name="slide-panel">
          <q-card v-if="activePanel" class="sliding-panel" elevated>
            <!-- پنل Overview -->
            <div v-if="activePanel === 'overview'" class="panel-content">
              <div class="panel-header">
                <div class="header-title">
                  <q-icon name="dashboard" size="24px" />
                  <h3>Overview</h3>
                </div>
                <q-btn round flat dense icon="close" @click="closePanel" />
              </div>

              <div class="content-section">
                <h4 class="section-title">System Status</h4>
                <div class="status-grid">
                  <q-card class="status-card online">
                    <div class="card-content">
                      <q-icon name="check_circle" color="positive" />
                      <span class="count">5</span>
                      <p class="label">Active Devices</p>
                    </div>
                  </q-card>

                  <q-card class="status-card warning">
                    <div class="card-content">
                      <q-icon name="warning" color="warning" />
                      <span class="count">1</span>
                      <p class="label">Warnings</p>
                    </div>
                  </q-card>
                </div>
              </div>

              <div class="content-section">
                <h4 class="section-title">Recent Readings</h4>
                <div class="readings-list">
                  <q-card v-for="reading in recentReadings" :key="reading.id" class="reading-card">
                    <div class="reading-content">
                      <div class="reading-icon">
                        <q-icon :name="reading.icon" :color="reading.color" />
                        <span class="reading-label">{{ reading.label }}</span>
                      </div>
                      <span class="reading-value">{{ reading.value }}</span>
                    </div>
                  </q-card>
                </div>
              </div>
            </div>

            <!-- پنل Devices -->
            <div v-if="activePanel === 'devices'" class="panel-content">
              <div class="panel-header">
                <div class="header-title">
                  <q-icon name="devices" size="24px" />
                  <h3>Devices</h3>
                </div>
                <q-btn round flat dense icon="close" @click="closePanel" />
              </div>

              <div class="search-section">
                <q-input
                  v-model="deviceSearch"
                  placeholder="Search devices..."
                  dense
                  outlined
                  class="search-input"
                >
                  <template v-slot:append>
                    <q-icon name="search" />
                  </template>
                </q-input>
              </div>

              <div class="filter-tabs">
                <q-btn
                  v-for="tab in deviceTabs"
                  :key="tab.id"
                  :label="tab.name"
                  :class="{ 'active': activeDeviceTab === tab.id }"
                  flat
                  dense
                  @click="activeDeviceTab = tab.id"
                >
                  <q-badge color="primary" floating>{{ tab.count }}</q-badge>
                </q-btn>
              </div>

              <div class="devices-list">
                <q-card
                  v-for="device in filteredDevices"
                  :key="device.id"
                  :class="['device-card', device.status]"
                  @click="selectDevice(device)"
                >
                  <div class="device-content">
                    <div class="device-info">
                      <div class="device-header">
                        <q-icon name="sensors" color="primary" />
                        <span class="device-name">{{ device.name }}</span>
                        <q-badge :color="getStatusColor(device.status)">
                          {{ device.status }}
                        </q-badge>
                      </div>
                      <p class="device-location">{{ device.location }}</p>
                      <div class="device-value">
                        Current: <strong>{{ device.value }} {{ device.unit }}</strong>
                      </div>
                    </div>
                    <q-btn round flat icon="analytics" @click.stop="showDeviceDetails(device)" />
                  </div>
                </q-card>
              </div>
            </div>

            <!-- پنل Alerts -->
            <div v-if="activePanel === 'alerts'" class="panel-content">
              <div class="panel-header">
                <div class="header-title">
                  <q-icon name="notifications" size="24px" />
                  <h3>Alerts</h3>
                </div>
                <q-btn round flat dense icon="close" @click="closePanel" />
              </div>

              <div class="alerts-list">
                <q-card
                  v-for="alert in alerts"
                  :key="alert.id"
                  :class="['alert-card', alert.priority]"
                >
                  <div class="alert-content">
                    <div class="alert-icon">
                      <q-icon :name="getAlertIcon(alert.priority)" :color="getAlertColor(alert.priority)" />
                    </div>
                    <div class="alert-info">
                      <h4>{{ alert.title }}</h4>
                      <p>{{ alert.message }}</p>
                      <span class="alert-time">{{ alert.time }}</span>
                    </div>
                    <q-btn round flat icon="check" @click="resolveAlert(alert.id)" />
                  </div>
                </q-card>
              </div>
            </div>

            <!-- پنل Analytics -->
            <div v-if="activePanel === 'analytics'" class="panel-content">
              <div class="panel-header">
                <div class="header-title">
                  <q-icon name="analytics" size="24px" />
                  <h3>Analytics</h3>
                </div>
                <q-btn round flat dense icon="close" @click="closePanel" />
              </div>

              <div class="analytics-content">
                <h4 class="section-title">Performance Metrics</h4>

                <q-card class="metric-card">
                  <div class="metric-item">
                    <div class="metric-header">
                      <span>Network Uptime</span>
                      <strong class="metric-value">99.8%</strong>
                    </div>
                    <q-linear-progress :value="0.998" color="positive" class="q-mt-sm" />
                  </div>

                  <div class="metric-item">
                    <div class="metric-header">
                      <span>Data Accuracy</span>
                      <strong class="metric-value">97.5%</strong>
                    </div>
                    <q-linear-progress :value="0.975" color="primary" class="q-mt-sm" />
                  </div>

                  <div class="metric-item">
                    <div class="metric-header">
                      <span>Response Time</span>
                      <strong class="metric-value">85ms</strong>
                    </div>
                    <q-linear-progress :value="0.75" color="accent" class="q-mt-sm" />
                  </div>
                </q-card>

                <div class="stats-grid">
                  <q-card class="stat-card">
                    <div class="stat-content">
                      <span class="stat-number">2.4k</span>
                      <p class="stat-label">Data Points/hr</p>
                    </div>
                  </q-card>

                  <q-card class="stat-card">
                    <div class="stat-content">
                      <span class="stat-number">156</span>
                      <p class="stat-label">Events Today</p>
                    </div>
                  </q-card>
                </div>
              </div>
            </div>

            <!-- پنل Settings -->
            <div v-if="activePanel === 'settings'" class="panel-content">
              <div class="panel-header">
                <div class="header-title">
                  <q-icon name="settings" size="24px" />
                  <h3>Settings</h3>
                </div>
                <q-btn round flat dense icon="close" @click="closePanel" />
              </div>

              <div class="settings-content">
                <h4 class="section-title">Dashboard Settings</h4>

                <q-card class="settings-card">
                  <div class="setting-item">
                    <div class="setting-info">
                      <span class="setting-label">Auto-refresh</span>
                      <span class="setting-description">Automatically update data every 30 seconds</span>
                    </div>
                    <q-toggle v-model="settings.autoRefresh" color="primary" />
                  </div>

                  <div class="setting-item">
                    <div class="setting-info">
                      <span class="setting-label">Notifications</span>
                      <span class="setting-description">Receive browser notifications</span>
                    </div>
                    <q-toggle v-model="settings.notifications" color="primary" />
                  </div>

                  <div class="setting-item">
                    <div class="setting-info">
                      <span class="setting-label">Dark Mode</span>
                      <span class="setting-description">Switch to dark theme</span>
                    </div>
                    <q-toggle v-model="settings.darkMode" color="primary" />
                  </div>
                </q-card>

                <div class="settings-actions">
                  <q-btn
                    outline
                    icon="map"
                    label="Map Settings"
                    class="full-width q-mb-sm"
                  />
                  <q-btn
                    outline
                    icon="notifications"
                    label="Alert Preferences"
                    class="full-width"
                  />
                </div>
              </div>
            </div>
          </q-card>
        </transition>
      </div>

      <!-- Floating Device Info -->
      <transition name="fade-slide">
        <q-card v-if="selectedDevice" class="floating-device-card" elevated>
          <div class="device-card-header">
            <div class="device-title">
              <h4>{{ selectedDevice.name }}</h4>
              <p>Device ID: {{ selectedDevice.id }}</p>
            </div>
            <q-btn round flat dense icon="close" @click="selectedDevice = null" />
          </div>

          <div class="device-reading">
            <span class="reading-label">Current Reading</span>
            <span class="reading-value">{{ selectedDevice.value }} {{ selectedDevice.unit }}</span>
          </div>

          <div class="device-actions">
            <q-btn outline label="View Details" class="action-btn" />
            <q-btn label="Configure" color="primary" class="action-btn" />
          </div>
        </q-card>
      </transition>

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
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, onUnmounted } from 'vue'
import L from 'leaflet'
import 'leaflet/dist/leaflet.css'

// Fix Leaflet default marker icon issue
delete L.Icon.Default.prototype._getIconUrl
L.Icon.Default.mergeOptions({
  iconRetinaUrl: 'https://unpkg.com/leaflet@1.7.1/dist/images/marker-icon-2x.png',
  iconUrl: 'https://unpkg.com/leaflet@1.7.1/dist/images/marker-icon.png',
  shadowUrl: 'https://unpkg.com/leaflet@1.7.1/dist/images/marker-shadow.png',
})

// Reactive state
const mapContainer = ref(null)
const activePanel = ref(null)
const activeDeviceTab = ref('all')
const deviceSearch = ref('')
const selectedDevice = ref(null)
const currentTime = ref('')
let map = null

// Settings
const settings = ref({
  autoRefresh: true,
  notifications: true,
  darkMode: false
})

// Data
const menuItems = ref([
  { id: 'overview', icon: 'dashboard', title: 'Overview' },
  { id: 'devices', icon: 'devices', title: 'Devices', badge: 12 },
  { id: 'alerts', icon: 'notifications', title: 'Alerts', badge: 3 },
  { id: 'analytics', icon: 'analytics', title: 'Analytics' },
  { id: 'settings', icon: 'settings', title: 'Settings' }
])

const deviceTabs = ref([
  { id: 'all', name: 'All', count: 12 },
  { id: 'online', name: 'Online', count: 8 },
  { id: 'offline', name: 'Offline', count: 2 },
  { id: 'warning', name: 'Warning', count: 2 }
])

const iotDevices = ref([
  { id: 1, name: 'Temperature Sensor A1', type: 'temperature', lat: 40.7128, lng: -74.0060, value: 22.5, unit: '°C', status: 'online', location: 'Main Building' },
  { id: 2, name: 'Humidity Sensor B2', type: 'humidity', lat: 40.7580, lng: -73.9855, value: 65, unit: '%', status: 'online', location: 'Lab Room' },
  { id: 3, name: 'Air Quality Monitor C3', type: 'air', lat: 40.7489, lng: -73.9680, value: 45, unit: 'AQI', status: 'warning', location: 'Entrance' },
  { id: 4, name: 'Power Meter D4', type: 'power', lat: 40.7282, lng: -73.7949, value: 1250, unit: 'W', status: 'online', location: 'Server Room' },
  { id: 5, name: 'Temperature Sensor E5', type: 'temperature', lat: 40.7614, lng: -73.9776, value: 24.1, unit: '°C', status: 'online', location: 'Office' },
  { id: 6, name: 'Wind Speed Monitor F6', type: 'wind', lat: 40.7589, lng: -73.9851, value: 12.3, unit: 'km/h', status: 'offline', location: 'Roof' }
])

const alerts = ref([
  { id: 1, title: 'Air Quality Alert', message: 'AQI above threshold', priority: 'warning', time: '5 min ago' },
  { id: 2, title: 'Connection Lost', message: 'Wind sensor offline', priority: 'error', time: '12 min ago' },
  { id: 3, title: 'Calibration Due', message: 'Temperature sensor needs calibration', priority: 'info', time: '1 hour ago' }
])

const recentReadings = ref([
  { id: 1, label: 'Temperature', value: '23.3°C', icon: 'thermostat', color: 'negative' },
  { id: 2, label: 'Humidity', value: '65%', icon: 'water_drop', color: 'primary' },
  { id: 3, label: 'Power', value: '1.25 kW', icon: 'bolt', color: 'warning' }
])

// Computed properties
const filteredDevices = computed(() => {
  let filtered = iotDevices.value

  if (activeDeviceTab.value !== 'all') {
    filtered = filtered.filter(device => device.status === activeDeviceTab.value)
  }

  if (deviceSearch.value) {
    const searchTerm = deviceSearch.value.toLowerCase()
    filtered = filtered.filter(device =>
      device.name.toLowerCase().includes(searchTerm) ||
      device.location.toLowerCase().includes(searchTerm)
    )
  }

  return filtered
})

// Methods
const toggleSidebar = () => {
  // Implement sidebar toggle if needed
}

const togglePanel = (panelId) => {
  if (activePanel.value === panelId) {
    activePanel.value = null
  } else {
    activePanel.value = panelId
  }
}

const closePanel = () => {
  activePanel.value = null
}

const selectDevice = (device) => {
  selectedDevice.value = device
  if (map && device.lat && device.lng) {
    map.panTo([device.lat, device.lng])
  }
}

const showDeviceDetails = (device) => {
  console.log('Device details:', device)
  // Implement device details modal
}

const resolveAlert = (alertId) => {
  alerts.value = alerts.value.filter(alert => alert.id !== alertId)
}

const getStatusColor = (status) => {
  const colors = {
    online: 'positive',
    offline: 'negative',
    warning: 'warning'
  }
  return colors[status] || 'grey'
}

const getAlertIcon = (priority) => {
  const icons = {
    error: 'error',
    warning: 'warning',
    info: 'info'
  }
  return icons[priority] || 'notifications'
}

const getAlertColor = (priority) => {
  const colors = {
    error: 'negative',
    warning: 'warning',
    info: 'info'
  }
  return colors[priority] || 'grey'
}

const createCustomIcon = (color) => {
  return L.divIcon({
    className: 'custom-marker',
    html: `<div style="background-color: ${color}; width: 24px; height: 24px; border-radius: 50%; border: 3px solid white; box-shadow: 0 2px 8px rgba(0,0,0,0.3);"></div>`,
    iconSize: [24, 24],
    iconAnchor: [12, 12],
  })
}

// Lifecycle
onMounted(() => {
  // Initialize map
  map = L.map(mapContainer.value).setView([40.7489, -73.9680], 12)

  // استفاده از نقشه روشن (سبک Light)
  L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
    attribution: '© OpenStreetMap contributors',
    maxZoom: 20
  }).addTo(map)

  // Add markers
  iotDevices.value.forEach(device => {
    const color = device.status === 'online' ? '#10b981' :
                 device.status === 'warning' ? '#f59e0b' : '#ef4444'

    const marker = L.marker([device.lat, device.lng], {
      icon: createCustomIcon(color)
    }).addTo(map)

    marker.bindPopup(`
      <div class="q-pa-sm">
        <h6 class="q-my-none text-weight-bold">${device.name}</h6>
        <div class="q-mt-sm">
          <div class="row items-center justify-between">
            <span class="text-grey">Status:</span>
            <q-badge color="${getStatusColor(device.status)}">${device.status}</q-badge>
          </div>
          <div class="row items-center justify-between q-mt-xs">
            <span class="text-grey">Value:</span>
            <span class="text-weight-bold">${device.value} ${device.unit}</span>
          </div>
        </div>
      </div>
    `)
  })

  // Update time
  currentTime.value = new Date().toLocaleTimeString()
  const timeInterval = setInterval(() => {
    currentTime.value = new Date().toLocaleTimeString()
  }, 1000)

  onUnmounted(() => {
    clearInterval(timeInterval)
    if (map) {
      map.remove()
    }
  })
})
</script>

<style scoped>
/* قاب اصلی با پدینگ */
.main-frame {
  width: 100%;
  height: 100vh;
  padding: 20px;
  background: linear-gradient(135deg, #063950 0%, #0a0a0a 100%);
  display: flex;
  align-items: center;
  justify-content: center;
}

.dashboard-container {
  width: 100%;
  height: 100%;
  position: relative;
  background: white;
  border-radius: 20px;
  box-shadow:
    0 20px 40px rgba(0, 0, 0, 0.1),
    0 0 0 1px rgba(255, 255, 255, 0.2),
    inset 0 1px 0 rgba(255, 255, 255, 0.4);
  overflow: hidden;
}

.map-container {
  width: 100%;
  height: 100%;
  background: #f8f9fa;
}

/* Sidebar Styles */
.sidebar-container {
  position: absolute;
  top: 0;
  left: 0;
  height: 100%;
  z-index: 1000;
  display: flex;
}

.icon-sidebar {
  width: 70px;
  height: 100%;
  background: linear-gradient(135deg, #063950 0%, #0a0a0a 100%);
  backdrop-filter: blur(20px);
  border-radius: 0;
  border-right: 1px solid rgba(255, 255, 255, 0.1);
  display: flex;
  flex-direction: column;
  padding: 16px 8px;
  box-shadow: 2px 0 10px rgba(0, 0, 0, 0.1);
}

.sidebar-header {
  display: flex;
  justify-content: center;
  margin-bottom: 24px;
}

.collapse-btn {
  color: #fff;
}

.icon-items {
  display: flex;
  flex-direction: column;
  gap: 8px;
  flex: 1;
}

.icon-item {
  width: 48px;
  height: 48px;
  color: #9e9e9e;
  position: relative;
  transition: all 0.3s ease;
}

.icon-item:hover {
  color: #fff;
  background: rgba(255, 255, 255, 0.1);
}

.icon-item.active {
  color: #fff;
  background: linear-gradient(135deg, #2196f3, #21cbf3);
}

.badge {
  position: absolute;
  top: 4px;
  right: 4px;
}

/* Sliding Panel */
.sliding-panel {
  width: 380px;
  height: 100%;
  background: rgba(30, 30, 30, 0.95);
  backdrop-filter: blur(20px);
  border-radius: 0;
  border-right: 1px solid rgba(255, 255, 255, 0.1);
  box-shadow: 2px 0 15px rgba(0, 0, 0, 0.2);
}

.slide-panel-enter-active,
.slide-panel-leave-active {
  transition: all 0.3s ease;
}

.slide-panel-enter-from,
.slide-panel-leave-to {
  transform: translateX(-100%);
  opacity: 0;
}

.panel-content {
  height: 100%;
  display: flex;
  flex-direction: column;
}

.panel-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 20px 24px;
  background: linear-gradient(135deg, #2196f3, #21cbf3);
  color: white;
}

.header-title {
  display: flex;
  align-items: center;
  gap: 12px;
}

.header-title h3 {
  margin: 0;
  font-size: 20px;
  font-weight: 600;
}

/* Content Sections */
.content-section {
  padding: 20px 24px;
  flex: 1;
  overflow-y: auto;
}

.section-title {
  color: #9e9e9e;
  font-size: 14px;
  font-weight: 600;
  margin: 0 0 16px 0;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

/* Status Grid */
.status-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 12px;
  margin-bottom: 24px;
}

.status-card {
  padding: 16px;
  background: rgba(255, 255, 255, 0.05);
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: 8px;
}

.status-card.online {
  border-left: 4px solid #4caf50;
}

.status-card.warning {
  border-left: 4px solid #ff9800;
}

.card-content {
  text-align: center;
}

.count {
  display: block;
  font-size: 24px;
  font-weight: bold;
  margin: 8px 0;
  color: #fff;
}

.label {
  margin: 0;
  color: #9e9e9e;
  font-size: 12px;
}

/* Readings List */
.readings-list {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.reading-card {
  background: rgba(255, 255, 255, 0.05);
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: 8px;
}

.reading-content {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 12px 16px;
}

.reading-icon {
  display: flex;
  align-items: center;
  gap: 8px;
}

.reading-label {
  color: #fff;
  font-size: 14px;
}

.reading-value {
  color: #fff;
  font-weight: bold;
  font-size: 14px;
}

/* Search Section */
.search-section {
  padding: 20px 24px 0;
}

.search-input {
  background: rgba(255, 255, 255, 0.05);
}

:deep(.search-input .q-field__control) {
  color: #fff;
  border-radius: 8px;
}

:deep(.search-input .q-field__control:before) {
  border-color: rgba(255, 255, 255, 0.2);
}

/* Filter Tabs */
.filter-tabs {
  display: flex;
  gap: 4px;
  padding: 16px 24px;
  background: rgba(255, 255, 255, 0.02);
}

.filter-tabs .q-btn {
  flex: 1;
  color: #9e9e9e;
  position: relative;
  border-radius: 6px;
}

.filter-tabs .q-btn.active {
  color: #fff;
  background: rgba(255, 255, 255, 0.1);
}

/* Devices List */
.devices-list {
  flex: 1;
  overflow-y: auto;
  padding: 0 24px 24px;
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.device-card {
  background: rgba(255, 255, 255, 0.05);
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: 8px;
  cursor: pointer;
  transition: all 0.3s ease;
}

.device-card:hover {
  border-color: #2196f3;
  transform: translateY(-1px);
  box-shadow: 0 4px 12px rgba(33, 150, 243, 0.2);
}

.device-card.online {
  border-left: 4px solid #4caf50;
}

.device-card.warning {
  border-left: 4px solid #ff9800;
}

.device-card.offline {
  border-left: 4px solid #f44336;
}

.device-content {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 16px;
}

.device-info {
  flex: 1;
}

.device-header {
  display: flex;
  align-items: center;
  gap: 8px;
  margin-bottom: 4px;
}

.device-name {
  color: #fff;
  font-weight: 500;
  flex: 1;
}

.device-location {
  color: #9e9e9e;
  font-size: 12px;
  margin: 0 0 4px 0;
}

.device-value {
  color: #9e9e9e;
  font-size: 12px;
}

/* Alerts List */
.alerts-list {
  flex: 1;
  overflow-y: auto;
  padding: 24px;
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.alert-card {
  background: rgba(255, 255, 255, 0.05);
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: 8px;
}

.alert-card.warning {
  border-left: 4px solid #ff9800;
}

.alert-card.error {
  border-left: 4px solid #f44336;
}

.alert-card.info {
  border-left: 4px solid #2196f3;
}

.alert-content {
  display: flex;
  align-items: flex-start;
  gap: 12px;
  padding: 16px;
}

.alert-info {
  flex: 1;
}

.alert-info h4 {
  color: #fff;
  margin: 0 0 4px 0;
  font-size: 14px;
}

.alert-info p {
  color: #9e9e9e;
  margin: 0 0 4px 0;
  font-size: 12px;
}

.alert-time {
  color: #9e9e9e;
  font-size: 10px;
}

/* Analytics */
.analytics-content {
  padding: 24px;
  flex: 1;
  overflow-y: auto;
}

.metric-card {
  background: rgba(255, 255, 255, 0.05);
  border: 1px solid rgba(255, 255, 255, 0.1);
  padding: 20px;
  margin-bottom: 20px;
  border-radius: 8px;
}

.metric-item {
  margin-bottom: 16px;
}

.metric-item:last-child {
  margin-bottom: 0;
}

.metric-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  color: #fff;
  font-size: 14px;
}

.metric-value {
  color: #2196f3;
}

.stats-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 12px;
}

.stat-card {
  background: rgba(255, 255, 255, 0.05);
  border: 1px solid rgba(255, 255, 255, 0.1);
  padding: 20px;
  text-align: center;
  border-radius: 8px;
}

.stat-content .stat-number {
  display: block;
  font-size: 24px;
  font-weight: bold;
  color: #fff;
  margin-bottom: 4px;
}

.stat-content .stat-label {
  margin: 0;
  color: #9e9e9e;
  font-size: 12px;
}

/* Settings */
.settings-content {
  padding: 24px;
  flex: 1;
  overflow-y: auto;
}

.settings-card {
  background: rgba(255, 255, 255, 0.05);
  border: 1px solid rgba(255, 255, 255, 0.1);
  padding: 20px;
  margin-bottom: 20px;
  border-radius: 8px;
}

.setting-item {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 12px 0;
  border-bottom: 1px solid rgba(255, 255, 255, 0.1);
}

.setting-item:last-child {
  border-bottom: none;
}

.setting-info {
  flex: 1;
}

.setting-label {
  display: block;
  color: #fff;
  font-size: 14px;
  margin-bottom: 2px;
}

.setting-description {
  display: block;
  color: #9e9e9e;
  font-size: 12px;
}

.settings-actions {
  padding-top: 16px;
}

/* Floating Device Card */
.floating-device-card {
  position: absolute;
  bottom: 24px;
  right: 24px;
  width: 320px;
  background: rgba(30, 30, 30, 0.95);
  backdrop-filter: blur(20px);
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: 12px;
  z-index: 1000;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3);
}

.fade-slide-enter-active,
.fade-slide-leave-active {
  transition: all 0.3s ease;
}

.fade-slide-enter-from,
.fade-slide-leave-to {
  transform: translateY(20px);
  opacity: 0;
}

.device-card-header {
  display: flex;
  align-items: flex-start;
  justify-content: space-between;
  padding: 20px 20px 0;
}

.device-title h4 {
  margin: 0 0 4px 0;
  color: #fff;
  font-size: 16px;
}

.device-title p {
  margin: 0;
  color: #9e9e9e;
  font-size: 12px;
}

.device-reading {
  padding: 20px;
  background: linear-gradient(135deg, rgba(33, 150, 243, 0.2), rgba(33, 203, 243, 0.2));
  margin: 16px 20px;
  border-radius: 8px;
  text-align: center;
}

.reading-label {
  display: block;
  color: #fff;
  font-size: 14px;
  margin-bottom: 4px;
}

.reading-value {
  display: block;
  color: #fff;
  font-size: 24px;
  font-weight: bold;
}

.device-actions {
  display: flex;
  gap: 8px;
  padding: 0 20px 20px;
}

.action-btn {
  flex: 1;
  border-radius: 6px;
}

/* Status Bar */
.status-bar {
  position: absolute;
  top: 24px;
  right: 24px;
  background: rgba(30, 30, 30, 0.95);
  backdrop-filter: blur(20px);
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: 8px;
  z-index: 1000;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.2);
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
  color: #fff;
  font-size: 14px;
}

.status-time {
  color: #9e9e9e;
  font-size: 14px;
}

/* Scrollbar Styling */
:deep(.scroll)::-webkit-scrollbar {
  width: 6px;
}

:deep(.scroll)::-webkit-scrollbar-track {
  background: rgba(255, 255, 255, 0.1);
  border-radius: 3px;
}

:deep(.scroll)::-webkit-scrollbar-thumb {
  background: rgba(255, 255, 255, 0.3);
  border-radius: 3px;
}

:deep(.scroll)::-webkit-scrollbar-thumb:hover {
  background: rgba(255, 255, 255, 0.5);
}

/* استایل برای نقشه روشن */
:deep(.leaflet-container) {
  background: #f8f9fa;
  font-family: inherit;
}

:deep(.leaflet-popup-content-wrapper) {
  background: rgba(30, 30, 30, 0.95);
  backdrop-filter: blur(20px);
  color: white;
  border-radius: 8px;
  border: 1px solid rgba(255, 255, 255, 0.1);
}

:deep(.leaflet-popup-tip) {
  background: rgba(30, 30, 30, 0.95);
  border: 1px solid rgba(255, 255, 255, 0.1);
}
</style>
