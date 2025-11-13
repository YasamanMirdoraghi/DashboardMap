<template>
  <!-- Sidebar با آیکون‌ها و متن -->
  <div class="sidebar-container">
    <!-- نوار آیکون‌ها -->
    <q-card class="icon-sidebar" :class="{ expanded: sidebarExpanded }" elevated>
      <div class="sidebar-header">
        <q-btn
          round
          dense
          flat
          :icon="sidebarExpanded ? 'chevron_left' : 'menu'"
          class="collapse-btn"
          @click="toggleSidebar"
        />
        <transition name="fade">
          <span v-if="sidebarExpanded" class="sidebar-title">Dashboard Menu</span>
        </transition>
      </div>

      <div class="icon-items">
        <div
          v-for="item in menuItems"
          :key="item.id"
          :class="{
            active: activePanel === item.id,
            'expanded-item': sidebarExpanded,
            hovered: hoveredItem === item.id,
          }"
          class="icon-item-wrapper"
          @click="togglePanel(item.id)"
          @mouseenter="handleMouseEnter(item.id)"
          @mouseleave="handleMouseLeave"
        >
          <div class="icon-item-content">
            <div class="icon-wrapper">
              <!-- === جایگذاری SVG برای منو اینجا === -->
              <!-- برای هر آیتم منو، SVG مربوطه را اینجا قرار دهید -->
              <div v-if="item.id === 'home'" class="custom-svg-icon">
                <!-- SVG مربوط به Home -->
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

              <div v-else-if="item.id === 'geofence'" class="custom-svg-icon">
                <!-- SVG مربوط به Geofence -->
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

              <div v-else-if="item.id === 'rfid'" class="custom-svg-icon">
                <!-- SVG مربوط به RFID -->
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

              <div v-else-if="item.id === 'alarms'" class="custom-svg-icon">
                <!-- SVG مربوط به Alarms -->
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

              <div v-else-if="item.id === 'settings'" class="custom-svg-icon">
                <!-- SVG مربوط به Settings -->
                <svg
                  xmlns="http://www.w3.org/2000/svg"
                  height="24px"
                  viewBox="0 -960 960 960"
                  width="24px"
                  fill="currentColor"
                >
                  <path
                    d="M686-132 444-376q-20 8-40.5 12t-43.5 4q-100 0-170-70t-70-170q0-36 10-68.5t28-61.5l146 146 72-72-146-146q29-18 61.5-28t68.5-10q100 0 170 70t70 170q0 23-4 43.5T584-516l244 242q12 12 12 29t-12 29l-84 84q-12 12-29 12t-29-12Zm29-85 27-27-256-256q18-20 26-46.5t8-53.5q0-60-38.5-104.5T386-758l74 74q12 12 12 28t-12 28L332-500q-12 12-28 12t-28-12l-74-74q9 57 53.5 95.5T360-440q26 0 52-8t47-25l256 256ZM472-488Z"
                  />
                </svg>
              </div>
            </div>
            <transition name="fade">
              <span v-if="sidebarExpanded" class="item-label">{{ item.title }}</span>
            </transition>
          </div>
        </div>
      </div>
    </q-card>

    <!-- پنل‌های کشویی -->
    <transition name="slide-panel">
      <q-card v-if="activePanel" class="sliding-panel" elevated>
        <!-- پنل Home -->
        <div v-if="activePanel === 'home'" class="panel-content">
          <div class="panel-header">
            <div class="header-title">
              <!-- === جایگذاری SVG برای هدر پنل Home اینجا === -->
              <div class="panel-svg-icon">
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
              <h3>Home</h3>
            </div>
            <q-btn round flat dense icon="close" @click="closePanel" />
          </div>

          <div class="content-section">
            <h4 class="section-title">System Overview</h4>
            <!-- محتوای Home خالی -->
          </div>

          <div class="content-section">
            <h4 class="section-title">Recent Activity</h4>
            <!-- محتوای Recent Activity خالی -->
          </div>
        </div>

        <!-- پنل Geofence -->
        <div v-if="activePanel === 'geofence'" class="panel-content">
          <div class="panel-header">
            <div class="header-title">
              <!-- === جایگذاری SVG برای هدر پنل Geofence اینجا === -->
              <div class="panel-svg-icon">
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
              <h3>Geofence</h3>
            </div>
            <q-btn round flat dense icon="close" @click="closePanel" />
          </div>

          <div class="search-section">
            <q-input
              v-model="geofenceSearch"
              placeholder="Search geofences..."
              dense
              outlined
              class="search-input"
            >
              <template v-slot:append>
                <q-icon name="search" />
              </template>
            </q-input>
          </div>

          <div class="geofence-list">
            <!-- محتوای Geofence خالی -->
          </div>
        </div>

        <!-- پنل RFID -->
        <div v-if="activePanel === 'rfid'" class="panel-content">
          <div class="panel-header">
            <div class="header-title">
              <!-- === جایگذاری SVG برای هدر پنل RFID اینجا === -->
              <div class="panel-svg-icon">
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
              <h3>RFID</h3>
            </div>
            <q-btn round flat dense icon="close" @click="closePanel" />
          </div>

          <div class="search-section">
            <q-input
              v-model="rfidSearch"
              placeholder="Search RFID devices..."
              dense
              outlined
              class="search-input"
            >
              <template v-slot:append>
                <q-icon name="search" />
              </template>
            </q-input>
          </div>

          <div class="rfid-content">
            <h4 class="section-title">RFID Devices</h4>
            <!-- محتوای RFID خالی -->
          </div>
        </div>

        <!-- پنل Alarms -->
        <div v-if="activePanel === 'alarms'" class="panel-content">
          <div class="panel-header">
            <div class="header-title">
              <!-- === جایگذاری SVG برای هدر پنل Alarms اینجا === -->
              <div class="panel-svg-icon">
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
              <h3>Alarms</h3>
            </div>
            <q-btn round flat dense icon="close" @click="closePanel" />
          </div>

          <div class="alerts-list">
            <!-- محتوای Alarms خالی -->
          </div>
        </div>

        <!-- پنل Settings -->
        <div v-if="activePanel === 'settings'" class="panel-content settings-panel">
          <div class="panel-header">
            <div class="header-title">
              <!-- === جایگذاری SVG برای هدر پنل Settings اینجا === -->
              <div class="panel-svg-icon">
                <svg
                  xmlns="http://www.w3.org/2000/svg"
                  height="24px"
                  viewBox="0 -960 960 960"
                  width="24px"
                  fill="currentColor"
                >
                  <path
                    d="M686-132 444-376q-20 8-40.5 12t-43.5 4q-100 0-170-70t-70-170q0-36 10-68.5t28-61.5l146 146 72-72-146-146q29-18 61.5-28t68.5-10q100 0 170 70t70 170q0 23-4 43.5T584-516l244 242q12 12 12 29t-12 29l-84 84q-12 12-29 12t-29-12Zm29-85 27-27-256-256q18-20 26-46.5t8-53.5q0-60-38.5-104.5T386-758l74 74q12 12 12 28t-12 28L332-500q-12 12-28 12t-28-12l-74-74q9 57 53.5 95.5T360-440q26 0 52-8t47-25l256 256ZM472-488Z"
                  />
                </svg>
              </div>
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
                  <span class="setting-description"
                    >Automatically update data every 30 seconds</span
                  >
                </div>
                <q-toggle v-model="settings.autoRefresh" color="primary" />
              </div>

              <div class="setting-item">
                <div class="setting-info">
                  <span class="setting-label">Notifications</span>
                  <span class="setting-description"
                    >Receive browser notifications</span
                  >
                </div>
                <q-toggle v-model="settings.notifications" color="primary" />
              </div>

              <div class="setting-item">
                <div class="setting-info">
                  <span class="setting-label">Dark Mode</span>
                  <span class="setting-description">Switch to dark theme</span>
                </div>
                <q-toggle v-model="isDarkMode" @update:model-value="$emit('toggle-dark-mode', $event)" color="primary" />
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
</template>

