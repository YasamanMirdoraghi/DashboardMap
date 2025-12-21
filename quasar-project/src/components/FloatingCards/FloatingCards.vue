<template>
  <!-- Glass Device Card Popup -->
  <transition name="slide-up">
    <div v-if="selectedDevice" class="glass-device-card">
      <!-- Header with gradient -->
      <div class="glass-header">
        <div class="header-content">
          <!-- سمت چپ: ایکون وضعیت و سریال نامبر -->
          <div class="device-main-info">
            <div class="status-indicator" :class="getDeviceStatus(selectedDevice)">
              <!-- ایکون وضعیت دستگاه -->
              <div
                v-if="getDeviceStatus(selectedDevice) === 'online-moving'"
                class="custom-svg-icon"
              >
                <svg
                  xmlns="http://www.w3.org/2000/svg"
                  height="24px"
                  viewBox="0 -960 960 960"
                  width="24px"
                  fill="currentColor"
                >
                  <path
                    d="M480-480q33 0 56.5-23.5T560-560q0-33-23.5-56.5T480-640q-33 0-56.5 23.5T400-560q0 33 23.5 56.5T480-480Zm0 294q122-112 181-203.5T720-552q0-109-69.5-178.5T480-800q-101 0-170.5 69.5T240-552q0 71 59 162.5T480-186Zm0 106Q319-217 239.5-334.5T160-552q0-150 96.5-239T480-880q127 0 223.5 89T800-552q0 100-79.5 217.5T480-80Zm0-480Z"
                  />
                </svg>
              </div>

              <div
                v-else-if="getDeviceStatus(selectedDevice) === 'online-stopped'"
                class="custom-svg-icon"
              >
                <svg
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
              </div>

              <div
                v-else-if="getDeviceStatus(selectedDevice) === 'offline'"
                class="custom-svg-icon"
              >
                <svg
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
            
            <!-- سریال نامبر در کنار ایکون -->
            <div class="serial-info">
              <div class="serial-title">Serial Number</div>
              <div class="serial-number">{{ selectedDevice.serialnumber }}</div>
            </div>
          </div>

          <!-- سمت راست: وضعیت حرکت و دکمه بستن -->
          <div class="header-right-section">
            <div class="status-container">
              <div class="moving-status-small" :class="getDeviceStatus(selectedDevice)">
                {{ getStatusText(getDeviceStatus(selectedDevice)) }}
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
      </div>

      <!-- Card Content -->
      <div class="glass-content">
        <!-- Location Info -->
        <div class="info-section location-info">
          <div class="section-title">
            <svg
              xmlns="http://www.w3.org/2000/svg"
              height="18px"
              viewBox="0 -960 960 960"
              width="18px"
              fill="currentColor"
            >
              <path
                d="M480-480q33 0 56.5-23.5T560-560q0-33-23.5-56.5T480-640q-33 0-56.5 23.5T400-560q0 33 23.5 56.5T480-480Zm0 294q122-112 181-203.5T720-552q0-109-69.5-178.5T480-800q-101 0-170.5 69.5T240-552q0 71 59 162.5T480-186Zm0 106Q319-217 239.5-334.5T160-552q0-150 96.5-239T480-880q127 0 223.5 89T800-552q0 100-79.5 217.5T480-80Zm0-480Z"
              />
            </svg>
            <span>Location</span>
          </div>
          <div class="coordinates">
            <div class="coord-item">
              <span class="coord-label">Lat:</span>
              <span class="coord-value">{{
                selectedDevice.position.latitude?.toFixed(6) || "N/A"
              }}</span>
            </div>
            <div class="coord-item">
              <span class="coord-label">Lng:</span>
              <span class="coord-value">{{
                selectedDevice.position.longitude?.toFixed(6) || "N/A"
              }}</span>
            </div>
          </div>
          <div class="address-info" v-if="selectedDevice.position.address">
            <svg
              xmlns="http://www.w3.org/2000/svg"
              height="12px"
              viewBox="0 -960 960 960"
              width="12px"
              fill="currentColor"
            >
              <path
                d="m480-322 140 84q6 4 13 4t13-4l140-84v-398H480v322Zm-320 82v-480h720v480L480-120 160-240Zm80-480v322l240 144 240-144v-322H240Zm320 220Z"
              />
            </svg>
            <span>{{ selectedDevice.position.address }}</span>
          </div>
        </div>

        <!-- Device Stats Grid -->
        <div class="stats-grid">
          <!-- Battery -->
          <div
            class="stat-card"
            :class="getBatteryStatus(selectedDevice.position.batt_v)"
          >
            <div class="stat-icon">
              <template
                v-if="
                  getBatteryStatus(selectedDevice.position.batt_v) === 'status-critical'
                "
              >
                <svg
                  xmlns="http://www.w3.org/2000/svg"
                  height="24px"
                  viewBox="0 -960 960 960"
                  width="24px"
                  fill="currentColor"
                >
                  <path
                    d="M320-80q-17 0-28.5-11.5T280-120v-640q0-17 11.5-28.5T320-800h80v-80h160v80h80q17 0 28.5 11.5T680-760v640q0 17-11.5 28.5T640-80H320Zm40-160h240v-480H360v480Z"
                  />
                </svg>
              </template>
              <template
                v-else-if="
                  getBatteryStatus(selectedDevice.position.batt_v) === 'status-warning'
                "
              >
                <svg
                  xmlns="http://www.w3.org/2000/svg"
                  height="24px"
                  viewBox="0 -960 960 960"
                  width="24px"
                  fill="currentColor"
                >
                  <path
                    d="M320-80q-17 0-28.5-11.5T280-120v-640q0-17 11.5-28.5T320-800h80v-80h160v80h80q17 0 28.5 11.5T680-760v640q0 17-11.5 28.5T640-80H320Zm40-320h240v-320H360v320Z"
                  />
                </svg>
              </template>
              <template v-else>
                <svg
                  xmlns="http://www.w3.org/2000/svg"
                  height="24px"
                  viewBox="0 -960 960 960"
                  width="24px"
                  fill="currentColor"
                >
                  <path
                    d="M320-80q-17 0-28.5-11.5T280-120v-640q0-17 11.5-28.5T320-800h80v-80h160v80h80q17 0 28.5 11.5T680-760v640q0 17-11.5 28.5T640-80H320Zm40-560h240v-80H360v80Z"
                  />
                </svg>
              </template>
            </div>
            <div class="stat-info">
              <div class="stat-label">Battery</div>
              <div class="stat-value">
                {{ formatBattery(selectedDevice.position.batt_v) }}
              </div>
            </div>
          </div>

          <!-- Speed -->
          <div class="stat-card" :class="getSpeedStatus(selectedDevice.position.speed)">
            <div class="stat-icon">
              <svg
                xmlns="http://www.w3.org/2000/svg"
                height="24px"
                viewBox="0 -960 960 960"
                width="24px"
                fill="currentColor"
              >
                <path
                  d="M418-340q24 24 62 23.5t56-27.5l224-336-336 224q-27 18-28.5 55t22.5 61Zm62-460q59 0 113.5 16.5T696-734l-76 48q-33-17-68.5-25.5T480-720q-133 0-226.5 93.5T160-400q0 42 11.5 83t32.5 77h552q23-38 33.5-79t10.5-85q0-36-8.5-70T766-540l48-76q30 47 47.5 100T880-406q1 57-13 109t-41 99q-11 18-30 28t-40 10H204q-21 0-40-10t-30-28q-26-45-40-95.5T80-400q0-83 31.5-155.5t86-127Q252-737 325-768.5T480-800Zm7 313Z"
                />
              </svg>
            </div>
            <div class="stat-info">
              <div class="stat-label">Speed</div>
              <div class="stat-value">{{ selectedDevice.position.speed || 0 }} km/h</div>
            </div>
          </div>

          <!-- Lock -->
          <div class="stat-card" :class="getLockStatus(selectedDevice.position.flags)">
            <div class="stat-icon">
              <svg
                v-if="selectedDevice.position.flags?.isCoilActive"
                xmlns="http://www.w3.org/2000/svg"
                height="24px"
                viewBox="0 -960 960 960"
                width="24px"
                fill="currentColor"
              >
                <path
                  d="M240-80q-33 0-56.5-23.5T160-160v-400q0-33 23.5-56.5T240-640h40v-80q0-83 58.5-141.5T480-920q83 0 141.5 58.5T680-720v80h40q33 0 56.5 23.5T800-560v400q0 33-23.5 56.5T720-80H240Zm0-80h480v-400H240v400Zm240-120q33 0 56.5-23.5T560-360q0-33-23.5-56.5T480-440q-33 0-56.5 23.5T400-360q0 33 23.5 56.5T480-280ZM360-640h240v-80q0-50-35-85t-85-35q-50 0-85 35t-35 85v80Zm120 280Z"
                />
              </svg>
              <svg
                v-else
                xmlns="http://www.w3.org/2000/svg"
                height="24px"
                viewBox="0 -960 960 960"
                width="24px"
                fill="currentColor"
              >
                <path
                  d="M240-160h480v-400H240v400Zm240-120q33 0 56.5-23.5T560-360q0-33-23.5-56.5T480-440q-33 0-56.5 23.5T400-360q0 33 23.5 56.5T480-280ZM240-160v-400 400Zm0 80q-33 0-56.5-23.5T160-160v-400q0-33 23.5-56.5T240-640h280v-80q0-83 58.5-141.5T720-920q83 0 141.5 58.5T920-720h-80q0-50-35-85t-85-35q-50 0-85 35t-35 85v80h120q33 0 56.5 23.5T800-560v400q0 33-23.5 56.5T720-80H240Z"
                />
              </svg>
            </div>
            <div class="stat-info">
              <div class="stat-label">Lock</div>
              <div class="stat-value">
                {{ getLockText(selectedDevice.position.flags) }}
              </div>
            </div>
          </div>

          <!-- GSM -->
          <div class="stat-card" :class="getGSMStatus(selectedDevice.position.gsm_sig)">
            <div class="stat-icon">
              <svg
                xmlns="http://www.w3.org/2000/svg"
                height="24px"
                viewBox="0 -960 960 960"
                width="24px"
                fill="currentColor"
              >
                <path
                  d="M200-160v-240h120v240H200Zm240 0v-440h120v440H440Zm240 0v-640h120v640H680Z"
                />
              </svg>
            </div>
            <div class="stat-info">
              <div class="stat-label">GSM</div>
              <div class="stat-value">
                {{ formatGSM(selectedDevice.position.gsm_sig) }}
              </div>
            </div>
          </div>

          <!-- GNSS -->
          <div class="stat-card" :class="getGNSSStatus(selectedDevice.position.flags)">
            <div class="stat-icon">
              <svg
                xmlns="http://www.w3.org/2000/svg"
                height="24px"
                viewBox="0 -960 960 960"
                width="24px"
                fill="currentColor"
              >
                <path
                  d="M440-42v-80q-125-14-214.5-103.5T122-440H42v-80h80q14-125 103.5-214.5T440-838v-80h80v80q125 14 214.5 103.5T838-520h80v80h-80q-14 125-103.5 214.5T520-122v80h-80Zm40-158q116 0 198-82t82-198q0-116-82-198t-198-82q-116 0-198 82t-82 198q0 116 82 198t198 82Zm0-120q-66 0-113-47t-47-113q0-66 47-113t113-47q66 0 113 47t47 113q0 66-47 113t-113 47Zm0-80q33 0 56.5-23.5T560-480q0-33-23.5-56.5T480-560q-33 0-56.5 23.5T400-480q0 33 23.5 56.5T480-400Zm0-80Z"
                />
              </svg>
            </div>
            <div class="stat-info">
              <div class="stat-label">GNSS</div>
              <div class="stat-value">{{ formatGNSS(selectedDevice.position.pdop) }}</div>
            </div>
          </div>

          <!-- Satelite -->
          <div class="stat-card" :class="getSatelliteStatus(selectedDevice.position.flags)">
            <div class="stat-icon">
              <svg
                xmlns="http://www.w3.org/2000/svg"
                height="17px"
                viewBox="0 -960 960 960"
                width="17px"
                fill="currentColor"
              >
                <path
                  d="M560-32v-80q117 0 198.5-81.5T840-392h80q0 75-28.5 140.5t-77 114q-48.5 48.5-114 77T560-32Zm0-160v-80q50 0 85-35t35-85h80q0 83-58.5 141.5T560-192ZM222-57q-15 0-30-6t-27-17L23-222q-11-12-17-27t-6-30q0-16 6-30.5T23-335l127-127q23-23 57-23.5t57 22.5l50 50 28-28-50-50q-23-23-23-56t23-56l57-57q23-23 56.5-23t56.5 23l50 50 28-28-50-50q-23-23-23-56.5t23-56.5l127-127q12-12 27-18t30-6q15 0 29.5 6t26.5 18l142 142q12 11 17.5 25.5T895-730q0 15-5.5 30T872-673L745-546q-23 23-56.5 23T632-546l-50-50-28 28 50 50q23 23 22.5 56.5T603-405l-56 56q-23 23-56.5 23T434-349l-50-50-28 28 50 50q23 23 22.5 57T405-207L278-80q-11 11-25.5 17T222-57Zm0-79 42-42-142-142-42 42 142 142Zm85-85 42-42-142-142-42 42 142 142Zm184-184 56-56-142-142-56 56 142 142Zm198-198 42-42-142-142-42 42 142 142Zm85-85 42-42-142-142-42 42 142 142Zm85-85 42-42-142-142-42 42 142 142ZM448-504Z"
                />
              </svg>
            </div>
            <div class="stat-info">
              <div class="stat-label">Satelite</div>
              <div class="stat-value">
                {{ selectedDevice.position.satellites }}
              </div>
            </div>
          </div>

          <!-- Temperature -->
          <div
            class="stat-card"
            :class="getTemperatureStatus(selectedDevice.position.temperature)"
          >
            <div class="stat-icon">
              <svg
                xmlns="http://www.w3.org/2000/svg"
                height="24px"
                viewBox="0 -960 960 960"
                width="24px"
                fill="currentColor"
              >
                <path
                  d="M520-520v-80h200v80H520Zm0-160v-80h320v80H520ZM320-120q-83 0-141.5-58.5T120-320q0-48 21-89.5t59-70.5v-240q0-50 35-85t85-35q50 0 85 35t35 85v240q38 29 59 70.5t21 89.5q0 83-58.5 141.5T320-120ZM200-320h240q0-29-12.5-54T392-416l-32-24v-280q0-17-11.5-28.5T320-760q-17 0-28.5 11.5T280-720v280l-32 24q-23 17-35.5 42T200-320Z"
                />
              </svg>
            </div>
            <div class="stat-info">
              <div class="stat-label">Temperature</div>
              <div class="stat-value">
                {{ selectedDevice.position.temperature || 0 }}°C
              </div>
            </div>
          </div>

          <!-- Humidity -->
          <div
            class="stat-card"
            :class="getHumidityStatus(selectedDevice.position.humidity)"
          >
            <div class="stat-icon">
              <svg
                xmlns="http://www.w3.org/2000/svg"
                height="24px"
                viewBox="0 -960 960 960"
                width="24px"
                fill="currentColor"
              >
                <path
                  d="M480-100q-133 0-226.5-92T160-416q0-63 24.5-120.5T254-638l226-222 226 222q45 44 69.5 101.5T800-416q0 132-93.5 224T480-100ZM240-416h480q0-47-18-89.5T650-580L480-748 310-580q-34 32-52 74.5T240-416Z"
                />
              </svg>
            </div>
            <div class="stat-info">
              <div class="stat-label">Humidity</div>
              <div class="stat-value">{{ selectedDevice.position.humidity || 0 }}%</div>
            </div>
          </div>

          <!-- Operator -->
          <div
            class="stat-card"
            :class="getOperatorStatus(selectedDevice.position.flags)"
          >
            <div class="stat-icon">
              <svg
                xmlns="http://www.w3.org/2000/svg"
                height="17px"
                viewBox="0 -960 960 960"
                width="17px"
                fill="currentColor"
              >
                <path
                  d="M200-120q-33 0-56.5-23.5T120-200v-560q0-33 23.5-56.5T200-840h560q33 0 56.5 23.5T840-760v560q0 33-23.5 56.5T760-120H200Zm0-80h560v-560H200v560Zm0-560v560-560Zm120 480h320v-80H400v-80h200v-80H400v-80h240v-80H320v400Z"
                />
              </svg>
            </div>
            <div class="stat-info">
              <div class="stat-label">Operator</div>
              <div class="stat-value">{{ formatType(selectedDevice.type) || "MCI" }}</div>
            </div>
          </div>

          <!-- Rope -->
          <div
            class="stat-card"
            :class="getRopeStatus(selectedDevice.position.flags)"
          >
            <div class="stat-icon">
              <svg
                xmlns="http://www.w3.org/2000/svg"
                height="18px"
                viewBox="0 -960 960 960"
                width="18px"
                fill="currentColor"
              >
                <path
                  d="M200-120q-17 0-28.5-11.5T160-160v-40h-40v-160q0-17 11.5-28.5T160-400h40v-280q0-66 47-113t113-47q66 0 113 47t47 113v400q0 33 23.5 56.5T600-200q33 0 56.5-23.5T680-280v-280h-40q-17 0-28.5-11.5T600-600v-160h40v-40q0-17 11.5-28.5T680-840h80q17 0 28.5 11.5T800-800v40h40v160q0 17-11.5 28.5T800-560h-40v280q0 66-47 113t-113 47q-66 0-113-47t-47-113v-400q0-33-23.5-56.5T360-760q-33 0-56.5 23.5T280-680v280h40q17 0 28.5 11.5T360-360v160h-40v40q0 17-11.5 28.5T280-120h-80Z"
                />
              </svg>
            </div>
            <div class="stat-info">
              <div class="stat-label">Rope</div>
              <div class="stat-value">{{ getRopeText(selectedDevice.position.flags) }}</div>
            </div>
          </div>
        </div>

        <!-- Alarm Status -->
        <div v-if="selectedDevice.position.alarm" class="alarm-section">
          <div class="section-title">
            <svg
              xmlns="http://www.w3.org/2000/svg"
              height="18px"
              viewBox="0 -960 960 960"
              width="18px"
              fill="currentColor"
            >
              <path
                d="M160-200v-80h80v-280q0-83 50-147.5T420-792v-28q0-25 17.5-42.5T480-880q25 0 42.5 17.5T540-820v28q80 20 130 84.5T720-560v280h80v80H160Zm320-300Zm0 420q-33 0-56.5-23.5T400-160h160q0 33-23.5 56.5T480-80ZM320-280h320v-280q0-66-47-113t-113-47q-66 0-113 47t-47 113v280Z"
              />
            </svg>
            <span>Alarm</span>
          </div>
          <div class="alarm-status">
            <svg
              xmlns="http://www.w3.org/2000/svg"
              height="20px"
              viewBox="0 -960 960 960"
              width="20px"
              fill="#ff9800"
            >
              <path
                d="M40-120l440-760 440 760H40Zm138-80h604L480-720 178-200Zm302-40q17 0 28.5-11.5T520-280q0-17-11.5-28.5T480-320q-17 0-28.5 11.5T440-280q0 17 11.5 28.5T480-240Zm0-120q17 0 28.5-11.5T520-400v-120q0-17-11.5-28.5T480-560q-17 0-28.5 11.5T440-520v120q0 17 11.5 28.5T480-360Zm0-60Z"
              />
            </svg>
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
            @click="openDeviceDetails"
          >
            <template v-slot:icon>
              <svg
                xmlns="http://www.w3.org/2000/svg"
                height="20px"
                viewBox="0 -960 960 960"
                width="20px"
                fill="currentColor"
              >
                <path
                  d="M320-240h320v-80H320v80Zm0-160h320v-80H320v80ZM240-80q-33 0-56.5-23.5T160-160v-640q0-33 23.5-56.5T240-880h320l240 240v480q0 33-23.5 56.5T720-80H240Zm280-520v-200H240v640h480v-440H520ZM240-800v200-200 640-640Z"
                />
              </svg>
            </template>
          </q-btn>
        </div>
      </div>
    </div>
  </transition>
