<template>
  <!-- Sidebar با آیکون‌ها و متن -->
  <div class="sidebar-container">
    <!-- نوار آیکون‌ها -->
    <q-card
      class="icon-sidebar"
      :class="{ expanded: sidebarExpanded || sidebarHovered }"
      elevated
    >
      <div class="sidebar-header">
        <q-btn
          round
          dense
          flat
          :icon="(sidebarExpanded || sidebarHovered) ? 'chevron_left' : 'menu'"
          class="collapse-btn"
          @click="toggleSidebar"
          @mouseenter="handleMenuHover(true)"
          @mouseleave="handleMenuHover(false)"
        />
        <span v-if="sidebarExpanded || sidebarHovered" class="sidebar-title">Menu</span>
      </div>

      <div class="icon-items">
        <div
          v-for="item in menuItems"
          :key="item.id"
          :class="{
            active: activePanel === item.id,
            'expanded-item': sidebarExpanded || sidebarHovered,
            hovered: hoveredItem === item.id,
          }"
          class="icon-item-wrapper"
          @click="togglePanel(item.id)"
          @mouseenter="handleMouseEnter(item.id)"
          @mouseleave="handleMouseLeave"
        >
          <div class="icon-item-content">
            <div class="icon-wrapper">
              <!-- آیتم‌های منو بدون تغییر -->
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

              <div v-else-if="item.id === 'settings'" class="custom-svg-icon">
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
            <span v-if="sidebarExpanded || sidebarHovered" class="item-label">{{ item.title }}</span>
          </div>
        </div>
      </div>
    </q-card>

    <q-card
      v-if="activePanel === 'home'"
      class="sliding-panel home-panel"
      :class="{ expanded: homeExpanded }"
      elevated
    >
      <div class="panel-content">
        <div class="panel-header home-panel-header">
          <div class="home-header-content">
            <h3 class="home-text">Home</h3>
            <q-btn round dense flat class="menu-toggle-btn" @click="toggleHomePanel">
              <svg
                v-if="!homeExpanded"
                xmlns="http://www.w3.org/2000/svg"
                height="30px"
                viewBox="0 -960 960 960"
                width="30px"
                fill="currentColor"
              >
                <path d="M480-360 280-560h400L480-360Z"/>
              </svg>
              <svg
                v-else
                xmlns="http://www.w3.org/2000/svg"
                height="30px"
                viewBox="0 -960 960 960"
                width="30px"
                fill="currentColor"
              >
                <path d="m280-400 200-200 200 200H280Z"/>
              </svg>
            </q-btn>
          </div>
        </div>

        <div v-if="homeExpanded" class="home-expanded-content">
          <div class="content-section">
            <!-- محتوای Home خالی -->
          </div>
          <div class="content-section">
            <!-- محتوای Recent Activity خالی -->
          </div>
        </div>
      </div>
    </q-card>
  </div>
</template>

<script setup>
import { ref } from "vue";

// Reactive state
const activePanel = ref("home");
const sidebarExpanded = ref(false);
const sidebarHovered = ref(false); // حالت جدید برای hover روی آیکون منو
const hoveredItem = ref(null);
const homeExpanded = ref(false);

// Data - منوی 5 آیتمی
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
  // وقتی کلیک می‌شه، hover رو غیرفعال کن تا منو باز بمونه
  sidebarHovered.value = false;
};

const toggleHomePanel = () => {
  homeExpanded.value = !homeExpanded.value;
};

const togglePanel = (panelId) => {
  if (activePanel.value === panelId) {
    if (panelId !== "home") {
      activePanel.value = "home";
    }
  } else {
    activePanel.value = panelId;
  }
  if (panelId !== "home") {
    homeExpanded.value = false;
  }
};

// فقط روی آیکون منو هاور کار کنه
const handleMenuHover = (isHovered) => {
  if (!sidebarExpanded.value) {
    // فقط وقتی که منو با کلیک باز نشده، hover روی آیکون کار کنه
    sidebarHovered.value = isHovered;
  }
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
  top: 10px;
  left: 10px;
  height: calc(100% - 20px);
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
  border-radius: 5px;
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
  width: 150px;
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
  /* border-radius: 8px; */
  cursor: pointer;
  transition: all 0.3s ease;
  position: relative;
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
  border-radius: 5px;
  border: 1px solid var(--border-color);
  margin: 0px 10px 10px 10px;
  box-shadow: 0 4px 20px var(--shadow-color);
  transition: all 0.5s cubic-bezier(0.4, 0, 0.2, 1);
}

/* پنل Home در حالت عادی */
.home-panel:not(.expanded) {
  height: 70px;
  overflow: hidden;
}

/* پنل Home در حالت گسترده */
.home-panel.expanded {
  height:100%;
  background: rgba(255, 255, 255, 0.1);
  backdrop-filter: blur(9px);
  -webkit-backdrop-filter: blur(20px);
  border: 1px solid rgba(255, 255, 255, 0.2);
}

/* محتوای پنل‌ها */
.panel-content {
  height: 100%;
  display: flex;
  flex-direction: column;
  overflow: hidden;
}

/* استایل مخصوص هدر پنل هوم */
.home-panel-header {
  padding: 0 24px;
  height: 70px;
  background: var(--accent-gradient);
  color: white;
}

.home-header-content {
  display: flex;
  align-items: center;
  justify-content: space-between;
  width: 100%;
  height: 100%;
}

.home-text {
  margin: 0;
  font-size: 18px;
  font-weight: 600;
  color: white;
}

.menu-toggle-btn {
  color: white;
  transition: all 0.3s ease;
}

.menu-toggle-btn:hover {
  background: rgba(255, 255, 255, 0.15);
  transform: scale(1.05);
}

/* محتوای گسترده Home */
.home-expanded-content {
  flex: 1;
  overflow-y: auto;
  padding: 20px;
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
