<script setup lang="ts">
import { ref, reactive, computed } from 'vue'
import { ElMessage, ElMessageBox } from 'element-plus'
import type { FormInstance } from 'element-plus'
import {
  Plus, Search, Upload, Download, Stamp, DataAnalysis,
  Warning, Clock,
} from '@element-plus/icons-vue'

interface StatItem {
  label: string
  value: string | number
  trend: string
  trendType: 'up' | 'down' | 'flat'
  icon: string
  color: string
}

interface TodoItem {
  id: number
  title: string
  type: string
  deadline: string
  status: 'pending' | 'processing' | 'done'
}

interface RecentRecord {
  id: number
  name: string
  category: string
  operator: string
  time: string
  status: 'success' | 'warning' | 'danger'
}

const stats = reactive<StatItem[]>([
  { label: '今日订单', value: 128, trend: '+12.5%', trendType: 'up', icon: 'Document', color: '#165dff' },
  { label: '活跃客户', value: 1563, trend: '+3.2%', trendType: 'up', icon: 'User', color: '#0fc6c2' },
  { label: '本月营收', value: '¥52.8万', trend: '+8.7%', trendType: 'up', icon: 'TrendCharts', color: '#f7ba1e' },
  { label: '待处理工单', value: 23, trend: '-5.1%', trendType: 'down', icon: 'Tickets', color: '#f53f3f' },
])

const todoList = reactive<TodoItem[]>([
  { id: 1, title: '审核采购订单 #PO-20260518-001', type: '订单审核', deadline: '2026-05-18', status: 'pending' },
  { id: 2, title: '客户报价单确认 - 华科科技', type: '报价确认', deadline: '2026-05-18', status: 'processing' },
  { id: 3, title: '库存预警处理 - A类物料不足', type: '库存预警', deadline: '2026-05-19', status: 'pending' },
  { id: 4, title: '合同续签 - 盛达物流', type: '合同管理', deadline: '2026-05-20', status: 'pending' },
  { id: 5, title: '月度报表提交', type: '报表任务', deadline: '2026-05-20', status: 'processing' },
])

const recentRecords = reactive<RecentRecord[]>([
  { id: 1, name: '采购订单 #PO-20260518-003', category: '采购管理', operator: '张三', time: '10分钟前', status: 'success' },
  { id: 2, name: '客户回访 - 鑫源电子', category: '客户管理', operator: '李四', time: '30分钟前', status: 'success' },
  { id: 3, name: '出库单 #WH-20260518-012', category: '仓储管理', operator: '王五', time: '1小时前', status: 'warning' },
  { id: 4, name: '合同签署 - 海纳科技', category: '合同管理', operator: '赵六', time: '2小时前', status: 'success' },
  { id: 5, name: '退款申请 #RF-20260518-007', category: '财务审核', operator: '钱七', time: '3小时前', status: 'danger' },
  { id: 6, name: '报价单 #QT-20260518-015', category: '销售管理', operator: '孙八', time: '4小时前', status: 'success' },
])

const statusMap: Record<string, string> = {
  pending: '待处理',
  processing: '进行中',
  done: '已完成',
}

const statusTypeMap: Record<string, string> = {
  pending: 'danger',
  processing: 'warning',
  done: 'success',
}

const recordStatusMap: Record<string, string> = {
  success: '已完成',
  warning: '处理中',
  danger: '异常',
}

const addDialogVisible = ref(false)
const queryDialogVisible = ref(false)
const importDialogVisible = ref(false)

const addFormRef = ref<FormInstance>()
const addForm = reactive({
  type: '',
  name: '',
  customer: '',
  amount: '',
  remark: '',
})
const addFormRules = reactive({
  type: [{ required: true, message: '请选择类型', trigger: 'change' }],
  name: [{ required: true, message: '请输入名称', trigger: 'blur' }],
  customer: [{ required: true, message: '请输入客户', trigger: 'blur' }],
  amount: [{ required: true, message: '请输入金额', trigger: 'blur' }],
})

const typeOptions = [
  { label: '采购订单', value: 'purchase' },
  { label: '销售订单', value: 'sales' },
  { label: '报价单', value: 'quote' },
  { label: '出库单', value: 'outbound' },
  { label: '入库单', value: 'inbound' },
]