<script setup>
import { ref } from "vue";

// const props = defineProps({
//   isDarkMode: Boolean
// });

// const emit = defineEmits(['toggle-dark-mode']);

// Reactive state
const activePanel = ref(null);
const geofenceSearch = ref("");
const rfidSearch = ref("");
const sidebarExpanded = ref(false);
const hoveredItem = ref(null);

// Settings
const settings = ref({
  autoRefresh: true,
  notifications: true,
});

// Data - منوی 5 آیتمی بر اساس تصویر
const menuItems = ref([
  { id: "home", title: "Home" },
  { id: "geofence", title: "Geofence" },
  { id: "rfid", title: "RFID" },
  { id: "alarms", title: "Alarms" },
  { id: "settings", title: "Settings" },
]);

// Methods
const toggleSidebar = () => {
  sidebarExpanded.value = !sidebarExpanded.value;
};

const togglePanel = (panelId) => {
  if (activePanel.value === panelId) {
    activePanel.value = null;
  } else {
    activePanel.value = panelId;
  }
};

const closePanel = () => {
  activePanel.value = null;
};

const handleMouseEnter = (itemId) => {
  hoveredItem.value = itemId;
};

const handleMouseLeave = () => {
  hoveredItem.value = null;
};
</script>

<style scoped>
/* Sidebar Styles */
.sidebar-container {
  position: absolute;
  top: 0;
  left: 0;
  height: 100%;
  z-index: 1000;
  display: flex;
  align-items: flex-start;
}

