<script setup>
import { computed, ref } from 'vue';
import { useI18n } from 'vue-i18n';
import {
  DashboardOutlined,
  UserOutlined,
  SettingOutlined,
  ToolOutlined,
  ClusterOutlined,
  LogoutOutlined,
  CloseOutlined,
  MenuFoldOutlined,
} from '@ant-design/icons-vue';

import { currentTheme } from '@/composables/useTheme.js';
import ThemeSwitch from '@/components/ThemeSwitch.vue';

const { t } = useI18n();

const SIDEBAR_COLLAPSED_KEY = 'isSidebarCollapsed';

const props = defineProps({
  // Path prefix (e.g. /custom-base/) the panel is served under. Defaults
  // to '' which means tab keys end up as '/panel/...'. Pages pass the
  // value the Go backend gave them (in production via a meta tag).
  basePath: { type: String, default: '' },
  // Current request URI so the matching menu item highlights.
  requestUri: { type: String, default: '' },
});

// AD-Vue 4 dropped <a-icon :type="x"> in favor of explicit icon
// imports — keep a small name-to-component map so tab definitions stay
// declarative.
const iconByName = {
  dashboard: DashboardOutlined,
  user: UserOutlined,
  setting: SettingOutlined,
  tool: ToolOutlined,
  cluster: ClusterOutlined,
  logout: LogoutOutlined,
};

// basePath comes from Go (`/` by default, `/myprefix/` when configured) so
// these concatenations land on absolute paths. In dev we synthesize the prop
// from a window global which can be empty — force a leading slash so the
// browser doesn't resolve the link relative to the current pathname (which
// would turn /panel/settings + 'panel/...' into /panel/panel/...).
const prefix = props.basePath?.startsWith('/') ? props.basePath : `/${props.basePath || ''}`;

// Labels are i18n-driven so the sidebar matches the locale picked
// in panel settings without a page reload of the sidebar component.
const tabs = computed(() => [
  { key: `${prefix}panel/`,         icon: 'dashboard', title: t('menu.dashboard') },
  { key: `${prefix}panel/inbounds`, icon: 'user',      title: t('menu.inbounds') },
  { key: `${prefix}panel/nodes`,    icon: 'cluster',   title: t('menu.nodes') },
  { key: `${prefix}panel/settings`, icon: 'setting',   title: t('menu.settings') },
  { key: `${prefix}panel/xray`,     icon: 'tool',      title: t('menu.xray') },
  { key: `${prefix}logout`,         icon: 'logout',    title: t('logout') },
]);

const activeTab = ref([props.requestUri]);

const drawerOpen = ref(false);
const collapsed = ref(JSON.parse(localStorage.getItem(SIDEBAR_COLLAPSED_KEY) || 'false'));

function openLink(key) {
  if (key.startsWith('http')) {
    window.open(key);
  } else {
    window.location.href = key;
  }
}

function onCollapse(isCollapsed, type) {
  // Only persist explicit toggle clicks, not breakpoint-triggered collapses.
  if (type === 'clickTrigger') {
    localStorage.setItem(SIDEBAR_COLLAPSED_KEY, isCollapsed);
    collapsed.value = isCollapsed;
  }
}

function toggleDrawer() {
  drawerOpen.value = !drawerOpen.value;
}

function closeDrawer() {
  drawerOpen.value = false;
}
</script>

<template>
  <div class="ant-sidebar">
    <a-layout-sider
      :theme="currentTheme"
      collapsible
      :collapsed="collapsed"
      breakpoint="md"
      @collapse="onCollapse"
    >
      <ThemeSwitch />
      <a-menu
        :theme="currentTheme"
        mode="inline"
        :selected-keys="activeTab"
        @click="({ key }) => openLink(key)"
      >
        <a-menu-item v-for="tab in tabs" :key="tab.key">
          <component :is="iconByName[tab.icon]" />
          <span>{{ tab.title }}</span>
        </a-menu-item>
      </a-menu>
    </a-layout-sider>

    <a-drawer
      placement="left"
      :closable="false"
      :open="drawerOpen"
      :wrap-class-name="currentTheme"
      :wrap-style="{ padding: 0 }"
      :style="{ height: '100%' }"
      @close="closeDrawer"
    >
      <ThemeSwitch />
      <a-menu
        :theme="currentTheme"
        mode="inline"
        :selected-keys="activeTab"
        @click="({ key }) => openLink(key)"
      >
        <a-menu-item v-for="tab in tabs" :key="tab.key">
          <component :is="iconByName[tab.icon]" />
          <span>{{ tab.title }}</span>
        </a-menu-item>
      </a-menu>
    </a-drawer>

    <button class="drawer-handle" type="button" @click="toggleDrawer">
      <CloseOutlined v-if="drawerOpen" />
      <MenuFoldOutlined v-else />
    </button>
  </div>
</template>

<style scoped>
.ant-sidebar > .ant-layout-sider {
  height: 100%;
}

