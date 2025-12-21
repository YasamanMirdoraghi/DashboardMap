<template>
  <!-- Sidebar Container -->
  <div class="sidebar-container">
    <!-- Sidebar Menu -->
    <q-card
      class="icon-sidebar"
      :class="{
        'sidebar-expanded': isHovering,
        'sidebar-open': sidebarOpen,
      }"
      @mouseleave="handleSidebarLeave"
      elevated
    >
      <div class="sidebar-header">
        <q-btn round dense flat icon="menu" class="menu-btn" @click="toggleSidebar" />
        <span class="sidebar-title">Menu</span>
      </div>
      <!-- Menu Open -->
      <div class="icon-items" v-show="sidebarOpen">
        <div
          v-for="(item, index) in menuItems"
          :key="item.id"
          :class="{
            active: activePanel === item.id,
          }"
          class="icon-item-wrapper"
          @click="activatePanel(item.id)"
          @mouseenter="handleIconHover"
          :style="{ '--item-index': index }"
        >
          <div class="icon-item-content">
            <div class="icon-container">
              <div class="icon-wrapper">
                <!-- Home Icon -->
                <div v-if="item.id === 'home'" class="custom-svg-icon">
                  <svg
                    xmlns="http://www.w3.org/2000/svg"
                    height="24px"
                    viewBox="0 -960 960 960"
                    width="24px"
                    fill="currentColor"
                  >
                    <path
                      d="M240-200h120v-240h240v240h120v-360L480-740 240-560v360Zm-80 80v-480l320-240 320 240v480H520v-240h-80v240H160Zm320-350Z"
                    />
                  </svg>
                </div>

                <!-- Geofence Icon -->
                <div v-else-if="item.id === 'geofence'" class="custom-svg-icon">
                  <svg
                    xmlns="http://www.w3.org/2000/svg"
                    height="24px"
                    viewBox="0 -960 960 960"
                    width="24px"
                    fill="currentColor"
                  >
                    <path
                      d="M480-80q-83 0-156-31.5T197-197q-54-54-85.5-127T80-480q0-83 31.5-156T197-763q54-54 127-85.5T480-880q83 0 156 31.5T763-763q54 54 85.5 127T880-480q0 83-31.5 156T763-197q-54 54-127 85.5T480-80Zm0-80q134 0 227-93t93-227q0-7-.5-14.5T799-507q-5 29-27 48t-52 19h-80q-33 0-56.5-23.5T560-520v-40H400v-80q0-33 23.5-56.5T480-720h40q0-23 12.5-40.5T563-789q-20-5-40.5-8t-42.5-3q-134 0-227 93t-93 227h200q66 0 113 47t47 113v40H400v110q20 5 39.5 7.5T480-160Z"
                    />
                  </svg>
                </div>

                <!-- RFID Icon -->
                <div v-else-if="item.id === 'rfid'" class="custom-svg-icon">
                  <svg
                    xmlns="http://www.w3.org/2000/svg"
                    height="24px"
                    viewBox="0 -960 960 960"
                    width="24px"
                    fill="currentColor"
                  >
                    <path
                      d="m159-168-34-14q-31-13-41.5-45t3.5-63l72-156v278Zm160 88q-33 0-56.5-23.5T239-160v-240l106 294q3 7 6 13.5t8 12.5h-40Zm206-4q-32 12-62-3t-42-47L243-622q-12-32 2-62.5t46-41.5l302-110q32-12 62 3t42 47l178 488q12 32-2 62.5T827-194L525-84Zm-86-476q17 0 28.5-11.5T479-600q0-17-11.5-28.5T439-640q-17 0-28.5 11.5T399-600q0 17 11.5 28.5T439-560Zm58 400 302-110-178-490-302 110 178 490ZM319-650l302-110-302 110Z"
                    />
                  </svg>
                </div>

                <!-- Alarms Icon -->
                <div v-else-if="item.id === 'alarms'" class="custom-svg-icon">
                  <svg
                    xmlns="http://www.w3.org/2000/svg"
                    height="24px"
                    viewBox="0 -960 960 960"
                    width="24px"
                    fill="currentColor"
                  >
                    <path
                      d="M160-200v-80h80v-280q0-83 50-147.5T420-792v-28q0-25 17.5-42.5T480-880q25 0 42.5 17.5T540-820v28q80 20 130 84.5T720-560v280h80v80H160Zm320-300Zm0 420q-33 0-56.5-23.5T400-160h160q0 33-23.5 56.5T480-80ZM320-280h320v-280q0-66-47-113t-113-47q-66 0-113 47t-47 113v280Z"
                    />
                  </svg>
                </div>

                <!-- Settings Icon -->
                <div v-else-if="item.id === 'settings'" class="custom-svg-icon">
                  <svg
                    xmlns="http://www.w3.org/2000/svg"
                    height="24px"
                    viewBox="0 -960 960 960"
                    width="24px"
                    fill="currentColor"
                  >
                    <path
                      d="M686-132 444-376q-20 8-40.5 12t-43.5 4q-100 0-170-70t-70-170q0-36 10-68.5t28-61.5l146 146l72-72-146-146q29-18 61.5-28t68.5-10q100 0 170 70t70 170q0 23-4 43.5T584-516l244 242q12 12 12 29t-12 29l-84 84q-12 12-29 12t-29-12Zm29-85 27-27-256-256q18-20 26-46.5t8-53.5q0-60-38.5-104.5T386-758l74 74q12 12 12 28t-12 28L332-500q-12 12-28 12t-28-12l-74-74q9 57 53.5 95.5T360-440q26 0 52-8t47-25l256 256ZM472-488Z"
                    />
                  </svg>
                </div>
              </div>
            </div>
            <span class="item-label">{{ item.title }}</span>
          </div>
        </div>
      </div>
    </q-card>
    <!-- EndMenu -->

    <!-- Panels Container with Transition Group -->
    <transition-group name="panel-transition" tag="div" class="panels-container">
      <!-- Home Panel -->
      <div
        v-if="activePanel === 'home' && sidebarOpen"
        key="home"
        class="home-panel-container"
      >
        <!-- Header Device List -->
        <div
          class="home-header-static"
          :class="{ 'header-expanded': homeExpanded }"
          @click="toggleHomePanel"
        >
          <div class="home-header-content">
            <div class="header-top-row">
              <div class="home-text">Devices List:</div>
            </div>
            <div class="header-bottom-row">
              <div class="total-devices-section">
                <div class="devices-count">
                  Total Devices:
                  <span class="count-badge">{{ totalDevices }}</span>
                </div>
              </div>
              <div class="status-stats-section">
                <div class="stat-item">
                  <div class="stat-info">
                    <span class="stat-dot moving"></span>
                    <span class="stat-label">Moving:</span>
                  </div>
                  <span class="stat-count">{{ movingDevices }}</span>
                </div>
                <div class="stat-item">
                  <div class="stat-info">
                    <span class="stat-dot stopped"></span>
                    <span class="stat-label">Stopped:</span>
                  </div>
                  <span class="stat-count">{{ stoppedDevices }}</span>
                </div>
                <div class="stat-item">
                  <div class="stat-info">
                    <span class="stat-dot offline"></span>
                    <span class="stat-label">Offline:</span>
                  </div>
                  <span class="stat-count">{{ offlineDevices }}</span>
                </div>
              </div>
            </div>
          </div>
        </div>

        <!-- Body Device List -->
        <div class="home-content-wrapper" :class="{ expanded: homeExpanded }">
          <q-card class="home-content-panel" elevated>
            <div class="home-expanded-content">
              <div class="devices-section">
                <div class="devices-list">
                  <div
                    v-for="device in sortedDevices"
                    :key="device.deviceid"
                    :class="[
                      'device-item',
                      getDeviceStatus(device),
                      { 'pinned-device': isDevicePinned(device.deviceid) },
                      { expanded: expandedDeviceId === device.deviceid },
                    ]"
                    @click="toggleDeviceDetails(device, $event)"
                  >
                    <div class="device-row-first">
                      <div class="device-column icon-column">
                        <div class="device-marker">
                          <div class="marker-visual">
                            <div class="marker-main">
                              <div class="marker-inner">
                                <svg
                                  v-if="getDeviceStatus(device).includes('online')"
                                  xmlns="http://www.w3.org/2000/svg"
                                  height="15px"
                                  viewBox="0 -960 960 960"
                                  width="15px"
                                  :fill="
                                    getDeviceStatus(device) === 'online-moving'
                                      ? '#1db4f9'
                                      : '#ff6b30'
                                  "
                                >
                                  <path
                                    d="M480-480q33 0 56.5-23.5T560-560q0-33-23.5-56.5T480-640q-33 0-56.5 23.5T400-560q0 33 23.5 56.5T480-480Zm0 294q122-112 181-203.5T720-552q0-109-69.5-178.5T480-800q-101 0-170.5 69.5T240-552q0 71 59 162.5T480-186Zm0 106Q319-217 239.5-334.5T160-552q0-150 96.5-239T480-880q127 0 223.5 89T800-552q0 100-79.5 217.5T480-80Zm0-480Z"
                                  />
                                </svg>
                                <svg
                                  v-else
                                  xmlns="http://www.w3.org/2000/svg"
                                  height="14px"
                                  viewBox="0 -960 960 960"
                                  width="14px"
                                  fill="#ffffff"
                                >
                                  <path
                                    d="M560-560q0-33-23.5-56.5T480-640q-10 0-19 2t-17 7l107 107q5-8 7-17t2-19Zm168 213-58-58q25-42 37.5-78.5T720-552q0-109-69.5-178.5T480-800q-44 0-82.5 13.5T328-747l-57-57q43-37 97-56.5T480-880q127 0 223.5 89T800-552q0 48-18 98.5T728-347Zm-157 71L244-603q-2 12-3 25t-1 26q0 71 59 162.5T480-186q26-23 48.5-45.5T571-276ZM819-28 627-220q-32 34-68 69t-79 71Q319-217 239.5-334.5T160-552q0-32 5-61t14-55L27-820l57-57L876-85l-57 57ZM408-439Zm91-137Z"
                                  />
                                </svg>
                              </div>
                            </div>
                          </div>
                        </div>
                      </div>
                      <!-- Device Name -->
                      <div class="device-column name-column">
                        <div class="device-name">
                          Device {{ device.deviceid }}
                          <span
                            v-if="isDevicePinned(device.deviceid)"
                            class="pinned-badge"
                          >
                            Pinned
                          </span>
                        </div>
                      </div>

                      <!-- Last Seen -->
                      <div class="device-column lastseen-column">
                        <div class="device-last-seen">
                          {{ device.lastSeen || "Just now" }}
                        </div>
                      </div>

                      <!-- Pin Button -->
                      <div class="device-column pin-column">
                        <div
                          class="pin-button"
                          :class="{ pinned: isDevicePinned(device.deviceid) }"
                          @click.stop="togglePinDevice(device)"
                          :title="
                            isDevicePinned(device.deviceid)
                              ? 'Unpin device'
                              : 'Pin device'
                          "
                        >
                          <!-- Pin Icon -->
                          <svg
                            v-if="isDevicePinned(device.deviceid)"
                            xmlns="http://www.w3.org/2000/svg"
                            height="16px"
                            viewBox="0 -960 960 960"
                            width="16px"
                            fill="#ff6b30"
                          >
                            <path
                              d="m640-480 80 80v80H520v240l-40 40-40-40v-240H240v-80l80-80v-280h-40v-80h400v80h-40v280Zm-286 80h252l-46-46v-314H400v314l-46 46Zm126 0Z"
                            />
                          </svg>

                          <!-- Unpin Icon -->
                          <svg
                            v-else
                            xmlns="http://www.w3.org/2000/svg"
                            height="15px"
                            viewBox="0 -960 960 960"
                            width="15px"
                            fill="rgba(255, 255, 255, 0.6)"
                          >
                            <path
                              d="M680-840v80h-40v327l-80-80v-247H400v87l-87-87-33-33v-47h400ZM480-40l-40-40v-240H240v-80l80-80v-46L56-792l56-56 736 736-58 56-264-264h-6v240l-40 40ZM354-400h92l-44-44-2-2-46 46Zm126-193Zm-78 149Z"
                            />
                          </svg>
                        </div>
                      </div>
                    </div>

                    <!-- Status Icons (Collapsed View) -->
                    <div
                      v-if="expandedDeviceId !== device.deviceid"
                      class="device-status-icons"
                    >
                      <!-- Battery Section -->
                      <div class="battery-left-container">
                        <div
                          class="battery-with-text"
                          :class="getBatteryStatus(device.position.batt_v)"
                        >
                          <div class="battery-icon">
                            <!-- Critical Battery Icon -->
                            <template
                              v-if="
                                getBatteryStatus(device.position.batt_v) ===
                                'status-critical'
                              "
                            >
                              <svg
                                xmlns="http://www.w3.org/2000/svg"
                                height="20px"
                                viewBox="0 -960 960 960"
                                width="20px"
                                fill="#1f1f1f"
                              >
                                <path
                                  d="M320-80q-17 0-28.5-11.5T280-120v-640q0-17 11.5-28.5T320-800h80v-80h160v80h80q17 0 28.5 11.5T680-760v640q0 17-11.5 28.5T640-80H320Zm40-160h240v-480H360v480Z"
                                />
                              </svg>
                            </template>

                            <!-- Warning Battery Icon -->
                            <template
                              v-else-if="
                                getBatteryStatus(device.position.batt_v) ===
                                'status-warning'
                              "
                            >
                              <svg
                                xmlns="http://www.w3.org/2000/svg"
                                height="20px"
                                viewBox="0 -960 960 960"
                                width="20px"
                                fill="#1f1f1f"
                              >
                                <path
                                  d="M320-80q-17 0-28.5-11.5T280-120v-640q0-17 11.5-28.5T320-800h80v-80h160v80h80q17 0 28.5 11.5T680-760v640q0 17-11.5 28.5T640-80H320Zm40-320h240v-320H360v320Z"
                                />
                              </svg>
                            </template>

                            <!-- Good Battery Icon -->
                            <template v-else>
                              <svg
                                xmlns="http://www.w3.org/2000/svg"
                                height="17px"
                                viewBox="0 -960 960 960"
                                width="17px"
                                fill="#1f1f1f"
                              >
                                <path
                                  d="M320-80q-17 0-28.5-11.5T280-120v-640q0-17 11.5-28.5T320-800h80v-80h160v80h80q17 0 28.5 11.5T680-760v640q0 17-11.5 28.5T640-80H320Zm40-560h240v-80H360v80Z"
                                />
                              </svg>
                            </template>
                          </div>
                          <span class="battery-text">{{ device.position.batt_v }}</span>
                        </div>
                      </div>

                      <!-- Other Status Icons -->
                      <div class="status-icons-right">
                        <!-- Lock Status -->

                        <div
                          v-if="
                            getLockStatus(device.position.flags) === 'status-critical' ||
                            getLockStatus(device.position.flags) === 'status-good'
                          "
                          class="status-icon"
                          :class="getLockStatus(device.position.flags)"
                        >
                          <!-- Locked Icon (قفل شده) -->
                          <svg
                            v-if="device.position.flags.isCoilActive"
                            xmlns="http://www.w3.org/2000/svg"
                            height="17px"
                            viewBox="0 -960 960 960"
                            width="17px"
                            fill="currentColor"
                          >
                            <path
                              d="M240-80q-33 0-56.5-23.5T160-160v-400q0-33 23.5-56.5T240-640h40v-80q0-83 58.5-141.5T480-920q83 0 141.5 58.5T680-720v80h40q33 0 56.5 23.5T800-560v400q0 33-23.5 56.5T720-80H240Zm0-80h480v-400H240v400Zm240-120q33 0 56.5-23.5T560-360q0-33-23.5-56.5T480-440q-33 0-56.5 23.5T400-360q0 33 23.5 56.5T480-280ZM360-640h240v-80q0-50-35-85t-85-35q-50 0-85 35t-35 85v80Zm120 280Z"
                            />
                          </svg>

                          <!-- Unlocked Icon (قفل باز) -->
                          <svg
                            v-else
                            xmlns="http://www.w3.org/2000/svg"
                            height="17px"
                            viewBox="0 -960 960 960"
                            width="17px"
                            fill="currentColor"
                          >
                            <path
                              d="M240-160h480v-400H240v400Zm240-120q33 0 56.5-23.5T560-360q0-33-23.5-56.5T480-440q-33 0-56.5 23.5T400-360q0 33 23.5 56.5T480-280ZM240-160v-400 400Zm0 80q-33 0-56.5-23.5T160-160v-400q0-33 23.5-56.5T240-640h280v-80q0-83 58.5-141.5T720-920q83 0 141.5 58.5T920-720h-80q0-50-35-85t-85-35q-50 0-85 35t-35 85v80h120q33 0 56.5 23.5T800-560v400q0 33-23.5 56.5T720-80H240Z"
                            />
                          </svg>

                          <span class="status-tooltip">{{
                            getLockText(device.position.flags)
                          }}</span>
                        </div>

                        <!-- GSM Status -->
                        <div
                          v-if="
                            getGSMStatus(device.position.gsm_sig) === 'status-warning' ||
                            getGSMStatus(device.position.gsm_sig) === 'status-critical'
                          "
                          class="status-icon"
                          :class="getGSMStatus(device.position.gsm_sig)"
                        >
                          <template v-if="device.position.gsm_sig >= 70">
                            <svg
                              xmlns="http://www.w3.org/2000/svg"
                              height="17px"
                              viewBox="0 -960 960 960"
                              width="17px"
                              fill="currentColor"
                            >
                              <path
                                d="M200-160v-240h120v240H200Zm240 0v-440h120v440H440Zm240 0v-640h120v640H680Z"
                              />
                            </svg>
                          </template>
                          <template
                            v-else-if="
                              device.position.gsm_sig >= 40 &&
                              device.position.gsm_sig < 70
                            "
                          >
                            <svg
                              xmlns="http://www.w3.org/2000/svg"
                              height="17px"
                              viewBox="0 -960 960 960"
                              width="17px"
                              fill="currentColor"
                            >
                              <path
                                d="M60-160v-320h120v320H60Zm240 0v-420h120v420H300Zm240 0v-520h120v520H540Zm240 0v-640h120v640H780Z"
                              />
                            </svg>
                          </template>
                          <template v-else>
                            <svg
                              xmlns="http://www.w3.org/2000/svg"
                              height="17px"
                              viewBox="0 -960 960 960"
                              width="17px"
                              fill="currentColor"
                            >
                              <path
                                d="M200-160v-240h120v240H200Zm240 0v-440h120v440H440Zm240 0v-640h120v640H680Z"
                              />
                            </svg>
                          </template>
                          <span class="status-tooltip">{{
                            formatGSM(device.position.gsm_sig)
                          }}</span>
                        </div>

                        <!-- Speed Status -->
                        <div
                          v-if="
                            getSpeedStatus(device.position.speed) === 'status-warning' ||
                            getSpeedStatus(device.position.speed) === 'status-critical'
                          "
                          class="status-icon"
                          :class="getSpeedStatus(device.position.speed)"
                        >
                          <svg
                            xmlns="http://www.w3.org/2000/svg"
                            height="17px"
                            viewBox="0 -960 960 960"
                            width="17px"
                            fill="currentColor"
                          >
                            <path
                              d="M418-340q24 24 62 23.5t56-27.5l224-336-336 224q-27 18-28.5 55t22.5 61Zm62-460q59 0 113.5 16.5T696-734l-76 48q-33-17-68.5-25.5T480-720q-133 0-226.5 93.5T160-400q0 42 11.5 83t32.5 77h552q23-38 33.5-79t10.5-85q0-36-8.5-70T766-540l48-76q30 47 47.5 100T880-406q1 57-13 109t-41 99q-11 18-30 28t-40 10H204q-21 0-40-10t-30-28q-26-45-40-95.5T80-400q0-83 31.5-155.5t86-127Q252-737 325-768.5T480-800Zm7 313Z"
                            />
                          </svg>
                          <span class="status-tooltip"
                            >{{ device.position.speed }} km/h</span
                          >
                        </div>

                        <!-- Rope Status -->
                        <div
                          v-if="
                            getRopeStatus(device.position.flags) === 'status-critical'
                          "
                          class="status-icon"
                          :class="getRopeStatus(device.position.flags)"
                        >
                          <svg
                            xmlns="http://www.w3.org/2000/svg"
                            height="17px"
                            viewBox="0 -960 960 960"
                            width="17px"
                            fill="#1f1f1f"
                          >
                            <path
                              d="M200-120q-17 0-28.5-11.5T160-160v-40h-40v-160q0-17 11.5-28.5T160-400h40v-280q0-66 47-113t113-47q66 0 113 47t47 113v400q0 33 23.5 56.5T600-200q33 0 56.5-23.5T680-280v-280h-40q-17 0-28.5-11.5T600-600v-160h40v-40q0-17 11.5-28.5T680-840h80q17 0 28.5 11.5T800-800v40h40v160q0 17-11.5 28.5T800-560h-40v280q0 66-47 113t-113 47q-66 0-113-47t-47-113v-400q0-33-23.5-56.5T360-760q-33 0-56.5 23.5T280-680v280h40q17 0 28.5 11.5T360-360v160h-40v40q0 17-11.5 28.5T280-120h-80Z"
                            />
                          </svg>
                          <span class="status-tooltip">{{
                            getRopeText(device.position.flags)
                          }}</span>
                        </div>

                        <!-- GNSS Status -->
                        <div
                          v-if="
                            getFixedStatus(device.position.pdop) === 'status-warning' ||
                            getFixedStatus(device.position.pdop) === 'status-critical'
                          "
                          class="status-icon"
                          :class="getFixedStatus(device.position.pdop)"
                        >
                          <svg
                            xmlns="http://www.w3.org/2000/svg"
                            height="17px"
                            viewBox="0 -960 960 960"
                            width="17px"
                            fill="#1f1f1f"
                          >
                            <path
                              d="M440-42v-80q-125-14-214.5-103.5T122-440H42v-80h80q14-125 103.5-214.5T440-838v-80h80v80q125 14 214.5 103.5T838-520h80v80h-80q-14 125-103.5 214.5T520-122v80h-80Zm40-158q116 0 198-82t82-198q0-116-82-198t-198-82q-116 0-198 82t-82 198q0 116 82 198t198 82Zm0-120q-66 0-113-47t-47-113q0-66 47-113t113-47q66 0 113 47t47 113q0 66-47 113t-113 47Zm0-80q33 0 56.5-23.5T560-480q0-33-23.5-56.5T480-560q-33 0-56.5 23.5T400-480q0 33 23.5 56.5T480-400Zm0-80Z"
                            />
                          </svg>
                          <span class="status-tooltip">{{
                            formatGNSS(device.position.pdop)
                          }}</span>
                        </div>

                        <!-- Satelite Status -->
                        <div
                          v-if="
                            getFixedStatus(device.position.satellites) ===
                              'status-warning' ||
                            getFixedStatus(device.position.satellites) ===
                              'status-critical'
                          "
                          class="status-icon"
                          :class="getFixedStatus(device.position.satellites)"
                        >
                          <svg
                            xmlns="http://www.w3.org/2000/svg"
                            height="17px"
                            viewBox="0 -960 960 960"
                            width="17px"
                            fill="#1f1f1f"
                          >
                            <path
                              d="M560-32v-80q117 0 198.5-81.5T840-392h80q0 75-28.5 140.5t-77 114q-48.5 48.5-114 77T560-32Zm0-160v-80q50 0 85-35t35-85h80q0 83-58.5 141.5T560-192ZM222-57q-15 0-30-6t-27-17L23-222q-11-12-17-27t-6-30q0-16 6-30.5T23-335l127-127q23-23 57-23.5t57 22.5l50 50 28-28-50-50q-23-23-23-56t23-56l57-57q23-23 56.5-23t56.5 23l50 50 28-28-50-50q-23-23-23-56.5t23-56.5l127-127q12-12 27-18t30-6q15 0 29.5 6t26.5 18l142 142q12 11 17.5 25.5T895-730q0 15-5.5 30T872-673L745-546q-23 23-56.5 23T632-546l-50-50-28 28 50 50q23 23 22.5 56.5T603-405l-56 56q-23 23-56.5 23T434-349l-50-50-28 28 50 50q23 23 22.5 57T405-207L278-80q-11 11-25.5 17T222-57Zm0-79 42-42-142-142-42 42 142 142Zm85-85 42-42-142-142-42 42 142 142Zm184-184 56-56-142-142-56 56 142 142Zm198-198 42-42-142-142-42 42 142 142Zm85-85 42-42-142-142-42 42 142 142ZM448-504Z"
                            />
                          </svg>
                          <span class="status-tooltip">{{
                            device.position.satellites
                          }}</span>
                        </div>

                        <!-- Temperature Status -->
                        <div
                          v-if="
                            getTemperatureStatus(device.position.temperature) ===
                              'status-warning' ||
                            getTemperatureStatus(device.position.temperature) ===
                              'status-critical'
                          "
                          class="status-icon"
                          :class="getTemperatureStatus(device.position.temperature)"
                        >
                          <svg
                            xmlns="http://www.w3.org/2000/svg"
                            height="17px"
                            viewBox="0 -960 960 960"
                            width="17px"
                            fill="#1f1f1f"
                          >
                            <path
                              d="M520-520v-80h200v80H520Zm0-160v-80h320v80H520ZM320-120q-83 0-141.5-58.5T120-320q0-48 21-89.5t59-70.5v-240q0-50 35-85t85-35q50 0 85 35t35 85v240q38 29 59 70.5t21 89.5q0 83-58.5 141.5T320-120ZM200-320h240q0-29-12.5-54T392-416l-32-24v-280q0-17-11.5-28.5T320-760q-17 0-28.5 11.5T280-720v280l-32 24q-23 17-35.5 42T200-320Z"
                            />
                          </svg>
                          <span class="status-tooltip"
                            >{{ device.position.temperature }}°C</span
                          >
                        </div>

                        <!-- Humidity Status -->
                        <div
                          v-if="
                            getHumidityStatus(device.position.humidity) ===
                              'status-warning' ||
                            getHumidityStatus(device.position.humidity) ===
                              'status-critical'
                          "
                          class="status-icon"
                          :class="getHumidityStatus(device.position.humidity)"
                        >
                          <svg
                            xmlns="http://www.w3.org/2000/svg"
                            height="17px"
                            viewBox="0 -960 960 960"
                            width="17px"
                            fill="#1f1f1f"
                          >
                            <path
                              d="M480-100q-133 0-226.5-92T160-416q0-63 24.5-120.5T254-638l226-222 226 222q45 44 69.5 101.5T800-416q0 132-93.5 224T480-100ZM240-416h480q0-47-18-89.5T650-580L480-748 310-580q-34 32-52 74.5T240-416Z"
                            />
                          </svg>
                          <span class="status-tooltip"
                            >{{ device.position.humidity }}%</span
                          >
                        </div>
                      </div>
                    </div>

                    <!-- Device Details Expanded View -->
                    <div
                      v-if="expandedDeviceId === device.deviceid"
                      class="device-details-expanded"
                    >
                      <!-- Row 1: Battery, Lock, GSM -->
                      <div class="details-row">
                        <!-- Battery -->
                        <div
                          class="detail-item"
                          :class="getBatteryStatus(device.position.batt_v)"
                        >
                          <div class="detail-icon">
                            <template
                              v-if="
                                getBatteryStatus(device.position.batt_v) ===
                                'status-critical'
                              "
                            >
                              <svg
                                xmlns="http://www.w3.org/2000/svg"
                                height="17px"
                                viewBox="0 -960 960 960"
                                width="17px"
                                fill="currentColor"
                              >
                                <path
                                  d="M320-80q-17 0-28.5-11.5T280-120v-640q0-17 11.5-28.5T320-800h80v-80h160v80h80q17 0 28.5 11.5T680-760v640q0 17-11.5 28.5T640-80H320Zm40-160h240v-480H360v480Z"
                                />
                              </svg>
                            </template>
                            <template
                              v-else-if="
                                getBatteryStatus(device.position.batt_v) ===
                                'status-warning'
                              "
                            >
                              <svg
                                xmlns="http://www.w3.org/2000/svg"
                                height="17px"
                                viewBox="0 -960 960 960"
                                width="17px"
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
                                height="17px"
                                viewBox="0 -960 960 960"
                                width="17px"
                                fill="#1f1f1f"
                              >
                                <path
                                  d="M320-80q-17 0-28.5-11.5T280-120v-640q0-17 11.5-28.5T320-800h80v-80h160v80h80q17 0 28.5 11.5T680-760v640q0 17-11.5 28.5T640-80H320Zm40-560h240v-80H360v80Z"
                                />
                              </svg>
                            </template>
                          </div>
                          <div class="detail-text">{{ device.position.batt_v }}</div>
                        </div>

                        <!-- Lock -->
                        <!-- Lock -->
                        <div
                          class="detail-item"
                          :class="getLockStatus(device.position.flags)"
                        >
                          <div class="detail-icon">
                            <!-- Locked Icon (قفل شده) -->
                            <svg
                              v-if="device.position.flags.isCoilActive"
                              xmlns="http://www.w3.org/2000/svg"
                              height="17px"
                              viewBox="0 -960 960 960"
                              width="17px"
                              fill="currentColor"
                            >
                              <path
                                d="M240-80q-33 0-56.5-23.5T160-160v-400q0-33 23.5-56.5T240-640h40v-80q0-83 58.5-141.5T480-920q83 0 141.5 58.5T680-720v80h40q33 0 56.5 23.5T800-560v400q0 33-23.5 56.5T720-80H240Zm0-80h480v-400H240v400Zm240-120q33 0 56.5-23.5T560-360q0-33-23.5-56.5T480-440q-33 0-56.5 23.5T400-360q0 33 23.5 56.5T480-280ZM360-640h240v-80q0-50-35-85t-85-35q-50 0-85 35t-35 85v80Zm120 280Z"
                              />
                            </svg>

                            <!-- Unlocked Icon (قفل باز) -->
                            <svg
                              v-else
                              xmlns="http://www.w3.org/2000/svg"
                              height="17px"
                              viewBox="0 -960 960 960"
                              width="17px"
                              fill="currentColor"
                            >
                              <path
                                d="M240-160h480v-400H240v400Zm240-120q33 0 56.5-23.5T560-360q0-33-23.5-56.5T480-440q-33 0-56.5 23.5T400-360q0 33 23.5 56.5T480-280ZM240-160v-400 400Zm0 80q-33 0-56.5-23.5T160-160v-400q0-33 23.5-56.5T240-640h280v-80q0-83 58.5-141.5T720-920q83 0 141.5 58.5T920-720h-80q0-50-35-85t-85-35q-50 0-85 35t-35 85v80h120q33 0 56.5 23.5T800-560v400q0 33-23.5 56.5T720-80H240Z"
                              />
                            </svg>
                          </div>
                          <div class="detail-text">
                            {{ getLockText(device.position.flags) }}
                          </div>
                        </div>

                        <!-- GSM -->
                        <!-- GSM -->
                        <div
                          class="detail-item"
                          :class="getGSMStatus(device.position.gsm_sig)"
                        >
                          <div class="detail-icon">
                            <!-- بیشتر از 70: 5 خط -->
                            <svg
                              v-if="device.position.gsm_sig >= 70"
                              xmlns="http://www.w3.org/2000/svg"
                              height="17px"
                              viewBox="0 -960 960 960"
                              width="17px"
                              fill="currentColor"
                            >
                              <path
                                d="M40-160v-240h120v240H40Zm190 0v-320h120v320H230Zm190 0v-440h120v440H420Zm190 0v-520h120v520H610Zm190 0v-640h120v640H800Z"
                              />
                            </svg>

                            <!-- بین 40 تا 70: 4 خط -->
                            <svg
                              v-else-if="
                                device.position.gsm_sig >= 40 &&
                                device.position.gsm_sig < 70
                              "
                              xmlns="http://www.w3.org/2000/svg"
                              height="17px"
                              viewBox="0 -960 960 960"
                              width="17px"
                              fill="currentColor"
                            >
                              <path
                                d="M120-160v-320h120v320H120Zm190 0v-420h120v420H310Zm190 0v-520h120v520H500Zm190 0v-640h120v640H690Z"
                              />
                            </svg>

                            <!-- کمتر از 40: 3 خط -->
                            <svg
                              v-else
                              xmlns="http://www.w3.org/2000/svg"
                              height="17px"
                              viewBox="0 -960 960 960"
                              width="17px"
                              fill="currentColor"
                            >
                              <path
                                d="M200-160v-240h120v240H200Zm240 0v-440h120v440H440Zm240 0v-640h120v640H680Z"
                              />
                            </svg>
                          </div>
                          <div class="detail-text">
                            {{ formatGSM(device.position.gsm_sig) }}
                          </div>
                        </div>
                      </div>

                      <!-- Row 2: Speed, Rope, GNSS -->
                      <div class="details-row">
                        <!-- Speed -->
                        <div
                          class="detail-item"
                          :class="getSpeedStatus(device.position.speed)"
                        >
                          <div class="detail-icon">
                            <svg
                              xmlns="http://www.w3.org/2000/svg"
                              height="17px"
                              viewBox="0 -960 960 960"
                              width="17px"
                              fill="currentColor"
                            >
                              <path
                                d="M418-340q24 24 62 23.5t56-27.5l224-336-336 224q-27 18-28.5 55t22.5 61Zm62-460q59 0 113.5 16.5T696-734l-76 48q-33-17-68.5-25.5T480-720q-133 0-226.5 93.5T160-400q0 42 11.5 83t32.5 77h552q23-38 33.5-79t10.5-85q0-36-8.5-70T766-540l48-76q30 47 47.5 100T880-406q1 57-13 109t-41 99q-11 18-30 28t-40 10H204q-21 0-40-10t-30-28q-26-45-40-95.5T80-400q0-83 31.5-155.5t86-127Q252-737 325-768.5T480-800Zm7 313Z"
                              />
                            </svg>
                          </div>
                          <div class="detail-text">{{ device.position.speed }} km/h</div>
                        </div>

                        <!-- Rope -->
                        <div
                          class="detail-item"
                          :class="getRopeStatus(device.position.flags)"
                        >
                          <div class="detail-icon">
                            <svg
                              xmlns="http://www.w3.org/2000/svg"
                              height="17px"
                              viewBox="0 -960 960 960"
                              width="17px"
                              fill="#1f1f1f"
                            >
                              <path
                                d="M200-120q-17 0-28.5-11.5T160-160v-40h-40v-160q0-17 11.5-28.5T160-400h40v-280q0-66 47-113t113-47q66 0 113 47t47 113v400q0 33 23.5 56.5T600-200q33 0 56.5-23.5T680-280v-280h-40q-17 0-28.5-11.5T600-600v-160h40v-40q0-17 11.5-28.5T680-840h80q17 0 28.5 11.5T800-800v40h40v160q0 17-11.5 28.5T800-560h-40v280q0 66-47 113t-113 47q-66 0-113-47t-47-113v-400q0-33-23.5-56.5T360-760q-33 0-56.5 23.5T280-680v280h40q17 0 28.5 11.5T360-360v160h-40v40q0 17-11.5 28.5T280-120h-80Z"
                              />
                            </svg>
                          </div>
                          <div class="detail-text">
                            {{ getRopeText(device.position.flags) }}
                          </div>
                        </div>

                        <!-- GNSS -->
                        <div
                          class="detail-item"
                          :class="getFixedStatus(device.position.pdop)"
                        >
                          <div class="detail-icon">
                            <svg
                              xmlns="http://www.w3.org/2000/svg"
                              height="17px"
                              viewBox="0 -960 960 960"
                              width="17px"
                              fill="#1f1f1f"
                            >
                              <path
                                d="M440-42v-80q-125-14-214.5-103.5T122-440H42v-80h80q14-125 103.5-214.5T440-838v-80h80v80q125 14 214.5 103.5T838-520h80v80h-80q-14 125-103.5 214.5T520-122v80h-80Zm40-158q116 0 198-82t82-198q0-116-82-198t-198-82q-116 0-198 82t-82 198q0 116 82 198t198 82Zm0-120q-66 0-113-47t-47-113q0-66 47-113t113-47q66 0 113 47t47 113q0 66-47 113t-113 47Zm0-80q33 0 56.5-23.5T560-480q0-33-23.5-56.5T480-560q-33 0-56.5 23.5T400-480q0 33 23.5 56.5T480-400Zm0-80Z"
                              />
                            </svg>
                          </div>
                          <div class="detail-text">
                            {{ formatGNSS(device.position.pdop) }}
                          </div>
                        </div>
                      </div>

                      <!-- Row 3: Temperature, Satellite, Humidity -->
                      <div class="details-row">
                        <!-- Temperature -->
                        <div
                          class="detail-item"
                          :class="getTemperatureStatus(device.position.temperature)"
                        >
                          <div class="detail-icon">
                            <svg
                              xmlns="http://www.w3.org/2000/svg"
                              height="17px"
                              viewBox="0 -960 960 960"
                              width="17px"
                              fill="#1f1f1f"
                            >
                              <path
                                d="M520-520v-80h200v80H520Zm0-160v-80h320v80H520ZM320-120q-83 0-141.5-58.5T120-320q0-48 21-89.5t59-70.5v-240q0-50 35-85t85-35q50 0 85 35t35 85v240q38 29 59 70.5t21 89.5q0 83-58.5 141.5T320-120ZM200-320h240q0-29-12.5-54T392-416l-32-24v-280q0-17-11.5-28.5T320-760q-17 0-28.5 11.5T280-720v280l-32 24q-23 17-35.5 42T200-320Z"
                              />
                            </svg>
                          </div>
                          <div class="detail-text">
                            {{ device.position.temperature }}°C
                          </div>
                        </div>

                        <!-- Satellite -->
                        <div
                          class="detail-item"
                          :class="getFixedStatus(device.position.satellites)"
                        >
                          <div class="detail-icon">
                            <svg
                              xmlns="http://www.w3.org/2000/svg"
                              height="17px"
                              viewBox="0 -960 960 960"
                              width="17px"
                              fill="#1f1f1f"
                            >
                              <path
                                d="M560-32v-80q117 0 198.5-81.5T840-392h80q0 75-28.5 140.5t-77 114q-48.5 48.5-114 77T560-32Zm0-160v-80q50 0 85-35t35-85h80q0 83-58.5 141.5T560-192ZM222-57q-15 0-30-6t-27-17L23-222q-11-12-17-27t-6-30q0-16 6-30.5T23-335l127-127q23-23 57-23.5t57 22.5l50 50 28-28-50-50q-23-23-23-56t23-56l57-57q23-23 56.5-23t56.5 23l50 50 28-28-50-50q-23-23-23-56.5t23-56.5l127-127q12-12 27-18t30-6q15 0 29.5 6t26.5 18l142 142q12 11 17.5 25.5T895-730q0 15-5.5 30T872-673L745-546q-23 23-56.5 23T632-546l-50-50-28 28 50 50q23 23 22.5 56.5T603-405l-56 56q-23 23-56.5 23T434-349l-50-50-28 28 50 50q23 23 22.5 57T405-207L278-80q-11 11-25.5 17T222-57Zm0-79 42-42-142-142-42 42 142 142Zm85-85 42-42-142-142-42 42 142 142Zm184-184 56-56-142-142-56 56 142 142Zm198-198 42-42-142-142-42 42 142 142Zm85-85 42-42-142-142-42 42 142 142ZM448-504Z"
                              />
                            </svg>
                          </div>
                          <div class="detail-text">{{ device.position.satellites }}</div>
                        </div>

                        <!-- Humidity -->
                        <div
                          class="detail-item"
                          :class="getHumidityStatus(device.position.humidity)"
                        >
                          <div class="detail-icon">
                            <svg
                              xmlns="http://www.w3.org/2000/svg"
                              height="17px"
                              viewBox="0 -960 960 960"
                              width="17px"
                              fill="#1f1f1f"
                            >
                              <path
                                d="M480-100q-133 0-226.5-92T160-416q0-63 24.5-120.5T254-638l226-222 226 222q45 44 69.5 101.5T800-416q0 132-93.5 224T480-100ZM240-416h480q0-47-18-89.5T650-580L480-748 310-580q-34 32-52 74.5T240-416Z"
                              />
                            </svg>
                          </div>
                          <div class="detail-text">{{ device.position.humidity }}%</div>
                        </div>
                      </div>

                      <!-- Row 4: Network Info -->
                      <!-- Row 4: Network Info -->
                      <div class="details-row">
                        <!-- LAC -->
                        <div
                          class="detail-item"
                          :style="{
                            color: device.position.flags.isCIDValid
                              ? '#4CAF50'
                              : '#F44336',
                          }"
                        >
                          <div class="detail-icon">
                            <svg
                              xmlns="http://www.w3.org/2000/svg"
                              height="17px"
                              viewBox="0 -960 960 960"
                              width="17px"
                              :fill="
                                device.position.flags.isCIDValid ? '#4CAF50' : '#F44336'
                              "
                            >
                              <path
                                d="M200-80q-50 0-85-35t-35-85q0-39 22.5-69.5T160-313v-334q-35-13-57.5-43.5T80-760q0-50 35-85t85-35q39 0 69.5 22.5T313-800h334q12-35 42.5-57.5T760-880q50 0 85 35t35 85q0 40-22.5 70.5T800-647v334q35 13 57.5 43.5T880-200q0 50-35 85t-85 35q-39 0-69.5-22.5T647-160H313q-13 35-43.5 57.5T200-80Zm0-640q17 0 28.5-11.5T240-760q0-17-11.5-28.5T200-800q-17 0-28.5 11.5T160-760q0 17 11.5 28.5T200-720Zm560 0q17 0 28.5-11.5T800-760q0-17-11.5-28.5T760-800q-17 0-28.5 11.5T720-760q0 17 11.5 28.5T760-720ZM313-240h334q9-26 28-45t45-28v-334q-26-9-45-28t-28-45H313q-9 26-28 45t-45 28v334q26 9 45 28t28 45Zm447 80q17 0 28.5-11.5T800-200q0-17-11.5-28.5T760-240q-17 0-28.5 11.5T720-200q0 17 11.5 28.5T760-160Zm-560 0q17 0 28.5-11.5T240-200q0-17-11.5-28.5T200-240q-17 0-28.5 11.5T160-200q0 17 11.5 28.5T200-160Zm0-600Zm560 0Zm0 560Zm-560 0Z"
                              />
                            </svg>
                          </div>
                          <div class="detail-text">LAC: {{ device.position.lac }}</div>
                        </div>

                        <!-- CID -->
                        <div
                          class="detail-item"
                          :style="{
                            color: device.position.flags.isCIDValid
                              ? '#4CAF50'
                              : '#F44336',
                          }"
                        >
                          <div class="detail-icon">
                            <svg
                              xmlns="http://www.w3.org/2000/svg"
                              height="17px"
                              viewBox="0 -960 960 960"
                              width="17px"
                              :fill="
                                device.position.flags.isCIDValid ? '#4CAF50' : '#F44336'
                              "
                            >
                              <path
                                d="M196-276q-57-60-86.5-133T80-560q0-78 29.5-151T196-844l48 48q-48 48-72 110.5T148-560q0 63 24 125.5T244-324l-48 48Zm96-96q-39-39-59.5-88T212-560q0-51 20.5-100t59.5-88l48 48q-30 27-45 64t-15 76q0 36 15 73t45 67l-48 48ZM280-80l135-405q-16-14-25.5-33t-9.5-42q0-42 29-71t71-29q42 0 71 29t29 71q0 23-9.5 42T545-485L680-80h-80l-26-80H387l-27 80h-80Zm133-160h134l-67-200-67 200Zm255-132-48-48q30-27 45-64t15-76q0-36-15-73t-45-67l48-48q39 39 58 88t22 100q0 51-20.5 100T668-372Zm96 96-48-48q48-48 72-110.5T812-560q0-63-24-125.5T716-796l48-48q57 60 86.5 133T880-560q0 78-28 151t-88 133Z"
                              />
                            </svg>
                          </div>
                          <div class="detail-text">CID: {{ device.position.cid }}</div>
                        </div>

                        <!-- Operator -->
                        <div
                          class="detail-item"
                          :style="{
                            color: device.position.flags.isCIDValid
                              ? '#4CAF50'
                              : '#F44336',
                          }"
                        >
                          <div class="detail-icon">
                            <svg
                              v-if="device.position.technology === '3G'"
                              xmlns="http://www.w3.org/2000/svg"
                              height="17px"
                              viewBox="0 -960 960 960"
                              width="17px"
                              :fill="
                                device.position.flags.isCIDValid ? '#4CAF50' : '#F44336'
                              "
                            >
                              <path
                                d="M120-120q-33 0-56.5-23.5T40-200v-560q0-33 23.5-56.5T120-840h720q33 0 56.5 23.5T920-760v560q0 33-23.5 56.5T840-120H120Zm0-80h720v-560H120v560Zm0 0v-560 560Zm640-320H640v80h40v80H560v-240h200q0-33-23.5-56.5T680-680H560q-33 0-56.5 23.5T480-600v240q0 33 23.5 56.5T560-280h120q33 0 56.5-23.5T760-360v-160ZM200-280h160q33 0 56.5-23.5T440-360v-60q0-25-17.5-42.5T380-480q25 0 42.5-17.5T440-540v-60q0-33-23.5-56.5T360-680H200v80h160v80H200v80h160v80H200v80Z"
                              />
                            </svg>
                            <svg
                              v-else-if="device.position.technology === '4G'"
                              xmlns="http://www.w3.org/2000/svg"
                              height="17px"
                              viewBox="0 -960 960 960"
                              width="17px"
                              :fill="
                                device.position.flags.isCIDValid ? '#4CAF50' : '#F44336'
                              "
                            >
                              <path
                                d="M120-120q-33 0-56.5-23.5T40-200v-560q0-33 23.5-56.5T120-840h720q33 0 56.5 23.5T920-760v560q0 33-23.5 56.5T840-120H120Zm0-80h720v-560H120v560Zm0 0v-560 560Zm680-320H660v80h60v80H600v-240h200q0-33-23.5-56.5T720-680H600q-33 0-56.5 23.5T520-600v240q0 33 23.5 56.5T600-280h120q33 0 56.5-23.5T800-360v-160ZM320-280h80v-120h80v-80h-80v-200h-80v200h-80v-200h-80v280h160v120Z"
                              />
                            </svg>
                            <svg
                              v-else
                              xmlns="http://www.w3.org/2000/svg"
                              height="17px"
                              viewBox="0 -960 960 960"
                              width="17px"
                              :fill="
                                device.position.flags.isCIDValid ? '#4CAF50' : '#F44336'
                              "
                            >
                              <path
                                d="M200-120q-33 0-56.5-23.5T120-200v-560q0-33 23.5-56.5T200-840h560q33 0 56.5 23.5T840-760v560q0 33-23.5 56.5T760-120H200Zm0-80h560v-560H200v560Zm0-560v560-560Zm120 480h320v-80H400v-80h200v-80H400v-80h240v-80H320v400Z"
                              />
                            </svg>
                          </div>
                          <div class="detail-text">{{ formatType(device.type) }}</div>
                        </div>
                      </div>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </q-card>
        </div>
      </div>

      <!-- Other Panels -->
      <q-card
        v-else-if="activePanel && activePanel !== 'home' && sidebarOpen"
        :key="activePanel"
        class="sliding-panel"
        :class="[`${activePanel}-panel`]"
        elevated
      >
        <div class="panel-content">
          <div class="panel-header">
            <h3>{{ getPanelTitle(activePanel) }}</h3>
          </div>
          <div class="panel-body">
            <div v-if="activePanel === 'geofence'">Geofence Content</div>
            <div v-else-if="activePanel === 'rfid'">RFID Content</div>
            <div v-else-if="activePanel === 'alarms'">Alarms Content</div>
            <div v-else-if="activePanel === 'settings'">Settings Content</div>
          </div>
        </div>
      </q-card>
    </transition-group>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from "vue";