.icon-sidebar {
  width: 70px;
  height: 100%;
  background: var(--accent-gradient);
  backdrop-filter: blur(20px);
  -webkit-backdrop-filter: blur(20px);
  border-radius: 0;
  border-right: 1px solid var(--border-color);
  display: flex;
  flex-direction: column;
  padding: 16px 0;
  box-shadow: 4px 0 20px var(--shadow-color), inset -1px 0 0 rgba(255, 255, 255, 0.1);
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  overflow: hidden;
  flex-shrink: 0;
}

.icon-sidebar.expanded {
  width: 250px;
}

.sidebar-header {
  display: flex;
  align-items: center;
  gap: 12px;
  margin-bottom: 24px;
  padding: 0 16px;
  min-height: 40px;
}

.sidebar-title {
  color: var(--text-primary);
  font-weight: 600;
  font-size: 16px;
  white-space: nowrap;
  letter-spacing: -0.01em;
}

.collapse-btn {
  color: var(--text-primary);
  transition: all 0.3s ease;
}

.collapse-btn:hover {
  background: rgba(255, 255, 255, 0.15);
  transform: scale(1.05);
}

/* آیتم‌های منو */
.icon-items {
  display: flex;
  flex-direction: column;
  gap: 4px;
  flex: 1;
  padding: 0 8px;
  width: 100%;
}

.icon-item-wrapper {
  width: 100%;
  height: 50px;
  display: flex;
  align-items: center;
  border-radius: 8px;
  cursor: pointer;
  transition: all 0.3s ease;
  position: relative;
}

.icon-item-wrapper:hover {
  /* background: rgba(255, 255, 255, 0.1); */
   /* border-left: 2px solid #ff4f00; */
}

.icon-item-wrapper:hover .custom-svg-icon svg,
.icon-item-wrapper:hover .item-label {
  color: #ff4f00 !important;
  fill: #ff4f00 !important;

}

.icon-item-wrapper.active {
  /* background: rgba(255, 255, 255, 0.2); */
  /* border-left: 2px solid #0095da; */
}

.icon-item-wrapper.active .custom-svg-icon svg,
.icon-item-wrapper.active .item-label {
  color: #0095da !important;
  fill: #0095da !important;
}

.icon-item-wrapper.hovered {
  transform: translateY(-1px);
}

.icon-item-content {
  display: flex;
  align-items: center;
  width: 100%;
  height: 100%;
  padding: 0 12px;
  gap: 12px;
}

