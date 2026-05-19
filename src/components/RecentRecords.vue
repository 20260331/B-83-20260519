<script setup lang="ts">
import { ref } from 'vue'
import { ElMessage, ElMessageBox } from 'element-plus'
import {
  Document,
  ShoppingCart,
  User,
  Goods,
  View,
  Edit,
  Delete,
  Refresh,
  MoreFilled,
  Clock
} from '@element-plus/icons-vue'

interface RecordItem {
  id: number
  type: 'order' | 'customer' | 'product' | 'invoice'
  title: string
  description: string
  amount?: string
  status: string
  statusType: 'success' | 'warning' | 'info' | 'danger' | 'primary'
  time: string
  operator: string
}

const records = ref<RecordItem[]>([
  {
    id: 1,
    type: 'order',
    title: '订单 #20240115008',
    description: '客户：张三 - 商品：笔记本电脑 x2',
    amount: '¥12,999.00',
    status: '已完成',
    statusType: 'success',
    time: '10分钟前',
    operator: '李四'
  },
  {
    id: 2,
    type: 'customer',
    title: '新增客户：王总科技',
    description: '联系人：王经理 - 电话：138****8888',
    status: '有效',
    statusType: 'success',
    time: '30分钟前',
    operator: '张三'
  },
  {
    id: 3,
    type: 'product',
    title: '库存预警：无线鼠标',
    description: '当前库存：12个 - 安全库存：50个',
    status: '库存不足',
    statusType: 'warning',
    time: '1小时前',
    operator: '系统'
  },
  {
    id: 4,
    type: 'invoice',
    title: '发票申请 #INV20240115',
    description: '发票金额：¥8,500.00 - 发票类型：增值税专用发票',
    status: '待审核',
    statusType: 'warning',
    time: '2小时前',
    operator: '赵六'
  },
  {
    id: 5,
    type: 'order',
    title: '订单 #20240115007',
    description: '客户：李总贸易 - 商品：机械键盘 x5',
    amount: '¥2,495.00',
    status: '待发货',
    statusType: 'primary',
    time: '3小时前',
    operator: '王五'
  },
  {
    id: 6,
    type: 'customer',
    title: '客户跟进：华信集团',
    description: '跟进记录：确认订单细节，预计下周签约',
    status: '跟进中',
    statusType: 'info',
    time: '5小时前',
    operator: '张三'
  }
])

const typeConfig = {
  order: { icon: ShoppingCart, color: '#409EFF', bgColor: '#ECF5FF' },
  customer: { icon: User, color: '#67C23A', bgColor: '#F0F9EB' },
  product: { icon: Goods, color: '#E6A23C', bgColor: '#FDF6EC' },
  invoice: { icon: Document, color: '#F56C6C', bgColor: '#FEF0F0' }
}

const handleView = (record: RecordItem) => {
  ElMessage.success(`正在查看：${record.title}`)
}

const handleEdit = (record: RecordItem) => {
  ElMessage.success(`正在编辑：${record.title}`)
}

const handleDelete = (record: RecordItem) => {
  ElMessageBox.confirm(`确定要删除 "${record.title}" 吗？`, '提示', {
    confirmButtonText: '确定',
    cancelButtonText: '取消',
    type: 'warning'
  }).then(() => {
    records.value = records.value.filter(r => r.id !== record.id)
    ElMessage.success('删除成功')
  }).catch(() => {})
}

const handleRefresh = () => {
  ElMessage.success('数据已刷新')
}

const getTypeStyle = (type: RecordItem['type']) => {
  return typeConfig[type]
}
</script>

<template>
  <div class="recent-records">
    <div class="section-header">
      <div class="header-left">
        <h3 class="section-title">最近记录</h3>
        <span class="section-desc">最新动态一目了然</span>
      </div>
      <div class="header-actions">
        <el-button :icon="Refresh" size="small" @click="handleRefresh">刷新</el-button>
        <el-button type="primary" size="small">查看全部</el-button>
      </div>
    </div>

    <div class="records-list">
      <div
        v-for="record in records"
        :key="record.id"
        class="record-item"
      >
        <div class="record-icon" :style="{ backgroundColor: getTypeStyle(record.type).bgColor, color: getTypeStyle(record.type).color }">
          <el-icon :size="20">
            <component :is="getTypeStyle(record.type).icon" />
          </el-icon>
        </div>
        <div class="record-content">
          <div class="record-header">
            <span class="record-title">{{ record.title }}</span>
            <el-tag :type="record.statusType" size="small">{{ record.status }}</el-tag>
          </div>
          <p class="record-desc">{{ record.description }}</p>
          <div class="record-footer">
            <span class="record-amount" v-if="record.amount">{{ record.amount }}</span>
            <span class="record-time">
              <el-icon><Clock /></el-icon>
              {{ record.time }}
            </span>
            <span class="record-operator">
              <el-icon><User /></el-icon>
              {{ record.operator }}
            </span>
          </div>
        </div>
        <div class="record-actions">
          <el-dropdown trigger="click">
            <el-button type="primary" link :icon="MoreFilled" />
            <template #dropdown>
              <el-dropdown-menu>
                <el-dropdown-item @click="handleView(record)">
                  <el-icon><View /></el-icon>
                  查看详情
                </el-dropdown-item>
                <el-dropdown-item @click="handleEdit(record)">
                  <el-icon><Edit /></el-icon>
                  编辑
                </el-dropdown-item>
                <el-dropdown-item divided @click="handleDelete(record)">
                  <el-icon><Delete /></el-icon>
                  删除
                </el-dropdown-item>
              </el-dropdown-menu>
            </template>
          </el-dropdown>
        </div>
      </div>
    </div>

    <div v-if="records.length === 0" class="empty-state">
      <el-empty description="暂无记录" />
    </div>
  </div>
</template>

<style scoped>
.recent-records {
  padding: 24px;
  height: 100%;
  display: flex;
  flex-direction: column;
}

.section-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 16px;
}

.header-left {
  display: flex;
  align-items: center;
  gap: 8px;
}

.section-title {
  margin: 0;
  font-size: 18px;
  font-weight: 600;
  color: #303133;
}

.section-desc {
  font-size: 13px;
  color: #909399;
}

.header-actions {
  display: flex;
  gap: 8px;
}

.records-list {
  flex: 1;
  overflow-y: auto;
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.record-item {
  display: flex;
  gap: 16px;
  padding: 16px;
  background: #fafafa;
  border-radius: 8px;
  transition: all 0.3s ease;
}

.record-item:hover {
  background: #f5f7fa;
}

.record-icon {
  width: 44px;
  height: 44px;
  border-radius: 8px;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
}

.record-content {
  flex: 1;
  min-width: 0;
}

.record-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 6px;
}

.record-title {
  font-size: 14px;
  font-weight: 500;
  color: #303133;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}

.record-desc {
  margin: 0 0 8px 0;
  font-size: 13px;
  color: #606266;
  overflow: hidden;
  text-overflow: ellipsis;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
}

.record-footer {
  display: flex;
  align-items: center;
  gap: 16px;
  flex-wrap: wrap;
}

.record-amount {
  font-size: 14px;
  font-weight: 600;
  color: #F56C6C;
}

.record-time,
.record-operator {
  display: flex;
  align-items: center;
  gap: 4px;
  font-size: 12px;
  color: #909399;
}

.record-actions {
  flex-shrink: 0;
}

.empty-state {
  flex: 1;
  display: flex;
  align-items: center;
  justify-content: center;
}
</style>