// Props
const props = defineProps({
  devices: {
    type: Array,
    default: () => [],
  },
});

// Emits
const emit = defineEmits(["device-selected"]);

// Reactive Variables
const activePanel = ref("home");
const sidebarOpen = ref(false);
const isHovering = ref(false);
const homeExpanded = ref(false);
const expandedDeviceId = ref(null);
const pinnedDevices = ref([]);

// Menu Items
const menuItems = ref([
  { id: "home", title: "Home" },
  { id: "geofence", title: "Geofence" },
  { id: "rfid", title: "RFID" },
  { id: "alarms", title: "Alarms" },
  { id: "settings", title: "Settings" },
]);

// Load Pinned Devices from LocalStorage
onMounted(() => {
  const savedPinned = localStorage.getItem("pinnedDevices");
  if (savedPinned) {
    pinnedDevices.value = JSON.parse(savedPinned);
  }
});

const formatGSM = (gsm_sig) => {
  return `${gsm_sig}%`;
};

const formatGNSS = (pdop) => {
  return `${pdop}`;
};
const formatType = (type) => {
  if (type == 1) return "MCI";
};

const getLockText = (flags) => {
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
const getDeviceStatus = (device) => {
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

const getBatteryStatus = (batt_v) => {
  if (!batt_v) return "status-critical";
  const voltage = batt_v;
  if (voltage >= 3400) return "status-good";
  if (voltage >= 3200) return "status-warning";
  return "status-critical";
};

const getLockStatus = (flags) => {
  return flags.isCoilActive ? "status-critical" : "status-good";
};

const getGSMStatus = (gsm_sig) => {
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
  if (flags.isRopeClosed && flags.isMechanicClosed) {
    return "status-good";
  } else if (!flags.isRopeClosed && !flags.isMechanicClosed) {
    return "status-critical";
  } else {
    return "status-warning";
  }
};

const getTemperatureStatus = (temperature) => {
  if (temperature >= 15 && temperature <= 30) return "status-good";
  if ((temperature >= 10 && temperature < 15) || (temperature > 30 && temperature <= 35))
    return "status-warning";
  return "status-critical";
};

const getHumidityStatus = (humidity) => {
  if (humidity >= 30 && humidity <= 60) return "status-good";
  if ((humidity >= 20 && humidity < 30) || (humidity > 60 && humidity <= 70))
    return "status-warning";
  return "status-critical";
};

const getFixedStatus = (flags) => {
  return flags.isFixed ? "status-good" : "status-critical";
};

/*const getCIDValidStatus = (flags) => {
  return flags.isCIDValid ? "status-good" : "status-critical";
};*/

// Computed Properties
const totalDevices = computed(() => props.devices.length);

const movingDevices = computed(
  () =>
    props.devices.filter((device) => getDeviceStatus(device) === "online-moving").length
);

const stoppedDevices = computed(
  () =>
    props.devices.filter((device) => getDeviceStatus(device) === "online-stopped").length
);

const offlineDevices = computed(
  () => props.devices.filter((device) => getDeviceStatus(device) === "offline").length
);

const sortedDevices = computed(() => {
  const devices = [...props.devices];

  // Separate pinned and unpinned devices
  const pinned = devices.filter((device) => isDevicePinned(device.deviceid));
  const unpinned = devices.filter((device) => !isDevicePinned(device.deviceid));

  // Sort pinned devices by status
  pinned.sort((a, b) => {
    const statusOrder = {
      "online-moving": 1,
      "online-stopped": 2,
      offline: 3,
    };
    return statusOrder[getDeviceStatus(a)] - statusOrder[getDeviceStatus(b)];
  });

  // Sort unpinned devices by status
  unpinned.sort((a, b) => {
    const statusOrder = {
      "online-moving": 1,
      "online-stopped": 2,
      offline: 3,
    };
    return statusOrder[getDeviceStatus(a)] - statusOrder[getDeviceStatus(b)];
  });

  return [...pinned, ...unpinned];
});

// Pin Device Functions
const togglePinDevice = (device) => {
  const deviceId = device.deviceid;

  if (isDevicePinned(deviceId)) {
    pinnedDevices.value = pinnedDevices.value.filter((id) => id !== deviceId);
  } else {
    pinnedDevices.value = [...pinnedDevices.value, deviceId];
  }

  // Save to localStorage
  localStorage.setItem("pinnedDevices", JSON.stringify(pinnedDevices.value));
};

const isDevicePinned = (deviceId) => {
  return pinnedDevices.value.includes(deviceId);
};

//
// // Device Details Functions
// const toggleDeviceDetails = (device) => {
//   const el = event.currentTarget;
//   if (expandedDeviceId.value === device.deviceid) {
//     // expandedDeviceId.value = null;
//     el.classList.remove("black-bg");
//     emit("device-selected", null);
//   } else {
//     // expandedDeviceId.value = device.deviceid;
//     emit("device-selected", device);
//     el.classList.add("black-bg");
//   }

//   // if (el.classList.contains("black-bg")) {
//   // } else {
//   // }
// };

let previousSelectedElement = null;

const toggleDeviceDetails = (device, event) => {
  const el = event.currentTarget;

  // اگر روی همون آیتم قبلی کلیک شد
  if (el === previousSelectedElement) {
    el.classList.remove("black-bg");
    previousSelectedElement = null;
    emit("device-selected", null);
    return;
  }

  // اگر آیتم قبلی وجود داره، کلاسش رو حذف کن
  if (previousSelectedElement) {
    previousSelectedElement.classList.remove("black-bg");
  }

  // آیتم جدید رو اضافه کن
  el.classList.add("black-bg");
  previousSelectedElement = el;
  emit("device-selected", device);
};
// const toggleBlack = (event) => {
// };

// Sidebar Functions
const toggleSidebar = () => {
  sidebarOpen.value = !sidebarOpen.value;
  expandedDeviceId.value = null;

  if (!sidebarOpen.value) {
    isHovering.value = false;
    homeExpanded.value = false;
  } else {
    if (activePanel.value === "home") {
      homeExpanded.value = true;
    }
  }
};

const toggleHomePanel = () => {
  homeExpanded.value = !homeExpanded.value;
};

const activatePanel = (panelId) => {
  if (activePanel.value !== panelId) {
    if (activePanel.value === "home") {
      homeExpanded.value = false;
    }
    setTimeout(() => {
      activePanel.value = panelId;
      expandedDeviceId.value = null;

      if (panelId === "home") {
        homeExpanded.value = sidebarOpen.value;
      } else {
        homeExpanded.value = false;
      }
    }, 50);
  }
};

const handleIconHover = () => {
  if (sidebarOpen.value) {
    isHovering.value = true;
  }
};

const handleSidebarLeave = () => {
  isHovering.value = false;
};

const getPanelTitle = (panelId) => {
  const item = menuItems.value.find((item) => item.id === panelId);
  return item ? item.title : "";
};
</script>

<style scoped>
/* Sidebar Container */
.sidebar-container {
  position: absolute;
  top: 10px;
  left: 10px;
  height: calc(100% - 20px);
  z-index: 1000;
  display: flex;
  align-items: flex-start;
  gap: 0;
}

/* Panels Container with Transition */
.panels-container {
  position: relative;
  display: flex;
  margin-left: 10px;
  height: 100%;
}

.panel-transition-enter-active {
  transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
}

.panel-transition-leave-active {
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  position: absolute !important;
  left: 0;
  top: 0;
  width: 100%;
}

.panel-transition-enter-from {
  opacity: 0;
  transform: translateX(-30px);
}

.panel-transition-leave-to {
  opacity: 0;
  transform: translateX(-30px);
}

/* Sidebar Styles */
.icon-sidebar {
  width: 65px;
  min-width: 65px;
  height: 70px;
  background: linear-gradient(135deg, #020407 0%, #000000 100%);
  backdrop-filter: blur(20px);
  -webkit-backdrop-filter: blur(20px);
  border-radius: 5px;
  border-right: 1px solid rgba(255, 255, 255, 0.1);
  display: flex;
  flex-direction: column;
  padding: 16px 0;
  box-shadow: 4px 0 20px rgba(0, 0, 0, 0.3), inset -1px 0 0 rgba(255, 255, 255, 0.1);
  overflow: hidden;
  transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
  flex-shrink: 0;
  box-sizing: border-box;
}

.icon-sidebar.sidebar-open {
  height: 100%;
  width: 65px;
  transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
}

.icon-sidebar.sidebar-expanded {
  width: 150px;
}

.sidebar-header {
  display: flex;
  align-items: center;
  justify-content: flex-start;
  gap: 12px;
  margin-bottom: 24px;
  padding: 0 16px;
  min-height: 40px;
  width: 100%;
  box-sizing: border-box;
  flex-shrink: 0;
}

.sidebar-title {
  color: #f8fafc;
  font-weight: 600;
  font-size: 16px;
  white-space: nowrap;
  opacity: 0;
  transition: opacity 0.3s cubic-bezier(0.4, 0, 0.2, 1);
}

.icon-sidebar.sidebar-expanded .sidebar-title {
  opacity: 1;
}

.menu-btn {
  color: #f8fafc;
  transition: background-color 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  z-index: 10;
  flex-shrink: 0;
}

.menu-btn:hover {
  background: rgba(255, 255, 255, 0.15);
}

.icon-items {
  display: flex;
  flex-direction: column;
  gap: 8px;
  flex: 1;
  width: 100%;
  align-items: center;
  justify-content: flex-start;
  padding-top: 20px;
  box-sizing: border-box;
}

.icon-item-wrapper {
  width: 100%;
  height: 50px;
  display: flex;
  align-items: center;
  cursor: pointer;
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  position: relative;
  border-radius: 8px;
  opacity: 0;
  transform: translateX(-20px);
  box-sizing: border-box;
  flex-shrink: 0;
  transition-delay: calc(var(--item-index, 0) * 0.05s);
}

.icon-sidebar.sidebar-open .icon-item-wrapper {
  opacity: 1;
  transform: translateX(0);
}

.icon-item-wrapper:hover .custom-svg-icon svg,
.icon-item-wrapper:hover .item-label {
  color: #0095da !important;
  fill: #0095da !important;
}

.icon-item-wrapper.active .custom-svg-icon svg,
.icon-item-wrapper.active .item-label {
  color: #ff4f00 !important;
  fill: #ff4f00 !important;
}

.icon-item-content {
  display: flex;
  align-items: center;
  justify-content: flex-start;
  width: 100%;
  height: 100%;
  position: relative;
  padding: 0 12px;
  box-sizing: border-box;
}

.icon-container {
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
  width: 40px;
  height: 40px;
}

.icon-wrapper {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 100%;
  height: 100%;
  border-radius: 8px;
  transition: background-color 0.3s cubic-bezier(0.4, 0, 0.2, 1);
}

.custom-svg-icon {
  width: 24px;
  height: 24px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.custom-svg-icon svg {
  width: 100%;
  height: 100%;
  color: rgba(255, 255, 255, 0.8);
  fill: rgba(255, 255, 255, 0.8);
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
}

.item-label {
  color: white;
  font-size: 14px;
  font-weight: 500;
  white-space: nowrap;
  margin-left: 12px;
  opacity: 0;
  width: 0;
  overflow: hidden;
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
}

.icon-sidebar.sidebar-expanded .item-label {
  opacity: 1;
  width: auto;
}

/* Home Panel Styles */
.home-panel-container {
  display: flex;
  flex-direction: column;
  width: 400px;
  min-width: 400px;
  max-width: 400px;
  flex-shrink: 0;
  box-sizing: border-box;
  height: 100%;
}

.home-header-static {
  width: 100%;
  height: 80px;
  min-height: 80px;
  background: linear-gradient(135deg, #020407 0%, #000000 100%);
  backdrop-filter: blur(20px);
  border-radius: 5px 5px 5px 5px;
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-bottom: none;
  flex-shrink: 0;
  cursor: pointer;
  display: flex;
  align-items: center;
  padding: 12px 16px;
  box-sizing: border-box;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.3);
  z-index: 1001;
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
}

.home-header-static.header-expanded {
  border-radius: 5px 5px 0 0;
  border-bottom: 1px solid rgba(255, 255, 255, 0.1);
}

.home-header-content {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  justify-content: center;
  gap: 4px;
  width: 100%;
  height: 100%;
  flex-shrink: 0;
  box-sizing: border-box;
}

.header-top-row {
  display: flex;
  align-items: center;
  justify-content: space-between;
  width: 100%;
  flex-shrink: 0;
  height: 24px;
}

.header-bottom-row {
  display: flex;
  align-items: center;
  justify-content: space-between;
  width: 100%;
  flex-shrink: 0;
  height: 20px;
  margin-bottom: 0;
}

.home-text {
  padding: 0;
  margin: 0;
  font-size: 16px;
  font-weight: 600;
  color: white;
  white-space: nowrap;
  line-height: 1;
  height: 20px;
  display: flex;
  align-items: center;
}

.total-devices-section {
  display: flex;
  align-items: center;
  margin-left: -5px;
  margin-top: 13px;
  flex-shrink: 0;
}

.devices-count {
  display: flex;
  align-items: center;
  gap: 6px;
  font-size: 11px;
  color: rgba(255, 255, 255, 0.747);
  font-weight: 500;
  background: rgba(59, 130, 246, 0.2);
  padding: 2px 6px;
  border-radius: 8px;
  flex-shrink: 0;
}

.count-badge {
  font-size: 11px;
  font-weight: 600;
  color: white;
  text-align: center;
  flex-shrink: 0;
}

.status-stats-section {
  display: flex;
  flex-direction: column;
  gap: 3px;
  align-items: flex-start;
  padding: 0px 4px 5px 0;
  flex-shrink: 0;
  margin-bottom: 21px;
}

.stat-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  width: 100%;
  font-size: 11px;
  color: rgba(255, 255, 255, 0.8);
}

.stat-info {
  display: flex;
  align-items: center;
  gap: 4px;
}

.stat-label {
  white-space: nowrap;
  min-width: 40px;
  text-align: left;
}

.stat-count {
  font-weight: 600;
  min-width: 10px;
  text-align: right;
}

.stat-dot {
  width: 5px;
  height: 5px;
  border-radius: 50%;
  flex-shrink: 0;
}

.stat-dot.moving {
  background: #1db4f9;
  box-shadow: 0 0 4px #1db4f9;
}

.stat-dot.stopped {
  background: #ff6b30;
  box-shadow: 0 0 4px #ff6b30;
}

.stat-dot.offline {
  background: #ffffff;
  box-shadow: 0 0 4px #ffffff;
}

.home-content-wrapper {
  flex: 1;
  overflow: hidden;
  max-height: 0;
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
}

.home-content-wrapper.expanded {
  max-height: calc(100% - 80px);
}

.home-content-panel {
  width: 100%;
  height: 100%;
  background: rgba(2, 2, 2, 0.853);
  backdrop-filter: blur(10px);
  border-radius: 0 0 5px 5px;
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-top: none;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.3);
  margin-top: 0;
  flex-shrink: 0;
  box-sizing: border-box;
  overflow: hidden;
  display: flex;
  flex-direction: column;
}

.home-expanded-content {
  flex: 1;
  overflow-y: auto;
  padding: 0;
  display: flex;
  flex-direction: column;
  width: 100%;
  box-sizing: border-box;
}

.devices-section {
  flex: 1;
  display: flex;
  flex-direction: column;
  width: 100%;
  padding: 0;
}

.devices-list {
  flex: 1;
  overflow-y: auto;
  display: flex;
  flex-direction: column;
  width: 100%;
  box-sizing: border-box;
}

/* Device Item Styles */
.device-item {
  display: flex;
  flex-direction: column;
  padding: 12px 16px;
  border-bottom: 1px solid rgba(68, 68, 68, 0.597);
  cursor: pointer;
  gap: 8px;
  transition: all 0.2s cubic-bezier(0.4, 0, 0.2, 1);
  width: 100%;
  box-sizing: border-box;
  flex-shrink: 0;
  min-height: 100px;
  border-radius: 6px;
  position: relative;
}

.device-item:hover {
  background: rgba(30, 30, 30, 0.833);
  border-radius: 6px;
}
.device-item.active {
  background: rgba(30, 30, 30, 0.833);
  border-radius: 6px;
}

.device-item.pinned-device {
  /*background: rgba(12, 12, 12, 0.486);*/
  border-left: 3px solid #ff6b30;
}

.device-row-first {
  display: flex;
  align-items: center;
  width: 100%;
  margin-bottom: 8px;
}

.device-column {
  display: flex;
  flex-direction: column;
  justify-content: center;
}

.icon-column {
  flex: 0 0 40px;
  align-items: center;
}

.name-column {
  flex: 1;
  align-items: flex-start;
  padding-left: 8px;
}

.lastseen-column {
  flex: 0 0 100px;
  align-items: flex-end;
}

.pin-column {
  flex: 0 0 40px;
  align-items: center;
  margin-left: 8px;
}

.pin-button {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 27px;
  height: 27px;
  border-radius: 8px;
  cursor: pointer;
  transition: all 0.2s cubic-bezier(0.4, 0, 0.2, 1);
  background: rgba(255, 255, 255, 0.05);
  border: 1px solid rgba(255, 255, 255, 0.1);
}

.pin-button:hover {
  background: rgba(255, 255, 255, 0.1);
  transform: scale(1.1);
}

.pin-button.pinned {
  background: rgba(255, 107, 48, 0.15);
}

.pin-button.pinned:hover {
  background: rgba(255, 107, 48, 0.25);
}

.pin-button svg {
  transition: all 0.2s cubic-bezier(0.4, 0, 0.2, 1);
}

.device-marker {
  flex-shrink: 0;
  margin-top: 0;
}

.marker-visual {
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
}

.marker-main {
  border-radius: 50%;
  background: rgba(0, 0, 0, 0.6);
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.3);
  border: 2px solid;
  position: relative;
  z-index: 2;
  width: 32px;
  height: 32px;
  flex-shrink: 0;
  transition: all 0.2s cubic-bezier(0.4, 0, 0.2, 1);
}

.device-item.online-moving .marker-main {
  border-color: #1db4f9;
}

.device-item.online-stopped .marker-main {
  border-color: #ff6b30;
}

.device-item.offline .marker-main {
  border-color: #ffffff;
}

.marker-inner {
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 50%;
  background: linear-gradient(135deg, #1f1f1fd1, #141414a3);
  width: 20px;
  height: 20px;
}

.device-name {
  font-size: 14px;
  font-weight: 600;
  color: white;
  letter-spacing: 0.1px;
  display: flex;
  align-items: center;
  gap: 8px;
}

.pinned-badge {
  font-size: 10px;
  font-weight: 500;
  color: #ff6b30;
  background: rgba(255, 107, 48, 0.15);
  padding: 2px 8px;
  border-radius: 10px;
  border: 1px solid rgba(255, 107, 48, 0.3);
}

.device-last-seen {
  font-size: 10px;
  color: rgba(255, 255, 255, 0.6);
  font-weight: 500;
  text-align: right;
  white-space: nowrap;
  background: rgba(255, 255, 255, 0.05);
  padding: 3px 6px;
  border-radius: 10px;
  border: 1px solid rgba(255, 255, 255, 0.1);
}

/* Status Icons */
.device-status-icons {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 6px 0;
  min-height: 40px;
  width: 100%;
}

.battery-left-container {
  display: flex;
  align-items: center;
}

.battery-with-text {
  display: flex;
  align-items: center;
  gap: 6px;
  padding: 4px 8px;
  border-radius: 6px;
  border: 1px solid rgba(255, 255, 255, 0.1);
  transition: all 0.2s cubic-bezier(0.4, 0, 0.2, 1);
}

.battery-icon {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 20px;
  height: 20px;
}

.battery-icon svg {
  width: 18px !important;
  height: 18px !important;
}

.battery-text {
  font-size: 12px;
  font-weight: 500;
  white-space: nowrap;
  color: #ffffff;
}

.battery-with-text.status-good .battery-icon svg,
.battery-with-text.status-good {
  color: #4caf50;
  fill: #4caf50;
}

.battery-with-text.status-warning .battery-icon svg,
.battery-with-text.status-warning {
  color: #ebac00;
  fill: #ebac00;
}

.battery-with-text.status-critical .battery-icon svg,
.battery-with-text.status-critical {
  color: #db0000;
  fill: #db0000;
}

.status-icons-right {
  display: flex;
  justify-content: flex-end;
  flex-wrap: wrap;
  gap: 12px;
}

.status-icons-right .status-icon {
  position: relative;
  width: 28px;
  height: 28px;
  border-radius: 6px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: all 0.2s cubic-bezier(0.4, 0, 0.2, 1);
  border: 1px solid rgba(120, 120, 120, 0.5);
}

.status-icons-right .status-icon:hover {
  transform: scale(1.15);
  z-index: 10;
}

.status-icons-right .status-icon:hover .status-tooltip {
  opacity: 1;
  visibility: visible;
  transform: translateY(0);
}

.status-tooltip {
  position: absolute;
  bottom: 100%;
  transform: translateX(-50%) translateY(5px);
  background: rgba(0, 0, 0, 0.9);
  color: white;
  padding: 5px 8px;
  border-radius: 4px;
  font-size: 11px;
  white-space: nowrap;
  opacity: 0;
  visibility: hidden;
  transition: all 0.2s cubic-bezier(0.4, 0, 0.2, 1);
  pointer-events: none;
  z-index: 100;
  border: 1px solid rgba(255, 255, 255, 0.1);
}

.status-tooltip::after {
  content: "";
  position: absolute;
  top: 100%;
  left: 50%;
  transform: translateX(-50%);
  border-width: 5px;
  border-style: solid;
  border-color: rgba(0, 0, 0, 0.9) transparent transparent transparent;
}

.status-icons-right .status-good {
  display: none !important;
}

.status-icons-right .status-neutral {
  display: none !important;
}

.status-icons-right .status-warning svg {
  fill: #ebac00;
}

.status-icons-right .status-critical svg {
  fill: #db0000;
}

/* Expanded Device Details */
.device-details-expanded {
  display: flex;
  flex-direction: column;
  gap: 8px;
  padding: 8px 0;
  animation: fadeIn 0.3s ease;
}

.device-details-expanded .status-warning,
.device-details-expanded .status-critical {
  border: none;
}

.details-row {
  display: flex;
  justify-content: space-between;
  align-items: center;
  width: 100%;
}

.detail-item {
  display: flex;
  align-items: center;
  gap: 6px;
  flex: 1;
  padding: 6px 8px;
  border-radius: 6px;
  transition: all 0.2s cubic-bezier(0.4, 0, 0.2, 1);
}

.detail-item:hover {
  background: rgba(255, 255, 255, 0.08);
}

.detail-icon {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 17px;
  height: 17px;
}

.detail-icon svg {
  width: 17px;
  height: 17px;
}

.detail-text {
  font-size: 12px;
  font-weight: 500;
  color: rgba(255, 255, 255, 0.91);
  white-space: nowrap;
}

.detail-item.status-good .detail-icon svg {
  fill: #4caf50 !important;
}

.detail-item.status-warning .detail-icon svg {
  fill: #ebac00 !important;
}

.detail-item.status-critical .detail-icon svg {
  fill: #db0000 !important;
}

.device-item.expanded {
  min-height: 220px;
  background-color: #101010c1;
}

.device-item.expanded .device-status-icons {
  display: none;
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(-10px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

/* Other Panels */
.sliding-panel {
  width: 400px;
  min-width: 400px;
  max-width: 400px;
  height: 100%;
  background: rgba(17, 17, 17, 0.644);
  backdrop-filter: blur(20px);
  border: 1px solid rgba(255, 255, 255, 0.2);
  border-radius: 5px 20px 20px 5px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.3);
  flex-shrink: 0;
  box-sizing: border-box;
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
}

.panel-content {
  height: 100%;
  display: flex;
  flex-direction: column;
  overflow: hidden;
  width: 100%;
  box-sizing: border-box;
}

.panel-header {
  padding: 0 16px;
  background: linear-gradient(135deg, #020407 0%, #000000 100%);
  color: white;
  border-bottom: 1px solid rgba(255, 255, 255, 0.1);
  height: 80px;
  min-height: 80px;
  flex-shrink: 0;
  display: flex;
  align-items: center;
  width: 100%;
  box-sizing: border-box;
  border-radius: 5px 20px 0 0;
}

.panel-header h3 {
  margin: 0;
  font-size: 16px;
  font-weight: 600;
  color: white;
}

.panel-body {
  flex: 1;
  overflow-y: auto;
  color: white;
  width: 100%;
  box-sizing: border-box;
  border-radius: 0 0 20px 5px;
}

/* Scrollbar Styling */
:deep(::-webkit-scrollbar) {
  width: 6px;
}

:deep(::-webkit-scrollbar-track) {
  background: rgba(255, 255, 255, 0.1);
  border-radius: 3px;
  margin: 2px 0;
}

:deep(::-webkit-scrollbar-thumb) {
  background: rgba(255, 255, 255, 0.3);
  border-radius: 3px;
  transition: background 0.3s;
}

:deep(::-webkit-scrollbar-thumb:hover) {
  background: rgba(255, 255, 255, 0.5);
}

.black-bg {
  background: rgba(12, 12, 12, 0.486) !important;
  /* color: white; */
}
</style>