const queryForm = reactive({
  keyword: '',
  category: '',
  dateRange: [] as string[],
})
const categoryOptions = [
  { label: '采购管理', value: 'purchase' },
  { label: '销售管理', value: 'sales' },
  { label: '客户管理', value: 'customer' },
  { label: '仓储管理', value: 'warehouse' },
  { label: '合同管理', value: 'contract' },
  { label: '财务审核', value: 'finance' },
]
const queryResults = ref<RecentRecord[]>([])
const hasQueried = ref(false)

const importFileList = ref<any[]>([])
const importProgress = ref(0)
const isImporting = ref(false)

const handleAddSubmit = async () => {
  if (!addFormRef.value) return
  await addFormRef.value.validate((valid) => {
    if (valid) {
      ElMessage.success(`新增${addForm.type}成功：${addForm.name}`)
      recentRecords.unshift({
        id: Date.now(),
        name: `${addForm.type} - ${addForm.name}`,
        category: addForm.type,
        operator: '当前用户',
        time: '刚刚',
        status: 'success',
      })
      addDialogVisible.value = false
      addFormRef.value?.resetFields()
    }
  })
}

const handleQuery = () => {
  hasQueried.value = true
  let results = [...recentRecords]
  if (queryForm.keyword) {
    const kw = queryForm.keyword.toLowerCase()
    results = results.filter(r => r.name.toLowerCase().includes(kw) || r.operator.includes(kw))
  }
  if (queryForm.category) {
    const catLabel = categoryOptions.find(c => c.value === queryForm.category)?.label || ''
    results = results.filter(r => r.category === catLabel)
  }
  queryResults.value = results
}

const resetQuery = () => {
  queryForm.keyword = ''
  queryForm.category = ''
  queryForm.dateRange = []
  queryResults.value = []
  hasQueried.value = false
}

const handleImportSubmit = async () => {
  if (importFileList.value.length === 0) {
    ElMessage.warning('请先选择要导入的文件')
    return
  }
  isImporting.value = true
  importProgress.value = 0
  const timer = setInterval(() => {
    importProgress.value += 10
    if (importProgress.value >= 100) {
      clearInterval(timer)
      isImporting.value = false
      ElMessage.success('文件导入成功！共导入 15 条记录')
      importFileList.value = []
      importProgress.value = 0
      importDialogVisible.value = false
    }
  }, 200)
}

const handleFileChange = (_file: any, fileList: any[]) => {
  importFileList.value = fileList.slice(-1)
}

const handleTodoAction = (item: TodoItem) => {
  ElMessageBox.confirm(`确认处理：${item.title}？`, '处理待办', {
    confirmButtonText: '确认处理',
    cancelButtonText: '取消',
    type: 'info',
  }).then(() => {
    item.status = 'processing'
    ElMessage.success('已开始处理')
  }).catch(() => {})
}

const handleRecordClick = (record: RecentRecord) => {
  ElMessage.info(`查看详情：${record.name}`)
}

const pendingTodoCount = computed(() => todoList.filter(t => t.status === 'pending').length)
</script>