</template>

<script setup>
const props = defineProps({
  selectedDevice: Object,
});

const emit = defineEmits(["close-device", "open-single-device"]);

const closeCard = () => {
  emit("close-device");
};

const openDeviceDetails = () => {
  console.log("Opening device details for:", props.selectedDevice?.deviceid);
  if (props.selectedDevice) {
    emit("open-single-device", props.selectedDevice);
  }
};

// Status Helper Functions
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

const getStatusText = (status) => {
  const texts = {
    "online-moving": "Moving",
    "online-stopped": "Stopped",
    offline: "Offline",
  };
  return texts[status] || "Unknown";
};

// Format Functions
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
  const voltage = batt_v / 1000;
  return `${voltage.toFixed(2)}V`;
};

const getLockText = (flags) => {
  if (!flags) return "Unknown";
  return flags.isCoilActive ? "Locked" : "Unlocked";
};

const getRopeText = (flags) => {
  if (flags.isRopeClosed && flags.isMechanicClosed) {
    return "Closed";
  } else if (!flags.isRopeClosed && !flags.isMechanicClosed) {
    return "Open";
  } else {
    return "Partially";
  }
};

// Status Detection Functions
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

// New Functions for GNSS, Satellite, Operator, and Rope
const getGNSSStatus = (flags) => {
  if (!flags) return "status-critical";
  return flags.isFixed ? "status-good" : "status-critical";
};

