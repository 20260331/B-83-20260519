<script setup lang="ts">
import { ref } from 'vue'
import StatCard from './StatCard.vue'
import QuickActions from './QuickActions.vue'
import TodoList from './TodoList.vue'
import RecentRecords from './RecentRecords.vue'
import { ElMessage } from 'element-plus'
import { Search } from '@element-plus/icons-vue'

const searchQuery = ref('')

const handleSearch = () => {
  if (searchQuery.value.trim()) {
    ElMessage.success(`正在搜索: ${searchQuery.value}`)
  }
}
</script>

<template>
  <div class="dashboard">
    <header class="dashboard-header">
      <div class="header-left">
        <h1 class="title">业务工作台</h1>
        <span class="subtitle">高效管理，智能办公</span>
      </div>
      <div class="header-right">
        <div class="search-box">
          <el-input
            v-model="searchQuery"
            placeholder="搜索订单、客户、文档..."
            class="search-input"
            @keyup.enter="handleSearch"
          >
            <template #prefix>
              <el-icon><Search /></el-icon>
            </template>
          </el-input>
          <el-button type="primary" @click="handleSearch">搜索</el-button>
        </div>
        <div class="user-info">
          <el-avatar :size="40" src="https://cube.elemecdn.com/3/7c/3ea6beec64369c2642b92c6726f1epng.png" />
          <div class="user-detail">
            <span class="username">管理员</span>
            <span class="role">系统管理员</span>
          </div>
        </div>
      </div>
    </header>

    <main class="dashboard-main">
      <section class="stats-section">
        <StatCard
          title="今日订单"
          :value="128"
          unit="单"
          :trend="12.5"
          icon="ShoppingCart"
          color="#409EFF"
        />
        <StatCard
          title="今日销售额"
          :value="56890"
          unit="元"
          :trend="8.2"
          icon="Money"
          color="#67C23A"
        />
        <StatCard
          title="待处理工单"
          :value="23"
          unit="个"
          :trend="-5.3"
          icon="Warning"
          color="#E6A23C"
        />
        <StatCard
          title="活跃客户"
          :value="1856"
          unit="人"
          :trend="15.8"
          icon="User"
          color="#F56C6C"
        />
      </section>

      <section class="quick-section">
        <QuickActions />
      </section>

      <section class="content-section">
        <div class="content-left">
          <TodoList />
        </div>
        <div class="content-right">
          <RecentRecords />
        </div>
      </section>
    </main>
  </div>
</template>

<style scoped>
.dashboard {
  min-height: 100vh;
  background: linear-gradient(135deg, #f5f7fa 0%, #c3cfe2 100%);
  padding: 24px;
}

.dashboard-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 24px;
  padding: 20px 32px;
  background: white;
  border-radius: 12px;
  box-shadow: 0 2px 12px rgba(0, 0, 0, 0.08);
}

.header-left {
  display: flex;
  flex-direction: column;
}

.title {
  margin: 0;
  font-size: 28px;
  font-weight: 600;
  color: #303133;
}

.subtitle {
  font-size: 14px;
  color: #909399;
  margin-top: 4px;
}

.header-right {
  display: flex;
  align-items: center;
  gap: 24px;
}

.search-box {
  display: flex;
  gap: 8px;
}

.search-input {
  width: 320px;
}

.user-info {
  display: flex;
  align-items: center;
  gap: 12px;
  padding-left: 24px;
  border-left: 1px solid #ebeef5;
}

.user-detail {
  display: flex;
  flex-direction: column;
}

.username {
  font-size: 14px;
  font-weight: 500;
  color: #303133;
}

.role {
  font-size: 12px;
  color: #909399;
}

.dashboard-main {
  display: flex;
  flex-direction: column;
  gap: 24px;
}

.stats-section {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 20px;
}

.quick-section {
  background: white;
  border-radius: 12px;
  box-shadow: 0 2px 12px rgba(0, 0, 0, 0.08);
}

.content-section {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 24px;
}

.content-left,
.content-right {
  background: white;
  border-radius: 12px;
  box-shadow: 0 2px 12px rgba(0, 0, 0, 0.08);
  min-height: 400px;
}

@media (max-width: 1200px) {
  .stats-section {
    grid-template-columns: repeat(2, 1fr);
  }
  
  .content-section {
    grid-template-columns: 1fr;
  }
}

@media (max-width: 768px) {
  .dashboard {
    padding: 12px;
  }
  
  .dashboard-header {
    flex-direction: column;
    gap: 16px;
    padding: 16px;
  }
  
  .header-right {
    flex-direction: column;
    width: 100%;
  }
  
  .search-box {
    width: 100%;
  }
  
  .search-input {
    width: 100%;
  }
  
  .stats-section {
    grid-template-columns: 1fr;
  }
}
</style>
