<template>
  <q-page class="row full-height">
    <!-- Side Menu -->
    <div class="col-3 q-pa-md dashboard-section">
      <!-- Header -->
      <div class="text-h4  q-mb-md">Tracking System</div>

      <!-- Main Menu - shown when not in device list -->
      <div v-if="currentView === 'mainMenu'" class="main-menu">
        <!-- Menu Cards -->
        <q-card class="q-mb-md cursor-pointer glass-card" @click="showDeviceList">
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

        <!-- Search Input -->
        <q-input
          v-model="searchQuery"
          placeholder="Search devices..."
          class="q-mb-md"
          outlined
          dense
          clearable
        >
          <template v-slot:prepend>
            <q-icon name="search" />
          </template>
        </q-input>

        <!-- Vehicle List -->
        <q-list bordered separator class="rounded-borders">
          <q-item
            v-for="vehicle in filteredVehicles"
            :key="vehicle.id"
            clickable
            v-ripple
            @click="selectVehicle(vehicle)"
            :class="{
              'bg-blue-1 selected-vehicle': selectedVehicle?.id === vehicle.id,
              'vehicle-item': true
            }"
          >
            <q-item-section avatar>
              <div class="vehicle-avatar" :class="getStatusClass(vehicle.status)">
                <q-icon name="sensors" size="20px" color="white" />
              </div>
            </q-item-section>

            <q-item-section>
              <q-item-label class="text-weight-bold">{{ vehicle.name }}</q-item-label>
              <q-item-label caption class="q-mt-xs">
                <q-icon
                  name="fiber_manual_record"
                  size="8px"
                  :color="getStatusColor(vehicle.status)"
                  class="q-mr-xs"
                />
                {{ getStatusDisplay(vehicle) }}
              </q-item-label>
            </q-item-section>

            <q-item-section side top>
              <div class="text-caption text-grey">
                {{ vehicle.lastUpdate }}
              </div>
            </q-item-section>
          </q-item>

          <!-- No results message -->
          <q-item v-if="filteredVehicles.length === 0" class="text-center text-grey">
            <q-item-section>
              No devices found
            </q-item-section>
          </q-item>
        </q-list>
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
        @click="handleMapVehicleClick(vehicle)"
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
          <div class="text-caption" :class="`text-${getStatusColor(vehicle.status)}`">
            {{ getStatusDisplay(vehicle) }}
          </div>
          <div class="text-caption text-grey">
            {{ vehicle.lastUpdate }}
          </div>
        </div>
      </div>
    </div>
  </q-page>
</template>

<script setup>
import { ref, computed } from 'vue'

const currentView = ref('mainMenu') // 'mainMenu' or 'deviceList'
const selectedVehicle = ref(null)
const searchQuery = ref('')

// Sample vehicle data - fixed status values
const vehicles = ref([
  {
    id: 1,
    name: 'Device 1',
    status: 'online',
    position: { x: 25, y: 40 },
    lastUpdate: '2 minutes ago'
  },
  {
    id: 2,
    name: 'Device 2',
    status: 'offline',
    position: { x: 60, y: 30 },
    lastUpdate: '15 hours ago'
  },
  {
    id: 3,
    name: 'Device 3',
    status: 'online',
    position: { x: 45, y: 65 },
    lastUpdate: '1 minute ago'
  },
  {
    id: 4,
    name: 'Device 4',
    status: 'idle',
    position: { x: 30, y: 20 },
    lastUpdate: '5 minutes ago'
  },
  {
    id: 5,
    name: 'Device 5',
    status: 'offline',
    position: { x: 70, y: 50 },
    lastUpdate: '3 hours ago'
  },
  {
    id: 6,
    name: 'Device 6',
    status: 'offline',
    position: { x: 60, y: 20 },
    lastUpdate: '3 hours ago'
  },
  {
    id: 7,
    name: 'Device 7',
    status: 'offline',
    position: { x: 30, y: 40 },
    lastUpdate: '3 hours ago'
  },
  {
    id: 8,
    name: 'Device 8',
    status: 'offline',
    position: { x: 70, y: 60 },
    lastUpdate: '3 hours ago'
  },
  {
    id: 9,
    name: 'Device 9',
    status: 'offline',
    position: { x: 80, y: 50 },
    lastUpdate: '3 hours ago'
  },
  {
    id: 10,
    name: 'Device 10',
    status: 'online',
    position: { x: 50, y: 50 },
    lastUpdate: '3 hours ago'
  }
])

// Computed property for filtered vehicles based on search
const filteredVehicles = computed(() => {
  if (!searchQuery.value) {
    return vehicles.value
  }

  const query = searchQuery.value.toLowerCase()
  return vehicles.value.filter(vehicle =>
    vehicle.name.toLowerCase().includes(query)
  )
})

// View management functions
const showMainMenu = () => {
  currentView.value = 'mainMenu'
  selectedVehicle.value = null
  searchQuery.value = ''
}

const showDeviceList = () => {
  currentView.value = 'deviceList'
  searchQuery.value = ''
}

// Vehicle selection with auto-switch to device list
const selectVehicle = (vehicle) => {
  selectedVehicle.value = vehicle
  // Ensure we're in device list view when selecting from list
  if (currentView.value !== 'deviceList') {
    currentView.value = 'deviceList'
  }
}

// Handle map vehicle click - switches to device list and selects vehicle
const handleMapVehicleClick = (vehicle) => {
  selectedVehicle.value = vehicle
  // Switch to device list view when clicking on map
  if (currentView.value !== 'deviceList') {
    currentView.value = 'deviceList'
  }
}

// Helper functions
const getStatusColor = (status) => {
  const colors = {
    online: 'green',
    idle: 'orange',
    offline: 'red'
  }
  return colors[status] || 'grey'
}

const getStatusClass = (status) => {
  const classes = {
    online: 'status-online',
    idle: 'status-idle',
    offline: 'status-offline'
  }
  return classes[status] || ''
}

// const getStatusText = (status) => {
//   const texts = {
//     online: 'Online',
//     idle: 'Idle',
//     offline: 'Offline'
//   }
//   return texts[status] || 'Unknown'
// }

// New function to get display text for status with time
const getStatusDisplay = (vehicle) => {
  if (vehicle.status === 'online') {
    return 'Online now'
  } else if (vehicle.status === 'idle') {
    return `Idle - ${vehicle.lastUpdate}`
  } else {
    return `Offline - ${vehicle.lastUpdate}`
  }
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
  background-color: #fbfbfb;
  border-left: 2px solid #0095da;
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


.vehicle-avatar.status-online {
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
  background-color: #ffffff;
  border:1px solid #0095da;
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
  box-shadow: 0 0 0 3px #0095da, 0 2px 6px rgba(0, 0, 0, 0.3);
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

.pulse.status-online {
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

/* Enhanced list item styles */
.vehicle-item {
  transition: all 0.3s ease;
  border-left: 3px solid transparent;
}

.vehicle-item:hover {
  background-color: rgba(207, 230, 248, 0.759) !important;
  border-left: 3px solid #0095da;
}
.vehicle-item:hover .vehicle-avatar {
  transform: scale(1.1);
  rotate: 10deg;
}

.selected-vehicle {
  background-color: #bbdefb !important;
  border-left: 3px solid #0095da;
  font-weight: bold;
}

/* Search input styling */
:deep(.q-field--outlined .q-field__control) {
  border-radius: 8px;
}

:deep(.q-field--outlined .q-field__control:hover) {
  border-color: #fafafa;
}
</style>