const getSatelliteStatus = (flags) => {
  if (!flags) return "status-critical";
  return flags.isFixed ? "status-good" : "status-critical";
};

const getOperatorStatus = (flags) => {
  if (!flags) return "status-critical";
  return flags.isCIDValid ? "status-good" : "status-critical";
};


const getRopeStatus = (flags) => {
  if (!flags) return "status-critical";
  
  if (!flags.isRopeClosed && !flags.isMechanicClosed) {
    return "status-good";
  } 
 
  else if (flags.isRopeClosed && flags.isMechanicClosed) {
    return "status-critical";
  } 
 
  else {
    return "status-warning";
  }
};

// Date Format Functions
// const formatDateShort = (unixtime) => {
//   if (!unixtime) return "N/A";
//   const date = new Date(unixtime * 1000);
//   const now = new Date();
//   const diffMs = now - date;
//   const diffMins = Math.floor(diffMs / 60000);
//   const diffHours = Math.floor(diffMs / 3600000);
//   const diffDays = Math.floor(diffMs / 86400000);

//   if (diffMins < 1) {
//     return "Just now";
//   } else if (diffMins < 60) {
//     return `${diffMins} min ago`;
//   } else if (diffHours < 24) {
//     return `${diffHours} hour${diffHours > 1 ? 's' : ''} ago`;
//   } else if (diffDays < 7) {
//     return `${diffDays} day${diffDays > 1 ? 's' : ''} ago`;
//   } else {
//     return date.toLocaleString("en-US", {
//       month: "short",
//       day: "numeric",
//       hour: "2-digit",
//       minute: "2-digit",
//     });
//   }
// };
</script>