.drawer-handle {
  position: fixed;
  top: 16px;
  left: 16px;
  z-index: 1100;
  background: rgba(0, 0, 0, 0.55);
  color: #fff;
  border: none;
  width: 36px;
  height: 36px;
  border-radius: 50%;
  cursor: pointer;
  display: none;
  align-items: center;
  justify-content: center;
}

@media (max-width: 768px) {
  .drawer-handle {
    display: inline-flex;
  }

  /* On mobile the drawer is the menu — hide the inline sider's content
   * + the collapse trigger so the sider stops taking layout space and
   * leaves no remnant button next to the page. */
  .ant-sidebar > .ant-layout-sider :deep(.ant-layout-sider-children),
  .ant-sidebar > .ant-layout-sider :deep(.ant-layout-sider-trigger) {
    display: none;
  }

  .ant-sidebar > .ant-layout-sider {
    flex: 0 0 0 !important;
    max-width: 0 !important;
    min-width: 0 !important;
    width: 0 !important;
  }
}
</style>

<style>
/* 1. 全局页面背景 (击穿暗黑/白天模式，管辖所有页面) */
html, body, #app, .ant-layout, .ant-layout-content {
  background-image: url('https://img.shiyeo.art/%E5%9B%BE%E7%89%87/e7f8f0c3ed6eacaad0004ef19d7efab6.png') !important;
  background-size: cover !important;
  background-position: center !important;
  background-attachment: fixed !important;
  background-color: transparent !important;
}

/* 2. 侧边栏、菜单、移动端抽屉背景完全透明化 */
.ant-layout-sider,
.ant-menu,
.ant-menu-sub,
.ant-layout-sider-children,
.ant-drawer-wrapper-body,
.ant-drawer-body {
  background: rgba(20, 20, 20, 0.4) !important;
  backdrop-filter: blur(12px) !important;
  -webkit-backdrop-filter: blur(12px) !important;
  border-right: 1px solid rgba(255, 255, 255, 0.1) !important;
}
.ant-menu-item-selected {
  background: rgba(255, 255, 255, 0.2) !important;
}

/* 3. 数据卡片透明化 */
.ant-card {
  background: rgba(20, 20, 20, 0.4) !important;
  backdrop-filter: blur(12px) !important;
  -webkit-backdrop-filter: blur(12px) !important;
  border: 1px solid rgba(255, 255, 255, 0.1) !important;
}
.ant-card-head {
  background: transparent !important;
  border-bottom: 1px solid rgba(255, 255, 255, 0.1) !important;
}

/* 4. 入站列表、节点等页面的表格强行透明化 */
.ant-table, 
.ant-table-wrapper, 
.ant-table-container,
.ant-table-content,
.ant-table-header {
  background: transparent !important;
}
.ant-table-thead > tr > th {
  background: rgba(0, 0, 0, 0.5) !important; /* 表头深色半透明 */
  color: #ffffff !important;
  border-bottom: 1px solid rgba(255, 255, 255, 0.15) !important;
}
.ant-table-tbody > tr > td {
  background: rgba(20, 20, 20, 0.4) !important; /* 表格行半透明 */
  color: #eeeeee !important;
  border-bottom: 1px solid rgba(255, 255, 255, 0.05) !important;
}
.ant-table-tbody > tr:hover > td {
  background: rgba(255, 255, 255, 0.15) !important; /* 鼠标悬停表格行发亮 */
}
.ant-table-expanded-row-fixed {
  background: transparent !important;
}

/* 5. 弹窗和抽屉 (修改设置、添加节点时弹出的窗口) */
.ant-modal-content, 
.ant-drawer-content,
.ant-drawer-header {
  background: rgba(20, 20, 20, 0.6) !important;
  backdrop-filter: blur(15px) !important;
  -webkit-backdrop-filter: blur(15px) !important;
  border: 1px solid rgba(255, 255, 255, 0.1) !important;
}
.ant-modal-header {
  background: transparent !important;
  border-bottom: 1px solid rgba(255, 255, 255, 0.1) !important;
}

/* 6. 强制全局所有文字变亮，防止背景吞字 */
.ant-typography,
.ant-card-head-title,
.ant-statistic-title,
.ant-statistic-content,
.ant-menu-item,
.ant-menu-submenu-title,
.ant-form-item-label > label,
.ant-modal-title,
.ant-drawer-title,
.ant-table-cell {
  color: rgba(255, 255, 255, 0.9) !important;
}

/* 7. 输入框和下拉选择框透明 */
.ant-input, 
.ant-input-password, 
.ant-select-selector,
.ant-select-dropdown {
  background: rgba(0, 0, 0, 0.3) !important;
  color: #ffffff !important;
  border: 1px solid rgba(255, 255, 255, 0.2) !important;
}
.ant-input::placeholder,
.ant-select-selection-placeholder {
  color: rgba(255, 255, 255, 0.4) !important;
}
.ant-select-selection-item {
  color: #ffffff !important;
}
</style>
