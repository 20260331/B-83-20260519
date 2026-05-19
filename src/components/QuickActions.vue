<script setup lang="ts">
import { ref } from 'vue'
import { ElMessage, ElMessageBox } from 'element-plus'
import {
  Plus,
  Search,
  Upload,
  Download,
  Setting,
  DataAnalysis,
  Goods,
  UserFilled
} from '@element-plus/icons-vue'

const actions = [
  { name: '新增订单', icon: Plus, color: '#409EFF', action: 'addOrder' },
  { name: '新增客户', icon: UserFilled, color: '#67C23A', action: 'addCustomer' },
  { name: '新增商品', icon: Goods, color: '#E6A23C', action: 'addProduct' },
  { name: '查询订单', icon: Search, color: '#F56C6C', action: 'searchOrder' },
  { name: '导入数据', icon: Upload, color: '#909399', action: 'importData' },
  { name: '导出报表', icon: Download, color: '#409EFF', action: 'exportReport' },
  { name: '数据分析', icon: DataAnalysis, color: '#67C23A', action: 'dataAnalysis' },
  { name: '系统设置', icon: Setting, color: '#E6A23C', action: 'systemSetting' }
]

const dialogVisible = ref(false)
const currentAction = ref('')
const formData = ref({
  orderNo: '',
  customer: '',
  amount: '',
  product: '',
  quantity: ''
})

const handleAction = (action: string, name: string) => {
  currentAction.value = name
  if (action === 'importData') {
    ElMessage.success('正在打开文件选择器...')
  } else if (action === 'exportReport') {
    ElMessage.success('报表正在生成中，请稍候...')
  } else if (action === 'searchOrder') {
    ElMessage.success('正在跳转到订单查询页面...')
  } else if (action === 'dataAnalysis') {
    ElMessage.success('正在加载数据分析面板...')
  } else if (action === 'systemSetting') {
    ElMessage.success('正在打开系统设置...')
  } else {
    dialogVisible.value = true
  }
}

const handleSubmit = () => {
  ElMessageBox.confirm(
    `确认要${currentAction.value}吗？`,
    '提示',
    {
      confirmButtonText: '确定',
      cancelButtonText: '取消',
      type: 'info'
    }
  ).then(() => {
    ElMessage.success(`${currentAction.value}成功！`)
    dialogVisible.value = false
    formData.value = {
      orderNo: '',
      customer: '',
      amount: '',
      product: '',
      quantity: ''
    }
  }).catch(() => {})
}
</script>

<template>
  <div class="quick-actions">
    <div class="section-header">
      <h3 class="section-title">快捷操作</h3>
      <span class="section-desc">常用功能一键直达</span>
    </div>
    <div class="actions-grid">
      <div
        v-for="(item, index) in actions"
        :key="index"
        class="action-item"
        @click="handleAction(item.action, item.name)"
      >
        <div class="action-icon" :style="{ backgroundColor: item.color + '15', color: item.color }">
          <el-icon :size="28">
            <component :is="item.icon" />
          </el-icon>
        </div>
        <span class="action-name">{{ item.name }}</span>
      </div>
    </div>

    <el-dialog
      v-model="dialogVisible"
      :title="currentAction"
      width="500px"
      :close-on-click-modal="false"
    >
      <el-form :model="formData" label-width="80px">
        <el-form-item label="订单编号" v-if="currentAction.includes('订单')">
          <el-input v-model="formData.orderNo" placeholder="请输入订单编号" />
        </el-form-item>
        <el-form-item label="客户名称">
          <el-input v-model="formData.customer" placeholder="请输入客户名称" />
        </el-form-item>
        <el-form-item label="商品名称" v-if="currentAction.includes('商品') || currentAction.includes('订单')">
          <el-input v-model="formData.product" placeholder="请输入商品名称" />
        </el-form-item>
        <el-form-item label="数量" v-if="currentAction.includes('商品') || currentAction.includes('订单')">
          <el-input-number v-model="formData.quantity" :min="1" />
        </el-form-item>
        <el-form-item label="金额" v-if="currentAction.includes('订单')">
          <el-input v-model="formData.amount" placeholder="请输入金额">
            <template #prefix>¥</template>
          </el-input>
        </el-form-item>
      </el-form>
      <template #footer>
        <el-button @click="dialogVisible = false">取消</el-button>
        <el-button type="primary" @click="handleSubmit">确定</el-button>
      </template>
    </el-dialog>
  </div>
</template>

<style scoped>
.quick-actions {
  padding: 24px;
}

.section-header {
  margin-bottom: 20px;
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
  margin-left: 8px;
}

.actions-grid {
  display: grid;
  grid-template-columns: repeat(8, 1fr);
  gap: 20px;
}

.action-item {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 12px;
  padding: 20px 12px;
  border-radius: 12px;
  cursor: pointer;
  transition: all 0.3s ease;
}

.action-item:hover {
  background: #f5f7fa;
  transform: translateY(-2px);
}

.action-icon {
  width: 56px;
  height: 56px;
  border-radius: 12px;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.3s ease;
}

.action-item:hover .action-icon {
  transform: scale(1.1);
}

.action-name {
  font-size: 13px;
  color: #606266;
  text-align: center;
}

@media (max-width: 1200px) {
  .actions-grid {
    grid-template-columns: repeat(4, 1fr);
  }
}

@media (max-width: 768px) {
  .actions-grid {
    grid-template-columns: repeat(2, 1fr);
  }
}
</style>
