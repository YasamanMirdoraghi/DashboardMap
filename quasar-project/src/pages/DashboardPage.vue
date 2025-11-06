<template>
  <q-page class="row full-height">
    <!-- Side Menu -->
    <div class="col-3 q-pa-md dashboard-section">
      <!-- Header -->
      <div class="text-h4 q-mb-md text-primary">Tracking System</div>

      <!-- Main Menu - shown when not in device list -->
      <div v-if="currentView === 'mainMenu'" class="main-menu">
        <!-- Menu Cards -->
        <q-card class="q-mb-md cursor-pointer" @click="showDeviceList">
          <q-card-section class="row items-center">
            <q-icon name="sensors" size="32px" color="primary" class="q-mr-md" />
            <div>
              <div class="text-h6">Device List</div>
              <div class="text-caption">View and manage all tracking devices</div>
            </div>
          </q-card-section>
        </q-card>

        <q-card class="q-mb-md">
          <q-card-section class="row items-center">
            <q-icon name="analytics" size="32px" color="orange" class="q-mr-md" />
            <div>
              <div class="text-h6">Reports</div>
              <div class="text-caption">View tracking reports and analytics</div>
            </div>
          </q-card-section>
        </q-card>

        <q-card class="q-mb-md">
          <q-card-section class="row items-center">
            <q-icon name="notifications" size="32px" color="red" class="q-mr-md" />
            <div>
              <div class="text-h6">Alerts</div>
              <div class="text-caption">System alerts and notifications</div>
            </div>
          </q-card-section>
        </q-card>

        <q-card class="q-mb-md">
          <q-card-section class="row items-center">
            <q-icon name="settings" size="32px" color="grey" class="q-mr-md" />
            <div>
              <div class="text-h6">Settings</div>
              <div class="text-caption">System configuration</div>
            </div>
          </q-card-section>
        </q-card>
      </div>

      <!-- Device List View - shown when in device list -->
      <div v-if="currentView === 'deviceList'" class="device-list-view">
        <!-- Back Button and Header -->
        <div class="row items-center q-mb-md">
          <q-btn
            icon="arrow_back"
            flat
            round
            @click="showMainMenu"
            class="q-mr-sm"
          />
          <div class="text-h6">Device List</div>
        </div>

        <!-- Vehicle List -->
        <q-list bordered separator>
          <q-item
            v-for="vehicle in vehicles"
            :key="vehicle.id"
            clickable
            v-ripple
            @click="selectVehicle(vehicle)"
            :class="{'bg-blue-1': selectedVehicle?.id === vehicle.id}"
          >
            <q-item-section avatar>
              <div class="vehicle-avatar" :class="getStatusClass(vehicle.status)">
                <q-icon name="location_on" size="20px" color="white" />
              </div>
            </q-item-section>
            <q-item-section>
              <q-item-label>{{ vehicle.name }}</q-item-label>
              <q-item-label caption>Plate: {{ vehicle.plate }}</q-item-label>
              <q-item-label caption>
                <q-icon name="speed" size="16px" class="q-mr-xs" />
                Speed: {{ vehicle.speed }} km/h
              </q-item-label>
            </q-item-section>
            <q-item-section side>
              <q-badge :color="getStatusColor(vehicle.status)">
                {{ getStatusText(vehicle.status) }}
              </q-badge>
            </q-item-section>
          </q-item>
        </q-list>

        <!-- Quick Actions -->
        <div class="q-mt-lg">
          <div class="text-subtitle1 q-mb-sm">Quick Actions</div>
          <div class="row q-gutter-sm">
            <q-btn
              color="primary"
              icon="refresh"
              label="Refresh"
              outline
              @click="refreshLocations"
            />
            <q-btn
              color="green"
              icon="add"
              label="Add Device"
              outline
            />
          </div>
        </div>
      </div>
    </div>

    <!-- Map -->
    <div class="col-9 relative-position map-section">
      <div class="map-background absolute-full bg-center bg-cover"></div>

      <!-- Vehicle Icons on Map -->
      <div
        v-for="vehicle in vehicles"
        :key="vehicle.id"
        class="absolute vehicle-marker"
        :style="{
          left: vehicle.position.x + '%',
          top: vehicle.position.y + '%'
        }"
        @click="selectVehicle(vehicle)"
      >
        <!-- Circle behind icon -->
        <div class="location-pin">
          <!-- Pulse animation -->
          <div class="pulse" :class="getStatusClass(vehicle.status)"></div>
          <!-- White circle -->
          <div class="circle" :class="{'selected': selectedVehicle?.id === vehicle.id}">
            <q-icon
              name="location_on"
              size="20px"
              :color="selectedVehicle?.id === vehicle.id ? 'blue' : getStatusColor(vehicle.status)"
              class="cursor-pointer"
            />
          </div>
        </div>

        <div
          v-if="selectedVehicle?.id === vehicle.id"
          class="vehicle-tooltip q-pa-xs bg-white rounded-borders shadow-2"
        >
          <div class="text-weight-bold">{{ vehicle.name }}</div>
          <div class="text-caption">{{ vehicle.plate }}</div>
          <div class="text-caption">
            <q-icon name="speed" size="14px" class="q-mr-xs" />
            Speed: {{ vehicle.speed }} km/h
          </div>
          <div class="text-caption" :class="`text-${getStatusColor(vehicle.status)}`">
            {{ getStatusText(vehicle.status) }}
          </div>
        </div>
      </div>

      <!-- Map Controls -->
      <div class="absolute-top-right q-pa-md">
        <q-btn-group rounded>
          <q-btn
            icon="zoom_in"
            color="white"
            text-color="black"
            @click="zoomIn"
          />
          <q-btn
            icon="zoom_out"
            color="white"
            text-color="black"
            @click="zoomOut"
          />
          <q-btn
            icon="my_location"
            color="primary"
            @click="centerMap"
          />
          <q-btn
            icon="gps_fixed"
            color="white"
            text-color="black"
            @click="refreshLocations"
          />
        </q-btn-group>
      </div>
    </div>
  </q-page>