<template>
  <div class="dashboard">
    <div class="section-header">
      <h2 class="greeting">工作台</h2>
      <span class="date-info">{{ new Date().toLocaleDateString('zh-CN', { year: 'numeric', month: 'long', day: 'numeric', weekday: 'long' }) }}</span>
    </div>

    <div class="stats-row">
      <div v-for="stat in stats" :key="stat.label" class="stat-card">
        <div class="stat-icon" :style="{ backgroundColor: stat.color + '15', color: stat.color }">
          <el-icon :size="28"><component :is="stat.icon" /></el-icon>
        </div>
        <div class="stat-info">
          <div class="stat-value">{{ stat.value }}</div>
          <div class="stat-label">{{ stat.label }}</div>
        </div>
        <div class="stat-trend" :class="stat.trendType">
          {{ stat.trend }}
        </div>
      </div>
    </div>

    <div class="action-row">
      <div class="action-card" @click="addDialogVisible = true">
        <div class="action-icon" style="background-color: #e8f3ff; color: #165dff;">
          <el-icon :size="24"><Plus /></el-icon>
        </div>
        <span class="action-label">新增记录</span>
      </div>
      <div class="action-card" @click="queryDialogVisible = true">
        <div class="action-icon" style="background-color: #e8fffb; color: #0fc6c2;">
          <el-icon :size="24"><Search /></el-icon>
        </div>
        <span class="action-label">数据查询</span>
      </div>
      <div class="action-card" @click="importDialogVisible = true">
        <div class="action-icon" style="background-color: #fff7e8; color: #f7ba1e;">
          <el-icon :size="24"><Upload /></el-icon>
        </div>
        <span class="action-label">数据导入</span>
      </div>
      <div class="action-card" @click="ElMessage.info('导出功能已触发')">
        <div class="action-icon" style="background-color: #e8ffea; color: #00b42a;">
          <el-icon :size="24"><Download /></el-icon>
        </div>
        <span class="action-label">数据导出</span>
      </div>
      <div class="action-card" @click="ElMessage.info('审批中心已打开')">
        <div class="action-icon" style="background-color: #ffe8e8; color: #f53f3f;">
          <el-icon :size="24"><Stamp /></el-icon>
        </div>
        <span class="action-label">审批中心</span>
      </div>
      <div class="action-card" @click="ElMessage.info('报表中心已打开')">
        <div class="action-icon" style="background-color: #f5e8ff; color: #722ed1;">
          <el-icon :size="24"><DataAnalysis /></el-icon>
        </div>
        <span class="action-label">报表中心</span>
      </div>
    </div>

    <div class="content-row">
      <div class="content-card todo-section">
        <div class="card-header">
          <div class="card-title">
            <el-icon :size="18" color="#f53f3f"><Warning /></el-icon>
            <span>待办事项</span>
            <el-badge :value="pendingTodoCount" :max="99" />
          </div>
          <el-button type="primary" link size="small">查看全部</el-button>
        </div>
        <div class="card-body">
          <div
            v-for="item in todoList"
            :key="item.id"
            class="todo-item"
          >
            <div class="todo-left">
              <el-tag :type="(statusTypeMap[item.status] as any)" size="small" effect="dark">
                {{ statusMap[item.status] }}
              </el-tag>
              <div class="todo-info">
                <span class="todo-title">{{ item.title }}</span>
                <span class="todo-meta">{{ item.type }} · 截止 {{ item.deadline }}</span>
              </div>
            </div>
            <el-button
              v-if="item.status === 'pending'"
              type="primary"
              size="small"
              round
              @click="handleTodoAction(item)"
            >
              处理
            </el-button>
          </div>
        </div>
      </div>

      <div class="content-card recent-section">
        <div class="card-header">
          <div class="card-title">
            <el-icon :size="18" color="#165dff"><Clock /></el-icon>
            <span>最近记录</span>
          </div>
          <el-button type="primary" link size="small">查看全部</el-button>
        </div>
        <div class="card-body">
          <el-table :data="recentRecords" stripe style="width: 100%" size="small" :header-cell-style="{ background: '#fafafa', color: '#1d2129', fontWeight: 600 }">
            <el-table-column prop="name" label="记录名称" min-width="200" show-overflow-tooltip>
              <template #default="{ row }">
                <span class="record-name" @click="handleRecordClick(row)">{{ row.name }}</span>
              </template>
            </el-table-column>
            <el-table-column prop="category" label="分类" width="100" />
            <el-table-column prop="operator" label="操作人" width="80" />
            <el-table-column prop="time" label="时间" width="100" />
            <el-table-column prop="status" label="状态" width="80" align="center">
              <template #default="{ row }">
                <el-tag :type="row.status" size="small" effect="light">
                  {{ recordStatusMap[row.status] }}
                </el-tag>
              </template>
            </el-table-column>
          </el-table>
        </div>
      </div>
    </div>

    <el-dialog v-model="addDialogVisible" title="新增记录" width="560px" destroy-on-close>
      <el-form ref="addFormRef" :model="addForm" :rules="addFormRules" label-width="80px" label-position="right">
        <el-form-item label="类型" prop="type">
          <el-select v-model="addForm.type" placeholder="请选择记录类型" style="width: 100%;">
            <el-option v-for="opt in typeOptions" :key="opt.value" :label="opt.label" :value="opt.label" />
          </el-select>
        </el-form-item>
        <el-form-item label="名称" prop="name">
          <el-input v-model="addForm.name" placeholder="请输入记录名称" />
        </el-form-item>
        <el-form-item label="客户" prop="customer">
          <el-input v-model="addForm.customer" placeholder="请输入客户名称" />
        </el-form-item>
        <el-form-item label="金额" prop="amount">
          <el-input v-model="addForm.amount" placeholder="请输入金额">
            <template #prepend>¥</template>
          </el-input>
        </el-form-item>
        <el-form-item label="备注" prop="remark">
          <el-input v-model="addForm.remark" type="textarea" :rows="3" placeholder="请输入备注信息" />
        </el-form-item>
      </el-form>
      <template #footer>
        <el-button @click="addDialogVisible = false">取消</el-button>
        <el-button type="primary" @click="handleAddSubmit">确认新增</el-button>
      </template>
    </el-dialog>

    <el-dialog v-model="queryDialogVisible" title="数据查询" width="680px" destroy-on-close>
      <div class="query-form">
        <el-form :model="queryForm" label-width="70px" inline>
          <el-form-item label="关键词">
            <el-input v-model="queryForm.keyword" placeholder="名称/操作人" clearable style="width: 180px;" />
          </el-form-item>
          <el-form-item label="分类">
            <el-select v-model="queryForm.category" placeholder="全部分类" clearable style="width: 150px;">
              <el-option v-for="opt in categoryOptions" :key="opt.value" :label="opt.label" :value="opt.value" />
            </el-select>
          </el-form-item>
          <el-form-item label="时间">
            <el-date-picker
              v-model="queryForm.dateRange"
              type="daterange"
              start-placeholder="开始日期"
              end-placeholder="结束日期"
              style="width: 240px;"
            />
          </el-form-item>
          <el-form-item>
            <el-button type="primary" @click="handleQuery">
              <el-icon><Search /></el-icon>查询
            </el-button>
            <el-button @click="resetQuery">重置</el-button>
          </el-form-item>
        </el-form>
      </div>
      <div v-if="hasQueried" class="query-results">
        <div v-if="queryResults.length > 0">
          <el-table :data="queryResults" stripe size="small" style="width: 100%;">
            <el-table-column prop="name" label="记录名称" min-width="200" show-overflow-tooltip />
            <el-table-column prop="category" label="分类" width="100" />
            <el-table-column prop="operator" label="操作人" width="80" />
            <el-table-column prop="time" label="时间" width="100" />
            <el-table-column prop="status" label="状态" width="80" align="center">
              <template #default="{ row }">
                <el-tag :type="row.status" size="small" effect="light">
                  {{ recordStatusMap[row.status] }}
                </el-tag>
              </template>
            </el-table-column>
          </el-table>
        </div>
        <el-empty v-else description="未查询到匹配的记录" :image-size="80" />
      </div>
      <div v-else class="query-placeholder">
        <el-icon :size="48" color="#c9cdd4"><Search /></el-icon>
        <p>输入条件后点击查询</p>
      </div>
    </el-dialog>

    <el-dialog v-model="importDialogVisible" title="数据导入" width="520px" destroy-on-close>
      <div class="import-area">
        <el-upload
          drag
          :auto-upload="false"
          :limit="1"
          accept=".xlsx,.xls,.csv"
          :on-change="handleFileChange"
          :file-list="importFileList"
        >
          <el-icon :size="48" color="#c9cdd4"><Upload /></el-icon>
          <div class="el-upload__text">将文件拖到此处，或<em>点击上传</em></div>
          <template #tip>
            <div class="el-upload__tip">支持 .xlsx / .xls / .csv 格式，单次最大 10MB</div>
          </template>
        </el-upload>
        <div v-if="isImporting" class="import-progress">
          <el-progress :percentage="importProgress" :stroke-width="8" />
          <p>正在导入数据，请稍候...</p>
        </div>
      </div>
      <template #footer>
        <el-button @click="importDialogVisible = false" :disabled="isImporting">取消</el-button>
        <el-button type="primary" @click="handleImportSubmit" :loading="isImporting">开始导入</el-button>
      </template>
    </el-dialog>
  </div>
