
<template>
  <!-- Glass Device Card Popup -->
  <transition name="slide-up">
    <div v-if="selectedDevice" class="glass-device-card">
      <!-- Header with gradient -->
      <div class="glass-header">
        <div class="header-content">
          <div class="device-main-info">
            <div class="status-indicator" :class="getDeviceStatus(selectedDevice)">
              <!-- ایکون وضعیت دستگاه -->
              
              <div v-if="getDeviceStatus(selectedDevice) === 'online-moving'" class="custom-svg-icon">
                <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960" width="24px" fill="currentColor">
                  <path d="M480-480q33 0 56.5-23.5T560-560q0-33-23.5-56.5T480-640q-33 0-56.5 23.5T400-560q0 33 23.5 56.5T480-480Zm0 294q122-112 181-203.5T720-552q0-109-69.5-178.5T480-800q-101 0-170.5 69.5T240-552q0 71 59 162.5T480-186Zm0 106Q319-217 239.5-334.5T160-552q0-150 96.5-239T480-880q127 0 223.5 89T800-552q0 100-79.5 217.5T480-80Zm0-480Z"/>
                </svg>
              </div>

              <div v-else-if="getDeviceStatus(selectedDevice) === 'online-stopped'" class="custom-svg-icon">
                <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960" width="24px" fill="currentColor">
                  <path d="M320-200h320v-400H320v400Zm0 80q-33 0-56.5-23.5T240-200v-400q0-33 23.5-56.5T320-680h320q33 0 56.5 23.5T720-600v400q0 33-23.5 56.5T640-120H320Zm160-280Z"/>
                </svg>
              </div>

              <div v-else class="custom-svg-icon">
                <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960" width="24px" fill="currentColor">
                  <path d="M560-560q0-33-23.5-56.5T480-640q-10 0-19 2t-17 7l107 107q5-8 7-17t2-19Zm168 213-58-58q25-42 37.5-78.5T720-552q0-109-69.5-178.5T480-800q-44 0-82.5 13.5T328-747l-57-57q43-37 97-56.5T480-880q127 0 223.5 89T800-552q0 48-18 98.5T728-347Zm-157 71L244-603q-2 12-3 25t-1 26q0 71 59 162.5T480-186q26-23 48.5-45.5T571-276ZM819-28 627-220q-32 34-68 69t-79 71Q319-217 239.5-334.5T160-552q0-32 5-61t14-55L27-820l57-57L876-85l-57 57ZM408-439Zm91-137Z"/>
                </svg>
              </div>

            </div>
            <div class="device-title-section">
              <!-- <h3 class="device-name">Device {{ selectedDevice.deviceid }}</h3> -->
              <h3 class="device-name">{{ selectedDevice.serialnumber }}</h3>

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
            <!-- ایکون ساعت -->
            <svg xmlns="http://www.w3.org/2000/svg" height="14px" viewBox="0 -960 960 960" width="14px" fill="currentColor">
              <path d="m612-292 56-56-148-148v-184h-80v216l172 172ZM480-80q-83 0-156-31.5T197-197q-54-54-85.5-127T80-480q0-83 31.5-156T197-763q54-54 127-85.5T480-880q83 0 156 31.5T763-763q54 54 85.5 127T880-480q0 83-31.5 156T763-197q-54 54-127 85.5T480-80Zm0-400Zm0 320q133 0 226.5-93.5T800-480q0-133-93.5-226.5T480-800q-133 0-226.5 93.5T160-480q0 133 93.5 226.5T480-160Z"/>
            </svg>
            <span>{{ formatDate(selectedDevice.position.unixtime) }}</span>
          </div>
        </div>

        <!-- Location Info -->
        <div class="info-section location-info">
          <div class="section-title">
            <!-- ایکون موقعیت -->
            <svg xmlns="http://www.w3.org/2000/svg" height="18px" viewBox="0 -960 960 960" width="18px" fill="currentColor">
              <path d="M480-480q33 0 56.5-23.5T560-560q0-33-23.5-56.5T480-640q-33 0-56.5 23.5T400-560q0 33 23.5 56.5T480-480Zm0 294q122-112 181-203.5T720-552q0-109-69.5-178.5T480-800q-101 0-170.5 69.5T240-552q0 71 59 162.5T480-186Zm0 106Q319-217 239.5-334.5T160-552q0-150 96.5-239T480-880q127 0 223.5 89T800-552q0 100-79.5 217.5T480-80Zm0-480Z"/>
            </svg>
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
            <!-- ایکون مکان -->
            <svg xmlns="http://www.w3.org/2000/svg" height="12px" viewBox="0 -960 960 960" width="12px" fill="currentColor">
              <path d="m480-322 140 84q6 4 13 4t13-4l140-84v-398H480v322Zm-320 82v-480h720v480L480-120 160-240Zm80-480v322l240 144 240-144v-322H240Zm320 220Z"/>
            </svg>
            <span>{{ selectedDevice.position.address }}</span>
          </div>
          <div class="fixed-status" v-if="selectedDevice.position.flags">
            <div class="fixed-badge" :class="getFixedStatus(selectedDevice.position.flags)">
              <!-- ایکون وضعیت فیکس -->
              <svg v-if="selectedDevice.position.flags.isFixed" xmlns="http://www.w3.org/2000/svg" height="12px" viewBox="0 -960 960 960" width="12px" fill="currentColor">
                <path d="M382-80 154-308l57-57 171 171 367-367 57 57-424 424Zm0-240L154-548l57-57 171 171 367-367 57 57-424 424Z"/>
              </svg>
              <svg v-else xmlns="http://www.w3.org/2000/svg" height="12px" viewBox="0 -960 960 960" width="12px" fill="currentColor">
                <path d="m256-200-56-56 224-224-224-224 56-56 224 224 224-224 56 56-224 224 224 224-56 56-224-224-224 224Z"/>
              </svg>
              <span>{{ selectedDevice.position.flags.isFixed ? 'Fixed' : 'Not Fixed' }}</span>
            </div>
          </div>
        </div>

        <!-- Device Stats Grid -->
        <div class="stats-grid">
          <!-- Battery -->
          <div class="stat-card" :class="getBatteryStatus(selectedDevice.position.batt_v)">
            <div class="stat-icon">
              <!-- ایکون باتری -->
              <template v-if="getBatteryStatus(selectedDevice.position.batt_v) === 'status-critical'">
                <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960" width="24px" fill="currentColor">
                  <path d="M320-80q-17 0-28.5-11.5T280-120v-640q0-17 11.5-28.5T320-800h80v-80h160v80h80q17 0 28.5 11.5T680-760v640q0 17-11.5 28.5T640-80H320Zm40-160h240v-480H360v480Z"/>
                </svg>
              </template>
              <template v-else-if="getBatteryStatus(selectedDevice.position.batt_v) === 'status-warning'">
                <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960" width="24px" fill="currentColor">
                  <path d="M320-80q-17 0-28.5-11.5T280-120v-640q0-17 11.5-28.5T320-800h80v-80h160v80h80q17 0 28.5 11.5T680-760v640q0 17-11.5 28.5T640-80H320Zm40-320h240v-320H360v320Z"/>
                </svg>
              </template>
              <template v-else>
                <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960" width="24px" fill="currentColor">
                  <path d="M320-80q-17 0-28.5-11.5T280-120v-640q0-17 11.5-28.5T320-800h80v-80h160v80h80q17 0 28.5 11.5T680-760v640q0 17-11.5 28.5T640-80H320Zm40-560h240v-80H360v80Z"/>
                </svg>
              </template>
            </div>
            <div class="stat-info">
              <div class="stat-label">Battery</div>
              <div class="stat-value">{{ formatBattery(selectedDevice.position.batt_v) }}</div>
            </div>
          </div>

          <!-- Speed -->
          <div class="stat-card" :class="getSpeedStatus(selectedDevice.position.speed)">
            <div class="stat-icon">
              <!-- ایکون سرعت -->
              <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960" width="24px" fill="currentColor">
                <path d="M418-340q24 24 62 23.5t56-27.5l224-336-336 224q-27 18-28.5 55t22.5 61Zm62-460q59 0 113.5 16.5T696-734l-76 48q-33-17-68.5-25.5T480-720q-133 0-226.5 93.5T160-400q0 42 11.5 83t32.5 77h552q23-38 33.5-79t10.5-85q0-36-8.5-70T766-540l48-76q30 47 47.5 100T880-406q1 57-13 109t-41 99q-11 18-30 28t-40 10H204q-21 0-40-10t-30-28q-26-45-40-95.5T80-400q0-83 31.5-155.5t86-127Q252-737 325-768.5T480-800Zm7 313Z"/>
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
              <!-- ایکون قفل -->
              <svg v-if="selectedDevice.position.flags?.isCoilActive" xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960" width="24px" fill="currentColor">
                <path d="M240-80q-33 0-56.5-23.5T160-160v-400q0-33 23.5-56.5T240-640h40v-80q0-83 58.5-141.5T480-920q83 0 141.5 58.5T680-720v80h40q33 0 56.5 23.5T800-560v400q0 33-23.5 56.5T720-80H240Zm0-80h480v-400H240v400Zm240-120q33 0 56.5-23.5T560-360q0-33-23.5-56.5T480-440q-33 0-56.5 23.5T400-360q0 33 23.5 56.5T480-280ZM360-640h240v-80q0-50-35-85t-85-35q-50 0-85 35t-35 85v80Zm120 280Z"/>
              </svg>
              <svg v-else xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960" width="24px" fill="currentColor">
                <path d="M240-160h480v-400H240v400Zm240-120q33 0 56.5-23.5T560-360q0-33-23.5-56.5T480-440q-33 0-56.5 23.5T400-360q0 33 23.5 56.5T480-280ZM240-160v-400 400Zm0 80q-33 0-56.5-23.5T160-160v-400q0-33 23.5-56.5T240-640h280v-80q0-83 58.5-141.5T720-920q83 0 141.5 58.5T920-720h-80q0-50-35-85t-85-35q-50 0-85 35t-35 85v80h120q33 0 56.5 23.5T800-560v400q0 33-23.5 56.5T720-80H240Z"/>
              </svg>
            </div>
            <div class="stat-info">
              <div class="stat-label">Lock</div>
              <div class="stat-value">{{ getLockText(selectedDevice.position.flags) }}</div>
            </div>
          </div>

          <!-- GSM -->
          <div class="stat-card" :class="getGSMStatus(selectedDevice.position.gsm_sig)">
            <div class="stat-icon">
              <!-- ایکون سیگنال GSM -->
              <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960" width="24px" fill="currentColor">
                <path d="M200-160v-240h120v240H200Zm240 0v-440h120v440H440Zm240 0v-640h120v640H680Z"/>
              </svg>
            </div>
            <div class="stat-info">
              <div class="stat-label">GSM</div>
              <div class="stat-value">{{ formatGSM(selectedDevice.position.gsm_sig) }}</div>
            </div>
          </div>

          <!-- GNSS -->
          <div class="stat-card" :class="getGNSSStatus(selectedDevice.position.satellites, selectedDevice.position.pdop)">
            <div class="stat-icon">
              <!-- ایکون GNSS -->
              <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960" width="24px" fill="currentColor">
                <path d="M440-42v-80q-125-14-214.5-103.5T122-440H42v-80h80q14-125 103.5-214.5T440-838v-80h80v80q125 14 214.5 103.5T838-520h80v80h-80q-14 125-103.5 214.5T520-122v80h-80Zm40-158q116 0 198-82t82-198q0-116-82-198t-198-82q-116 0-198 82t-82 198q0 116 82 198t198 82Zm0-120q-66 0-113-47t-47-113q0-66 47-113t113-47q66 0 113 47t47 113q0 66-47 113t-113 47Zm0-80q33 0 56.5-23.5T560-480q0-33-23.5-56.5T480-560q-33 0-56.5 23.5T400-480q0 33 23.5 56.5T480-400Zm0-80Z"/>
              </svg>
            </div>
            <div class="stat-info">
              <div class="stat-label">GNSS</div>
              <div class="stat-value">{{ formatGNSS(selectedDevice.position.pdop) }}</div>
            </div>
          </div>

          <!-- Temperature -->
          <div class="stat-card" :class="getTemperatureStatus(selectedDevice.position.temperature)">
            <div class="stat-icon">
              <!-- ایکون دما -->
              <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960" width="24px" fill="currentColor">
                <path d="M520-520v-80h200v80H520Zm0-160v-80h320v80H520ZM320-120q-83 0-141.5-58.5T120-320q0-48 21-89.5t59-70.5v-240q0-50 35-85t85-35q50 0 85 35t35 85v240q38 29 59 70.5t21 89.5q0 83-58.5 141.5T320-120ZM200-320h240q0-29-12.5-54T392-416l-32-24v-280q0-17-11.5-28.5T320-760q-17 0-28.5 11.5T280-720v280l-32 24q-23 17-35.5 42T200-320Z"/>
              </svg>
            </div>
            <div class="stat-info">
              <div class="stat-label">Temperature</div>
              <div class="stat-value">{{ selectedDevice.position.temperature || 0 }}°C</div>
            </div>
          </div>

          <!-- Humidity -->
          <div class="stat-card" :class="getHumidityStatus(selectedDevice.position.humidity)">
            <div class="stat-icon">
              <!-- ایکون رطوبت -->
              <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960" width="24px" fill="currentColor">
                <path d="M480-100q-133 0-226.5-92T160-416q0-63 24.5-120.5T254-638l226-222 226 222q45 44 69.5 101.5T800-416q0 132-93.5 224T480-100ZM240-416h480q0-47-18-89.5T650-580L480-748 310-580q-34 32-52 74.5T240-416Z"/>
              </svg>
            </div>
            <div class="stat-info">
              <div class="stat-label">Humidity</div>
              <div class="stat-value">{{ selectedDevice.position.humidity || 0 }}%</div>
            </div>
          </div>

          <!-- Network -->
          <div class="stat-card network">
            <div class="stat-icon">
              <!-- ایکون شبکه -->
              <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960" width="24px" fill="currentColor">
                <path d="M120-120q-33 0-56.5-23.5T40-200v-560q0-33 23.5-56.5T120-840h720q33 0 56.5 23.5T920-760v560q0 33-23.5 56.5T840-120H120Zm0-80h720v-560H120v560Zm600-320H640v80h40v80H560v-240h200q0-33-23.5-56.5T680-680H560q-33 0-56.5 23.5T480-600v240q0 33 23.5 56.5T560-280h120q33 0 56.5-23.5T760-360v-160ZM200-280h160q33 0 56.5-23.5T440-360v-60q0-25-17.5-42.5T380-480q25 0 42.5-17.5T440-540v-60q0-33-23.5-56.5T360-680H200v80h160v80H200v80h160v80H200v80Zm320-240Z"/>
              </svg>
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
            <!-- ایکون طناب -->
            <svg xmlns="http://www.w3.org/2000/svg" height="18px" viewBox="0 -960 960 960" width="18px" fill="currentColor">
              <path d="M200-120q-17 0-28.5-11.5T160-160v-40h-40v-160q0-17 11.5-28.5T160-400h40v-280q0-66 47-113t113-47q66 0 113 47t47 113v400q0 33 23.5 56.5T600-200q33 0 56.5-23.5T680-280v-280h-40q-17 0-28.5-11.5T600-600v-160h40v-40q0-17 11.5-28.5T680-840h80q17 0 28.5 11.5T800-800v40h40v160q0 17-11.5 28.5T800-560h-40v280q0 66-47 113t-113 47q-66 0-113-47t-47-113v-400q0-33-23.5-56.5T360-760q-33 0-56.5 23.5T280-680v280h40q17 0 28.5 11.5T360-360v160h-40v40q0 17-11.5 28.5T280-120h-80Z"/>
            </svg>
            <span>Rope Status</span>
          </div>
          <div class="rope-status" :class="getRopeStatus(selectedDevice.position.flags)">
            <!-- ایکون وضعیت طناب -->
            <svg v-if="getRopeStatus(selectedDevice.position.flags) === 'status-good'" xmlns="http://www.w3.org/2000/svg" height="18px" viewBox="0 -960 960 960" width="18px" fill="currentColor">
              <path d="M382-80 154-308l57-57 171 171 367-367 57 57-424 424Zm0-240L154-548l57-57 171 171 367-367 57 57-424 424Z"/>
            </svg>
            <svg v-else-if="getRopeStatus(selectedDevice.position.flags) === 'status-warning'" xmlns="http://www.w3.org/2000/svg" height="18px" viewBox="0 -960 960 960" width="18px" fill="currentColor">
              <path d="M40-120l440-760 440 760H40Zm138-80h604L480-720 178-200Zm302-40q17 0 28.5-11.5T520-280q0-17-11.5-28.5T480-320q-17 0-28.5 11.5T440-280q0 17 11.5 28.5T480-240Zm0-120q17 0 28.5-11.5T520-400v-120q0-17-11.5-28.5T480-560q-17 0-28.5 11.5T440-520v120q0 17 11.5 28.5T480-360Zm0-60Z"/>
            </svg>
            <svg v-else xmlns="http://www.w3.org/2000/svg" height="18px" viewBox="0 -960 960 960" width="18px" fill="currentColor">
              <path d="m256-200-56-56 224-224-224-224 56-56 224 224 224-224 56 56-224 224 224 224-56 56-224-224-224 224Z"/>
            </svg>
            <span>{{ getRopeText(selectedDevice.position.flags) }}</span>
          </div>
        </div>

        <!-- Alarm Status -->
        <div v-if="selectedDevice.position.alarm" class="alarm-section">
          <div class="section-title">
            <!-- ایکون آلارم -->
            <svg xmlns="http://www.w3.org/2000/svg" height="18px" viewBox="0 -960 960 960" width="18px" fill="currentColor">
              <path d="M160-200v-80h80v-280q0-83 50-147.5T420-792v-28q0-25 17.5-42.5T480-880q25 0 42.5 17.5T540-820v28q80 20 130 84.5T720-560v280h80v80H160Zm320-300Zm0 420q-33 0-56.5-23.5T400-160h160q0 33-23.5 56.5T480-80ZM320-280h320v-280q0-66-47-113t-113-47q-66 0-113 47t-47 113v280Z"/>
            </svg>
            <span>Alarm</span>
          </div>
          <div class="alarm-status">
            <!-- ایکون هشدار -->
            <svg xmlns="http://www.w3.org/2000/svg" height="20px" viewBox="0 -960 960 960" width="20px" fill="#ff9800">
              <path d="M40-120l440-760 440 760H40Zm138-80h604L480-720 178-200Zm302-40q17 0 28.5-11.5T520-280q0-17-11.5-28.5T480-320q-17 0-28.5 11.5T440-280q0 17 11.5 28.5T480-240Zm0-120q17 0 28.5-11.5T520-400v-120q0-17-11.5-28.5T480-560q-17 0-28.5 11.5T440-520v120q0 17 11.5 28.5T480-360Zm0-60Z"/>
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
            <!-- ایکون جزئیات -->
            <template v-slot:icon>
              <svg xmlns="http://www.w3.org/2000/svg" height="20px" viewBox="0 -960 960 960" width="20px" fill="currentColor">
                <path d="M320-240h320v-80H320v80Zm0-160h320v-80H320v80ZM240-80q-33 0-56.5-23.5T160-160v-640q0-33 23.5-56.5T240-880h320l240 240v480q0 33-23.5 56.5T720-80H240Zm280-520v-200H240v640h480v-440H520ZM240-800v200-200 640-640Z"/>
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
  selectedDevice: Object
});

const emit = defineEmits(['close-device', 'open-single-device']);

const closeCard = () => {
  emit('close-device');
};

const openDeviceDetails = () => {
  console.log('Opening device details for:', props.selectedDevice?.deviceid);
  if (props.selectedDevice) {
    emit('open-single-device', props.selectedDevice);
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
    'online-moving': 'Moving',
    'online-stopped': 'Stopped',
    'offline': 'Offline'
  };
  return texts[status] || 'Unknown';
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
  if (!flags) return "Unknown";
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
  background: rgba(0, 0, 0, 0.05);
  overflow-y: auto;
  flex: 1;
}

/* Header with gradient */
.glass-header {
  background: #010101;
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
  gap: 16px;
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