<style scoped>
/* اضافه کردن استایل برای ایکون‌های سفارشی */
.custom-svg-icon {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 24px;
  height: 24px;
}

.custom-svg-icon svg {
  width: 100%;
  height: 100%;
  color: white;
  fill: white;
}

/* Glass Morphism Device Card - Base Styles */
.glass-device-card {
  position: fixed;
  bottom: 20px;
  right: 20px;
  width: 420px;
  max-height: 96vh;
  background: rgba(37, 37, 36, 0.95);
  backdrop-filter: blur(20px) saturate(180%);
  -webkit-backdrop-filter: blur(20px) saturate(180%);
  border-radius: 20px;
  border: 1px solid rgba(255, 255, 255, 0.2);
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.3), 0 1px 0 rgba(255, 255, 255, 0.1) inset;
  z-index: 1000;
  overflow: hidden;
  font-family: "Inter", -apple-system, BlinkMacSystemFont, sans-serif;
  display: flex;
  flex-direction: column;
}

.glass-content {
  padding: 15px;
  background: rgba(0, 0, 0, 0.05);
  overflow-y: auto;
  flex: 1;
}

/* Header with gradient - استایل جدید */
.glass-header {
  background: #010101;
  padding: 16px 20px;
  border-bottom: 1px solid rgba(255, 255, 255, 0.1);
  flex-shrink: 0;
}