</template>

<style scoped>
.dashboard {
  max-width: 1400px;
  margin: 0 auto;
}

.section-header {
  display: flex;
  align-items: baseline;
  gap: 16px;
  margin-bottom: 20px;
}

.greeting {
  font-size: 22px;
  font-weight: 600;
  color: #1d2129;
}

.date-info {
  font-size: 14px;
  color: #86909c;
}

.stats-row {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 16px;
  margin-bottom: 20px;
}

.stat-card {
  background: #fff;
  border-radius: 8px;
  padding: 20px;
  display: flex;
  align-items: center;
  gap: 16px;
  box-shadow: 0 1px 4px rgba(0, 0, 0, 0.06);
  transition: box-shadow 0.2s;
  position: relative;
  overflow: hidden;
}

.stat-card:hover {
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}

.stat-icon {
  width: 52px;
  height: 52px;
  border-radius: 12px;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
}

.stat-info {
  flex: 1;
  min-width: 0;
}

.stat-value {
  font-size: 24px;
  font-weight: 700;
  color: #1d2129;
  line-height: 1.2;
}

.stat-label {
  font-size: 13px;
  color: #86909c;
  margin-top: 2px;
}

.stat-trend {
  font-size: 12px;
  font-weight: 600;
  padding: 2px 8px;
  border-radius: 10px;
}