.icon-wrapper {
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 24px;
  height: 24px;
  flex-shrink: 0;
}

/* استایل برای آیکون‌های SVG سفارشی */
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
  transition: all 0.3s ease;
}

/* استایل برای آیکون‌های SVG در پنل‌ها */
.panel-svg-icon {
  width: 24px;
  height: 24px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.panel-svg-icon svg {
  width: 100%;
  height: 100%;
  color: white;
  fill: white;
}

.item-label {
  color: white;
  font-size: 14px;
  font-weight: 500;
  white-space: nowrap;
  opacity: 0;
  transition: all 0.3s ease;
}

.icon-sidebar.expanded .item-label {
  opacity: 1;
}

/* پنل‌های کشویی */
.sliding-panel {
  width: 350px;
  height: 100%;
  background: var(--bg-primary);
  backdrop-filter: blur(20px);
  border-radius: 0;
  border-right: 1px solid var(--border-color);
  margin-left: 0;
}

.slide-panel-enter-active {
  transition: all 0.3s ease-out;
}

.slide-panel-leave-active {
  transition: all 0.2s ease-in;
}

.slide-panel-enter-from {
  transform: translateX(-100%);
  opacity: 0;
}

.slide-panel-leave-to {
  transform: translateX(-100%);
  opacity: 0;
}

/* محتوای پنل‌ها */
.panel-content {
  height: 100%;
  display: flex;
  flex-direction: column;
}

/* پنل Settings در پایین */
.settings-panel .settings-content {
  margin-top: auto;
  padding-bottom: 20px;
}

.panel-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 20px;
  background: var(--accent-gradient);
  color: white;
}

.header-title {
  display: flex;
  align-items: center;
  gap: 12px;
}

.header-title h3 {
  margin: 0;
  font-size: 18px;
  font-weight: 600;
}

.content-section {
  padding: 20px;
  flex: 1;
  overflow-y: auto;
}

.section-title {
  color: #9ca3af;
  font-size: 12px;
  font-weight: 600;
  margin: 0 0 16px 0;
  text-transform: uppercase;
}

/* Settings Styles */
.settings-content {
  padding: 20px;
  flex: 1;
  overflow-y: auto;
  display: flex;
  flex-direction: column;
}

.settings-card {
  background: var(--bg-secondary);
  border: 1px solid var(--border-color);
  padding: 20px;
  margin-bottom: 20px;
  border-radius: 12px;
}

.setting-item {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 16px 0;
  border-bottom: 1px solid var(--border-color);
  transition: all 0.3s ease;
}

.setting-item:hover {
  background: var(--bg-accent);
  margin: 0 -20px;
  padding: 16px 20px;
}

.setting-item:last-child {
  border-bottom: none;
}

.setting-info {
  flex: 1;
  text-align: left;
}

.setting-label {
  display: block;
  color: var(--text-primary);
  font-size: 14px;
  font-weight: 600;
  margin-bottom: 4px;
}

.setting-description {
  display: block;
  color: var(--text-secondary);
  font-size: 13px;
  line-height: 1.4;
}

.settings-actions {
  padding-top: 20px;
  margin-top: auto;
}

/* Search Section */
.search-section {
  padding: 20px 20px 0;
}

.search-input {
  background: var(--bg-secondary);
}

:deep(.search-input .q-field__control) {
  color: var(--text-primary);
  border-radius: 10px;
  height: 44px;
}

:deep(.search-input .q-field__control:before) {
  border-color: var(--border-color);
}

:deep(.search-input .q-field__control:hover:before) {
  border-color: var(--accent-color);
}

/* انیمیشن‌ها */
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.2s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}

/* Scrollbar */
:deep(::-webkit-scrollbar) {
  width: 6px;
}

:deep(::-webkit-scrollbar-track) {
  background: rgba(255, 255, 255, 0.1);
  border-radius: 3px;
}

:deep(::-webkit-scrollbar-thumb) {
  background: rgba(255, 255, 255, 0.3);
  border-radius: 3px;
}
</style>