</template>

<script setup>
import { ref } from 'vue'

const currentView = ref('mainMenu') // 'mainMenu' or 'deviceList'
const selectedVehicle = ref(null)

// Sample vehicle data
const vehicles = ref([
  {
    id: 1,
    name: 'Vehicle 1',
    plate: 'ABC123',
    status: 'moving',
    position: { x: 25, y: 40 },
    speed: 60,
    lastUpdate: '2 minutes ago'
  },
  {
    id: 2,
    name: 'Vehicle 2',
    plate: 'DEF456',
    status: 'idle',
    position: { x: 60, y: 30 },
    speed: 0,
    lastUpdate: '5 minutes ago'
  },
  {
    id: 3,
    name: 'Vehicle 3',
    plate: 'GHI789',
    status: 'offline',
    position: { x: 45, y: 65 },
    speed: 0,
    lastUpdate: '1 hour ago'
  }
])

// View management functions
const showMainMenu = () => {
  currentView.value = 'mainMenu'
  selectedVehicle.value = null
}

const showDeviceList = () => {
  currentView.value = 'deviceList'
}

// Helper functions
const getStatusColor = (status) => {
  const colors = {
    moving: 'green',
    idle: 'orange',
    offline: 'red'
  }
  return colors[status] || 'grey'
}

const getStatusClass = (status) => {
  const classes = {
    moving: 'status-moving',
    idle: 'status-idle',
    offline: 'status-offline'
  }
  return classes[status] || ''
}

const getStatusText = (status) => {
  const texts = {
    moving: 'Moving',
    idle: 'Idle',
    offline: 'Offline'
  }
  return texts[status] || 'Unknown'
}

const selectVehicle = (vehicle) => {
  selectedVehicle.value = vehicle
}

const zoomIn = () => {
  console.log('Zoom In')
}

const zoomOut = () => {
  console.log('Zoom Out')
}

const centerMap = () => {
  console.log('Center Map')
}

const refreshLocations = () => {
  console.log('Refresh Locations')
  // Add your refresh logic here
}
</script>

<style scoped>
.map-background {
  background-image: url("https://media.wired.com/photos/59269cd37034dc5f91bec0f1/191:100/w_1280,c_limit/GoogleMapTA.jpg?mbid=social_retweet");
  z-index: 0;
  background-repeat: no-repeat;
  background-size: cover;
}

.map-section {
  height: 100vh;
}

.full-height {
  height: 100vh;
}

.dashboard-section {
  background-color: #f8f9fa;
  border-left: 1px solid #e0e0e0;
  overflow-y: auto;
}

.vehicle-marker {
  transform: translate(-50%, -50%);
  z-index: 10;
}

.vehicle-tooltip {
  position: absolute;
  top: 100%;
  left: 50%;
  transform: translateX(-50%);
  margin-top: 8px;
  white-space: nowrap;
  z-index: 20;
  min-width: 140px;
}

/* Style for small icons in list */
.vehicle-avatar {
  width: 36px;
  height: 36px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.3s ease;
}

.vehicle-avatar.status-moving {
  background-color: #4caf50; /* Green */
}

.vehicle-avatar.status-idle {
  background-color: #ff9800; /* Orange */
}

.vehicle-avatar.status-offline {
  background-color: #f44336; /* Red */
}

/* Style for location pins on map */
.location-pin {
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
}

.circle {
  width: 40px;
  height: 40px;
  background-color: white;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.3);
  z-index: 2;
  position: relative;
  transition: all 0.3s ease;
}

.circle.selected {
  box-shadow: 0 0 0 3px blue, 0 2px 6px rgba(0, 0, 0, 0.3);
  transform: scale(1.1);
}

/* Pulse animation */
.pulse {
  position: absolute;
  width: 60px;
  height: 60px;
  background-color: currentColor;
  border-radius: 50%;
  opacity: 0.6;
  z-index: 1;
  animation: pulse 2s infinite;
}

.pulse.status-moving {
  color: #4caf50;
}

.pulse.status-idle {
  color: #ff9800;
}

.pulse.status-offline {
  color: #f44336;
}

@keyframes pulse {
  0% {
    transform: scale(0.8);
    opacity: 0.6;
  }
  70% {
    transform: scale(1.2);
    opacity: 0;
  }
  100% {
    transform: scale(0.8);
    opacity: 0;
  }
}

/* Don't show pulse for offline devices */
.pulse.status-offline {
  display: none;
}

/* Menu card hover effects */
.q-card {
  transition: all 0.3s ease;
}

.q-card:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
}
</style>
