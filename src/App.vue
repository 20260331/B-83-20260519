<script setup lang="ts">
import Dashboard from './components/Dashboard.vue'
import { DataBoard, Bell } from '@element-plus/icons-vue'
</script>

<template>
  <div class="workbench-layout">
    <header class="workbench-header">
      <div class="header-left">
        <div class="logo-area">
          <el-icon :size="24" color="#fff"><DataBoard /></el-icon>
          <span class="app-title">业务工作台</span>
        </div>
      </div>
      <div class="header-center">
        <el-input
          placeholder="搜索功能、数据、记录..."
          prefix-icon="Search"
          class="global-search"
          clearable
        />
      </div>
      <div class="header-right">
        <el-badge :value="3" :max="99" class="header-badge">
          <el-icon :size="20" color="#fff"><Bell /></el-icon>
        </el-badge>
        <el-avatar :size="32" class="user-avatar">管</el-avatar>
      </div>
    </header>
    <main class="workbench-main">
      <Dashboard />
    </main>
  </div>
</template>

<style scoped>
.workbench-layout {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
  background-color: #f2f3f5;
}

.workbench-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  height: 56px;
  padding: 0 24px;
  background: linear-gradient(135deg, #165dff 0%, #0e42d2 100%);
  box-shadow: 0 2px 8px rgba(22, 93, 255, 0.25);
  flex-shrink: 0;
}

.header-left {
  display: flex;
  align-items: center;
  gap: 12px;
}

.logo-area {
  display: flex;
  align-items: center;
  gap: 8px;
}

.app-title {
  font-size: 18px;
  font-weight: 600;
  color: #fff;
  letter-spacing: 1px;
}

.header-center {
  flex: 1;
  max-width: 480px;
  margin: 0 32px;
}

.global-search :deep(.el-input__wrapper) {
  border-radius: 20px;
  background-color: rgba(255, 255, 255, 0.15);
  box-shadow: none;
  border: 1px solid rgba(255, 255, 255, 0.2);
}

.global-search :deep(.el-input__wrapper:hover),
.global-search :deep(.el-input__wrapper.is-focus) {
  background-color: rgba(255, 255, 255, 0.25);
  border-color: rgba(255, 255, 255, 0.4);
}

.global-search :deep(.el-input__inner) {
  color: #fff;
}

.global-search :deep(.el-input__inner::placeholder) {
  color: rgba(255, 255, 255, 0.6);
}

.global-search :deep(.el-input__prefix .el-icon) {
  color: rgba(255, 255, 255, 0.7);
}

.header-right {
  display: flex;
  align-items: center;
  gap: 20px;
}

.header-badge {
  cursor: pointer;
}

.user-avatar {
  background-color: rgba(255, 255, 255, 0.2);
  color: #fff;
  font-size: 14px;
  cursor: pointer;
}

.workbench-main {
  flex: 1;
  padding: 20px 24px;
  overflow-y: auto;
}
</style>
