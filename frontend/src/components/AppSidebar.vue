<style>
/* 1. 强制全站背景图 (击穿暗黑/白天模式，管辖所有页面) */
html, body, #app, .ant-layout, .ant-layout-content {
  background-color: transparent !important;
  background-image: url('https://img.shiyeo.art/%E5%9B%BE%E7%89%87/e7f8f0c3ed6eacaad0004ef19d7efab6.png') !important;
  background-size: cover !important;
  background-position: center !important;
  background-attachment: fixed !important;
}

/* 2. 侧边栏、菜单完全透明化 */
.ant-layout-sider,
.ant-menu,
.ant-menu-sub,
.ant-layout-sider-children {
  background: rgba(20, 20, 20, 0.4) !important;
  backdrop-filter: blur(12px) !important;
  -webkit-backdrop-filter: blur(12px) !important;
  border-right: 1px solid rgba(255, 255, 255, 0.1) !important;
}
.ant-menu-item-selected {
  background: rgba(255, 255, 255, 0.2) !important;
}

/* 3. 首页数据卡片透明化 */
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
  background: rgba(0, 0, 0, 0.5) !important; /* 表头稍微深一点 */
  color: #ffffff !important;
  border-bottom: 1px solid rgba(255, 255, 255, 0.1) !important;
}
.ant-table-tbody > tr > td {
  background: rgba(20, 20, 20, 0.4) !important; /* 表格行半透明 */
  color: #eeeeee !important;
  border-bottom: 1px solid rgba(255, 255, 255, 0.05) !important;
}
.ant-table-tbody > tr:hover > td {
  background: rgba(255, 255, 255, 0.15) !important; /* 鼠标悬浮发亮 */
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

/* 6. 强制全局所有文字变亮，防止背景吃字 */
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

/* 7. 输入框、选择框透明 */
.ant-input,
.ant-input-password,
.ant-select-selector,
.ant-select-dropdown {
  background: rgba(0, 0, 0, 0.4) !important;
  color: #ffffff !important;
  border: 1px solid rgba(255, 255, 255, 0.2) !important;
}
.ant-select-selection-item {
  color: #ffffff !important;
}
</style>