.stat-trend.up {
  color: #00b42a;
  background-color: #e8ffea;
}

.stat-trend.down {
  color: #f53f3f;
  background-color: #ffe8e8;
}

.stat-trend.flat {
  color: #86909c;
  background-color: #f2f3f5;
}

.action-row {
  display: grid;
  grid-template-columns: repeat(6, 1fr);
  gap: 16px;
  margin-bottom: 20px;
}

.action-card {
  background: #fff;
  border-radius: 8px;
  padding: 20px 16px;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 10px;
  cursor: pointer;
  box-shadow: 0 1px 4px rgba(0, 0, 0, 0.06);
  transition: all 0.2s;
}

.action-card:hover {
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  transform: translateY(-2px);
}

.action-icon {
  width: 48px;
  height: 48px;
  border-radius: 12px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.action-label {
  font-size: 14px;
  font-weight: 500;
  color: #1d2129;
}

.content-row {
  display: grid;
  grid-template-columns: 380px 1fr;
  gap: 16px;
}

.content-card {
  background: #fff;
  border-radius: 8px;
  box-shadow: 0 1px 4px rgba(0, 0, 0, 0.06);
  overflow: hidden;
}

.card-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 16px 20px;
  border-bottom: 1px solid #f2f3f5;
}

.card-title {
  display: flex;
  align-items: center;
  gap: 8px;
  font-size: 15px;
  font-weight: 600;
  color: #1d2129;
}

.card-body {
  padding: 12px 16px;
}

.todo-item {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 12px 8px;
  border-bottom: 1px solid #f7f8fa;
  transition: background-color 0.15s;
}

.todo-item:last-child {
  border-bottom: none;
}

.todo-item:hover {
  background-color: #f7f8fa;
}

.todo-left {
  display: flex;
  align-items: flex-start;
  gap: 10px;
  flex: 1;
  min-width: 0;
}

.todo-info {
  display: flex;
  flex-direction: column;
  gap: 2px;
}

.todo-title {
  font-size: 13px;
  color: #1d2129;
  line-height: 1.4;
}

.todo-meta {
  font-size: 12px;
  color: #c9cdd4;
}

.record-name {
  color: #165dff;
  cursor: pointer;
}

.record-name:hover {
  text-decoration: underline;
}

.query-form {
  margin-bottom: 16px;
}

.query-results {
  border-top: 1px solid #f2f3f5;
  padding-top: 16px;
}

.query-placeholder {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 40px 0;
  color: #c9cdd4;
  gap: 12px;
}

.query-placeholder p {
  font-size: 14px;
  color: #86909c;
}

.import-area {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.import-progress {
  width: 100%;
  margin-top: 16px;
  text-align: center;
}

.import-progress p {
  margin-top: 8px;
  font-size: 13px;
  color: #86909c;
}

@media (max-width: 1200px) {
  .stats-row {
    grid-template-columns: repeat(2, 1fr);
  }
  .action-row {
    grid-template-columns: repeat(3, 1fr);
  }
  .content-row {
    grid-template-columns: 1fr;
  }
}

@media (max-width: 768px) {
  .stats-row {
    grid-template-columns: 1fr;
  }
  .action-row {
    grid-template-columns: repeat(2, 1fr);
  }
}
</style>