.header-content {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  gap: 12px;
}

/* استایل‌های سمت چپ - ایکون و سریال نامبر */
.device-main-info {
  display: flex;
  align-items: center;
  gap: 12px;
  flex: 1;
  min-width: 0;
}

.status-indicator {
  width: 44px;
  height: 44px;
  border-radius: 12px;
  display: flex;
  align-items: center;
  justify-content: center;
  color: white;
  background: rgba(255, 255, 255, 0.2);
  backdrop-filter: blur(5px);
  flex-shrink: 0;
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

.serial-info {
  display: flex;
  flex-direction: column;
  gap: 2px;
  min-width: 0;
}

.serial-title {
  font-size: 10px;
  color: rgba(255, 255, 255, 0.7);
  text-transform: uppercase;
  letter-spacing: 0.5px;
  font-weight: 500;
}

.serial-number {
  font-size: 16px;
  font-weight: 700;
  color: white;
  letter-spacing: -0.3px;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}

/* استایل‌های سمت راست - وضعیت و دکمه بستن */
.header-right-section {
  display: flex;
  flex-direction: column;
  align-items: flex-end;
  gap: 8px;
  flex-shrink: 0;
}

.status-container {
  display: flex;
  justify-content: flex-end;
}

.moving-status-small {
  display: inline-flex;
  align-items: center;
  gap: 6px;
  padding: 4px 10px;
  border-radius: 8px;
  font-size: 11px;
  font-weight: 600;
  color: white;
  text-transform: uppercase;
  letter-spacing: 0.3px;
  white-space: nowrap;
}

.moving-status-small.online-moving {
  background: rgba(29, 180, 249, 0.15);
  border: 1px solid rgba(29, 180, 249, 0.3);
}

.moving-status-small.online-stopped {
  background: rgba(255, 107, 48, 0.15);
  border: 1px solid rgba(255, 107, 48, 0.3);
}

.moving-status-small.offline {
  background: rgba(255, 255, 255, 0.15);
  border: 1px solid rgba(255, 255, 255, 0.3);
}

.header-actions {
  display: flex;
  align-items: center;
}

.glass-btn {
  color: white;
  background: rgba(255, 255, 255, 0.1);
  backdrop-filter: blur(5px);
  width: 32px;
  height: 32px;
  min-height: 32px;
  border-radius: 8px;
}

.glass-btn:hover {
  background: rgba(255, 255, 255, 0.2);
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
  font-family: "JetBrains Mono", monospace;
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
  background: rgba(255, 107, 48, 0.15);
  backdrop-filter: blur(5px);
  border: 1px solid rgba(255, 107, 48, 0.3);
  border-radius: 10px;
  color: #ff6b30;
  font-weight: 600;
  font-size: 13px;
  text-transform: uppercase;
  letter-spacing: 0.3px;
  height: 40px;
  transition: all 0.2s ease;
}

.glass-action-btn:hover {
  background: rgba(255, 107, 48, 0.25);
  border-color: rgba(255, 107, 48, 0.5);
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
@media (max-width: 1200px) {
  .glass-device-card {
    width: 380px;
  }
}

@media (max-width: 992px) {
  .glass-device-card {
    width: 350px;
    right: 15px;
    bottom: 15px;
  }

  .serial-number {
    font-size: 14px;
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

@media (max-width: 768px) {
  .glass-device-card {
    width: 320px;
    right: 10px;
    bottom: 10px;
  }

  .glass-header {
    padding: 12px 16px;
  }

  .glass-content {
    padding: 16px;
  }

  .serial-number {
    font-size: 14px;
  }

  .status-indicator {
    width: 40px;
    height: 40px;
  }

  .moving-status-small {
    font-size: 10px;
    padding: 3px 8px;
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

  .serial-number {
    font-size: 14px;
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
    width: 36px;
    height: 36px;
  }

  .glass-content {
    padding: 12px;
    max-height: calc(90vh - 52px);
  }

  .serial-number {
    font-size: 13px;
  }

  .header-right-section {
    gap: 6px;
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

  .alarm-status {
    font-size: 12px;
  }

  .glass-action-btn {
    font-size: 11px;
    height: 36px;
  }
}

@media (max-width: 360px) {
  .glass-device-card {
    width: 98%;
    border-radius: 12px;
  }

  .device-main-info {
    min-width: 150px;
  }

  .serial-number {
    font-size: 13px;
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