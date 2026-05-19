<script setup lang="ts">
import { ref, reactive, computed } from 'vue'
import { ElMessage } from 'element-plus'
import { UploadFilled } from '@element-plus/icons-vue'

interface StatCard {
  title: string
  value: string | number
  trend: string
  trendType: 'up' | 'down' | 'normal'
  icon: string
  color: string
}

interface TodoItem {
  id: number
  title: string
  type: string
  typeColor: string
  time: string
  status: 'pending' | 'done'
}

interface RecentRecord {
  id: number
  title: string
  category: string
  user: string
  time: string
  status: string
  statusColor: string
}

interface QuickAction {
  icon: string
  name: string
  color: string
  actionKey: string
}

interface BusinessRecord {
  id: number
  name: string
  type: string
  amount: number
  owner: string
  status: string
  createTime: string
}

const activeTab = ref('overview')

const stats = ref<StatCard[]>([
  { title: '今日新增客户', value: 128, trend: '+12.5%', trendType: 'up', icon: '👥', color: '#409EFF' },
  { title: '待处理订单', value: 36, trend: '-8.2%', trendType: 'down', icon: '📋', color: '#E6A23C' },
  { title: '本月销售额', value: '¥286,540', trend: '+23.8%', trendType: 'up', icon: '💰', color: '#67C23A' },
  { title: '异常预警', value: 8, trend: '持平', trendType: 'normal', icon: '⚠️', color: '#F56C6C' }
])

const todos = ref<TodoItem[]>([
  { id: 1, title: '审核新客户注册申请', type: '审批', typeColor: '#409EFF', time: '10分钟前', status: 'pending' },
  { id: 2, title: '跟进大客户合同续签', type: '跟进', typeColor: '#E6A23C', time: '30分钟前', status: 'pending' },
  { id: 3, title: '月度数据报表整理', type: '报表', typeColor: '#67C23A', time: '2小时前', status: 'pending' },
  { id: 4, title: '回复客户投诉工单 #8821', type: '工单', typeColor: '#F56C6C', time: '昨天', status: 'done' },
  { id: 5, title: '系统版本升级通知', type: '系统', typeColor: '#909399', time: '2天前', status: 'done' }
])

const recentRecords = ref<RecentRecord[]>([
  { id: 1, title: '深圳科技有限公司', category: '客户信息', user: '张三', time: '5分钟前', status: '已更新', statusColor: '#67C23A' },
  { id: 2, title: '采购订单 PO-20260519-001', category: '采购订单', user: '李四', time: '15分钟前', status: '待审批', statusColor: '#E6A23C' },
  { id: 3, title: '销售合同 HT-2026-0452', category: '销售合同', user: '王五', time: '1小时前', status: '已签订', statusColor: '#67C23A' },
  { id: 4, title: '客户回访记录', category: '客户服务', user: '赵六', time: '2小时前', status: '处理中', statusColor: '#409EFF' }
])

const quickActions = ref<QuickAction[]>([
  { icon: '➕', name: '新增客户', color: '#409EFF', actionKey: 'addCustomer' },
  { icon: '📝', name: '新增订单', color: '#67C23A', actionKey: 'addOrder' },
  { icon: '🔍', name: '数据查询', color: '#E6A23C', actionKey: 'queryData' },
  { icon: '📥', name: '批量导入', color: '#909399', actionKey: 'importData' },
  { icon: '📤', name: '数据导出', color: '#F56C6C', actionKey: 'exportData' },
  { icon: '📊', name: '报表分析', color: '#409EFF', actionKey: 'reportAnalysis' },
  { icon: '⚙️', name: '系统设置', color: '#67C23A', actionKey: 'settings' },
  { icon: '🔔', name: '消息中心', color: '#E6A23C', actionKey: 'messages' }
])

const businessRecords = ref<BusinessRecord[]>([
  { id: 1, name: '深圳科技有限公司', type: '客户', amount: 0, owner: '张三', status: '活跃', createTime: '2026-05-19 09:30' },
  { id: 2, name: '采购订单 PO-001', type: '订单', amount: 125800, owner: '李四', status: '待审批', createTime: '2026-05-19 10:15' },
  { id: 3, name: '销售合同 HT-0452', type: '合同', amount: 586000, owner: '王五', status: '已签订', createTime: '2026-05-18 16:42' },
  { id: 4, name: '上海贸易集团', type: '客户', amount: 0, owner: '赵六', status: '潜在', createTime: '2026-05-18 14:20' },
  { id: 5, name: '采购订单 PO-002', type: '订单', amount: 89600, owner: '张三', status: '已完成', createTime: '2026-05-18 11:08' }
])

const searchForm = reactive({
  keyword: '',
  type: '',
  status: '',
  dateRange: [] as string[]
})

const addCustomerVisible = ref(false)
const addOrderVisible = ref(false)
const importVisible = ref(false)

const customerForm = reactive({
  name: '',
  type: '普通客户',
  phone: '',
  email: '',
  address: ''
})

const orderForm = reactive({
  orderNo: '',
  customer: '',
  type: '采购',
  amount: 0,
  description: ''
})

const importForm = reactive({
  type: '客户数据',
  file: null as File | null,
  sheetName: ''
})

const filteredRecords = computed(() => {
  let result = businessRecords.value
  if (searchForm.keyword) {
    const kw = searchForm.keyword.toLowerCase()
    result = result.filter(r => r.name.toLowerCase().includes(kw) || r.owner.includes(searchForm.keyword))
  }
  if (searchForm.type) {
    result = result.filter(r => r.type === searchForm.type)
  }
  if (searchForm.status) {
    result = result.filter(r => r.status === searchForm.status)
  }
  return result
})

const pendingTodoCount = computed(() => todos.value.filter(t => t.status === 'pending').length)

const handleQuickAction = (action: QuickAction) => {
  switch (action.actionKey) {
    case 'addCustomer':
      addCustomerVisible.value = true
      break
    case 'addOrder':
      addOrderVisible.value = true
      break
    case 'queryData':
      activeTab.value = 'query'
      ElMessage.info('已切换到数据查询')
      break
    case 'importData':
      importVisible.value = true
      break
    case 'exportData':
      ElMessage.success('数据导出任务已启动，完成后将收到通知')
      break
    case 'reportAnalysis':
      ElMessage.info('报表分析模块正在加载...')
      break
    case 'settings':
      ElMessage.info('系统设置入口')
      break
    case 'messages':
      ElMessage.info('您有 3 条未读消息')
      break
  }
}

const submitCustomer = () => {
  if (!customerForm.name) {
    ElMessage.warning('请输入客户名称')
    return
  }
  const newRecord: BusinessRecord = {
    id: businessRecords.value.length + 1,
    name: customerForm.name,
    type: '客户',
    amount: 0,
    owner: '当前用户',
    status: '潜在',
    createTime: new Date().toLocaleString('zh-CN')
  }
  businessRecords.value.unshift(newRecord)
  todos.value.unshift({
    id: todos.value.length + 1,
    title: `跟进新客户：${customerForm.name}`,
    type: '跟进',
    typeColor: '#E6A23C',
    time: '刚刚',
    status: 'pending'
  })
  ElMessage.success('客户新增成功')
  addCustomerVisible.value = false
  Object.assign(customerForm, { name: '', type: '普通客户', phone: '', email: '', address: '' })
}

const submitOrder = () => {
  if (!orderForm.customer || orderForm.amount <= 0) {
    ElMessage.warning('请填写客户和订单金额')
    return
  }
  const newRecord: BusinessRecord = {
    id: businessRecords.value.length + 1,
    name: `订单-${orderForm.orderNo || `ORD-${Date.now().toString().slice(-6)}`}`,
    type: '订单',
    amount: orderForm.amount,
    owner: '当前用户',
    status: '待审批',
    createTime: new Date().toLocaleString('zh-CN')
  }
  businessRecords.value.unshift(newRecord)
  if (stats.value[1]) {
    const currentVal = Number(stats.value[1].value) || 0
    stats.value[1].value = (currentVal + 1).toString()
  }
  ElMessage.success('订单已提交待审批')
  addOrderVisible.value = false
  Object.assign(orderForm, { orderNo: '', customer: '', type: '采购', amount: 0, description: '' })
}

const handleImport = () => {
  if (!importForm.file) {
    ElMessage.warning('请先选择要导入的文件')
    return
  }
  ElMessage.success(`已开始导入 ${importForm.type}，共解析到 ${Math.floor(Math.random() * 50) + 10} 条数据`)
  importVisible.value = false
  importForm.file = null
}

const onFileChange = (file: File) => {
  importForm.file = file
}

const completeTodo = (todo: TodoItem) => {
  todo.status = 'done'
  ElMessage.success(`已完成：${todo.title}`)
}

const handleSearch = () => {
  ElMessage.success(`查询完成，共 ${filteredRecords.value.length} 条结果`)
}

const resetSearch = () => {
  searchForm.keyword = ''
  searchForm.type = ''
  searchForm.status = ''
  searchForm.dateRange = []
}

const handleExport = () => {
  if (filteredRecords.value.length === 0) {
    ElMessage.warning('没有可导出的数据')
    return
  }
  ElMessage.success(`已导出 ${filteredRecords.value.length} 条数据`)
}

const statusType = (status: string) => {
  const map: Record<string, string> = {
    '活跃': 'success',
    '潜在': 'info',
    '待审批': 'warning',
    '已签订': 'success',
    '已完成': 'success',
    '处理中': 'primary',
    '已更新': 'success'
  }
  return map[status] || 'info'
}

const typeOptions = ['客户', '订单', '合同', '供应商']
const statusOptions = ['活跃', '潜在', '待审批', '已签订', '已完成', '处理中']
</script>

<template>
  <div class="workbench">
    <header class="wb-header">
      <div class="wb-header-left">
        <div class="logo-box">
          <span class="logo-icon">🏢</span>
          <span class="logo-text">企业业务工作台</span>
        </div>
        <nav class="main-nav">
          <div
            v-for="tab in [{ key: 'overview', name: '数据概览' }, { key: 'query', name: '业务查询' }, { key: 'stats', name: '统计分析' }]"
            :key="tab.key"
            :class="['nav-item', { active: activeTab === tab.key }]"
            @click="activeTab = tab.key"
          >
            {{ tab.name }}
          </div>
        </nav>
      </div>
      <div class="wb-header-right">
        <el-badge :value="pendingTodoCount" :max="99" class="header-badge">
          <span class="header-icon">🔔</span>
        </el-badge>
        <div class="user-info">
          <div class="avatar">管</div>
          <div class="user-detail">
            <div class="user-name">管理员</div>
            <div class="user-role">系统管理员</div>
          </div>
        </div>
      </div>
    </header>

    <main class="wb-main">
      <div v-if="activeTab === 'overview'" class="tab-overview">
        <section class="stats-section">
          <div class="section-header">
            <h3>核心数据概览</h3>
            <span class="section-sub">数据更新于 {{ new Date().toLocaleTimeString('zh-CN') }}</span>
          </div>
          <div class="stats-grid">
            <div v-for="stat in stats" :key="stat.title" class="stat-card" :style="{ borderTopColor: stat.color }">
              <div class="stat-icon" :style="{ backgroundColor: stat.color + '15', color: stat.color }">
                {{ stat.icon }}
              </div>
              <div class="stat-info">
                <div class="stat-value">{{ stat.value }}</div>
                <div class="stat-title">{{ stat.title }}</div>
                <div :class="['stat-trend', stat.trendType]">
                  <span v-if="stat.trendType === 'up'">↑</span>
                  <span v-else-if="stat.trendType === 'down'">↓</span>
                  {{ stat.trend }}
                </div>
              </div>
            </div>
          </div>
        </section>

        <section class="quick-actions-section">
          <div class="section-header">
            <h3>常用功能入口</h3>
          </div>
          <div class="quick-actions-grid">
            <div
              v-for="action in quickActions"
              :key="action.actionKey"
              class="quick-action-card"
              @click="handleQuickAction(action)"
            >
              <div class="qa-icon" :style="{ backgroundColor: action.color + '15', color: action.color }">
                {{ action.icon }}
              </div>
              <div class="qa-name">{{ action.name }}</div>
            </div>
          </div>
        </section>

        <section class="middle-row">
          <div class="panel-panel">
            <div class="section-header">
              <h3>待办事项</h3>
              <span class="section-sub">{{ pendingTodoCount }} 项待处理</span>
            </div>
            <div class="todo-list">
              <div v-for="todo in todos" :key="todo.id" :class="['todo-item', { done: todo.status === 'done' }]">
                <div class="todo-check" @click="completeTodo(todo)" v-if="todo.status === 'pending'">
                  ⬜
                </div>
                <div class="todo-check done-check" v-else>
                  ✅
                </div>
                <div class="todo-body">
                  <div class="todo-title">{{ todo.title }}</div>
                  <div class="todo-meta">
                    <span class="type-tag" :style="{ backgroundColor: todo.typeColor + '20', color: todo.typeColor }">
                      {{ todo.type }}
                    </span>
                    <span class="todo-time">{{ todo.time }}</span>
                  </div>
                </div>
              </div>
            </div>
          </div>

          <div class="recent-panel">
            <div class="section-header">
              <h3>最近记录</h3>
              <el-button type="primary" link size="small" @click="activeTab = 'query'">查看全部 →</el-button>
            </div>
            <div class="recent-list">
              <div v-for="rec in recentRecords" :key="rec.id" class="recent-item">
                <div class="rec-icon">📌</div>
                <div class="rec-body">
                  <div class="rec-title">{{ rec.title }}</div>
                  <div class="rec-meta">
                    <span class="rec-cat">{{ rec.category }}</span>
                    <span class="rec-user">{{ rec.user }}</span>
                    <span class="rec-time">{{ rec.time }}</span>
                  </div>
                </div>
                <div class="rec-status" :style="{ color: rec.statusColor }">{{ rec.status }}</div>
              </div>
            </div>
          </div>
        </section>
      </div>

      <div v-if="activeTab === 'query'" class="tab-query">
        <div class="search-panel">
          <div class="section-header">
            <h3>业务数据查询</h3>
          </div>
          <div class="search-form">
            <el-form :model="searchForm" inline>
              <el-form-item label="关键词">
                <el-input v-model="searchForm.keyword" placeholder="客户名称/负责人" clearable style="width: 220px" />
              </el-form-item>
              <el-form-item label="类型">
                <el-select v-model="searchForm.type" placeholder="全部" clearable style="width: 140px">
                  <el-option v-for="t in typeOptions" :key="t" :label="t" :value="t" />
                </el-select>
              </el-form-item>
              <el-form-item label="状态">
                <el-select v-model="searchForm.status" placeholder="全部" clearable style="width: 140px">
                  <el-option v-for="s in statusOptions" :key="s" :label="s" :value="s" />
                </el-select>
              </el-form-item>
              <el-form-item>
                <el-button type="primary" @click="handleSearch">🔍 查询</el-button>
                <el-button @click="resetSearch">重置</el-button>
                <el-button type="success" plain @click="handleExport">📤 导出</el-button>
              </el-form-item>
            </el-form>
          </div>
          <div class="action-row">
            <el-button type="primary" @click="addCustomerVisible = true">➕ 新增客户</el-button>
            <el-button type="success" @click="addOrderVisible = true">📝 新增订单</el-button>
            <el-button @click="importVisible = true">📥 批量导入</el-button>
          </div>
        </div>
        <div class="table-panel">
          <el-table :data="filteredRecords" stripe style="width: 100%" border>
            <el-table-column prop="id" label="编号" width="80" />
            <el-table-column prop="name" label="名称/单号" min-width="220" />
            <el-table-column prop="type" label="类型" width="100">
              <template #default="{ row }">
                <span class="type-badge">{{ row.type }}</span>
              </template>
            </el-table-column>
            <el-table-column prop="amount" label="金额" width="140">
              <template #default="{ row }">
                <span v-if="row.amount > 0">¥{{ row.amount.toLocaleString() }}</span>
                <span v-else style="color: #999;">-</span>
              </template>
            </el-table-column>
            <el-table-column prop="owner" label="负责人" width="120" />
            <el-table-column prop="status" label="状态" width="110">
              <template #default="{ row }">
                <el-tag :type="statusType(row.status)" effect="plain">{{ row.status }}</el-tag>
              </template>
            </el-table-column>
            <el-table-column prop="createTime" label="创建时间" width="170" />
            <el-table-column label="操作" width="180" fixed="right">
              <template #default="{ row }">
                <el-button type="primary" link size="small" @click="ElMessage.info('查看详情：' + row.name)">详情</el-button>
                <el-button type="success" link size="small" @click="ElMessage.info('编辑：' + row.name)">编辑</el-button>
                <el-button type="danger" link size="small" @click="ElMessage.warning('删除功能需二次确认')">删除</el-button>
              </template>
            </el-table-column>
          </el-table>
          <div class="table-footer">
            <span class="total-info">共 {{ filteredRecords.length }} 条记录</span>
            <el-pagination small layout="prev, pager, next" :total="filteredRecords.length" :page-size="10" />
          </div>
        </div>
      </div>

      <div v-if="activeTab === 'stats'" class="tab-stats">
        <div class="stats-panel">
          <div class="section-header">
            <h3>统计分析</h3>
          </div>
          <div class="stats-placeholder">
            <div class="ph-icon">📊</div>
            <div class="ph-title">统计分析模块</div>
            <div class="ph-desc">此处将展示销售趋势、客户分布、订单统计、业绩排行等多维图表分析</div>
            <div class="ph-grid">
              <div class="ph-card">
                <div class="ph-card-title">销售趋势</div>
                <div class="ph-card-content">近 30 天销售走势折线图</div>
              </div>
              <div class="ph-card">
                <div class="ph-card-title">客户分布</div>
                <div class="ph-card-content">客户类型、地区分布饼图</div>
              </div>
              <div class="ph-card">
                <div class="ph-card-title">订单状态</div>
                <div class="ph-card-content">各状态订单数量柱状图</div>
              </div>
              <div class="ph-card">
                <div class="ph-card-title">业绩排行</div>
                <div class="ph-card-content">销售人员业绩排名列表</div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </main>

    <el-dialog v-model="addCustomerVisible" title="新增客户" width="520px">
      <el-form :model="customerForm" label-width="90px" label-position="right">
        <el-form-item label="客户名称" required>
          <el-input v-model="customerForm.name" placeholder="请输入客户全称" />
        </el-form-item>
        <el-form-item label="客户类型">
          <el-select v-model="customerForm.type">
            <el-option label="普通客户" value="普通客户" />
            <el-option label="VIP 客户" value="VIP客户" />
            <el-option label="战略客户" value="战略客户" />
          </el-select>
        </el-form-item>
        <el-form-item label="联系电话">
          <el-input v-model="customerForm.phone" placeholder="请输入联系电话" />
        </el-form-item>
        <el-form-item label="电子邮箱">
          <el-input v-model="customerForm.email" placeholder="请输入邮箱" />
        </el-form-item>
        <el-form-item label="联系地址">
          <el-input v-model="customerForm.address" type="textarea" :rows="2" placeholder="请输入详细地址" />
        </el-form-item>
      </el-form>
      <template #footer>
        <el-button @click="addCustomerVisible = false">取消</el-button>
        <el-button type="primary" @click="submitCustomer">确定新增</el-button>
      </template>
    </el-dialog>

    <el-dialog v-model="addOrderVisible" title="新增订单" width="520px">
      <el-form :model="orderForm" label-width="90px" label-position="right">
        <el-form-item label="订单编号">
          <el-input v-model="orderForm.orderNo" placeholder="留空自动生成" />
        </el-form-item>
        <el-form-item label="关联客户" required>
          <el-input v-model="orderForm.customer" placeholder="请输入客户名称" />
        </el-form-item>
        <el-form-item label="订单类型">
          <el-select v-model="orderForm.type">
            <el-option label="采购" value="采购" />
            <el-option label="销售" value="销售" />
            <el-option label="调拨" value="调拨" />
          </el-select>
        </el-form-item>
        <el-form-item label="订单金额" required>
          <el-input-number v-model="orderForm.amount" :min="0" :precision="2" placeholder="0.00" style="width: 100%" />
        </el-form-item>
        <el-form-item label="订单备注">
          <el-input v-model="orderForm.description" type="textarea" :rows="2" placeholder="备注信息" />
        </el-form-item>
      </el-form>
      <template #footer>
        <el-button @click="addOrderVisible = false">取消</el-button>
        <el-button type="primary" @click="submitOrder">提交订单</el-button>
      </template>
    </el-dialog>

    <el-dialog v-model="importVisible" title="批量导入数据" width="520px">
      <el-form :model="importForm" label-width="90px" label-position="right">
        <el-form-item label="数据类型">
          <el-select v-model="importForm.type" style="width: 100%">
            <el-option label="客户数据" value="客户数据" />
            <el-option label="订单数据" value="订单数据" />
            <el-option label="合同数据" value="合同数据" />
          </el-select>
        </el-form-item>
        <el-form-item label="选择文件">
          <el-upload drag :auto-upload="false" :limit="1" :on-change="(f: File) => onFileChange(f)" accept=".xlsx,.xls,.csv">
            <el-icon class="el-icon--upload"><UploadFilled /></el-icon>
            <div class="el-upload__text">
              将文件拖到此处，或 <em>点击上传</em>
            </div>
            <template #tip>
              <div class="el-upload__tip">支持 .xlsx / .xls / .csv 格式，单个文件不超过 10MB</div>
            </template>
          </el-upload>
        </el-form-item>
        <el-form-item v-if="importForm.file" label="文件确认">
          <div class="file-info">
            📎 {{ importForm.file.name }}
            <span class="file-size">({{ (importForm.file.size / 1024).toFixed(1) }} KB)</span>
          </div>
        </el-form-item>
      </el-form>
      <template #footer>
        <el-button @click="importVisible = false">取消</el-button>
        <el-button type="primary" @click="handleImport">开始导入</el-button>
      </template>
    </el-dialog>
  </div>
</template>

<style scoped>
.workbench {
  min-height: 100vh;
  background: #f0f2f5;
}

.wb-header {
  height: 60px;
  background: #fff;
  border-bottom: 1px solid #e4e7ed;
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 0 24px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.04);
  position: sticky;
  top: 0;
  z-index: 100;
}

.wb-header-left {
  display: flex;
  align-items: center;
  gap: 40px;
}

.logo-box {
  display: flex;
  align-items: center;
  gap: 10px;
}

.logo-icon {
  font-size: 28px;
}

.logo-text {
  font-size: 18px;
  font-weight: 600;
  color: #303133;
}

.main-nav {
  display: flex;
  gap: 8px;
}

.nav-item {
  padding: 8px 20px;
  border-radius: 6px;
  cursor: pointer;
  color: #606266;
  font-size: 14px;
  transition: all 0.2s;
}

.nav-item:hover {
  background: #ecf5ff;
  color: #409EFF;
}

.nav-item.active {
  background: #409EFF;
  color: #fff;
}

.wb-header-right {
  display: flex;
  align-items: center;
  gap: 20px;
}

.header-badge {
  cursor: pointer;
}

.header-icon {
  font-size: 22px;
}

.user-info {
  display: flex;
  align-items: center;
  gap: 10px;
  cursor: pointer;
  padding: 4px 10px;
  border-radius: 6px;
  transition: background 0.2s;
}

.user-info:hover {
  background: #f5f7fa;
}

.avatar {
  width: 36px;
  height: 36px;
  border-radius: 50%;
  background: linear-gradient(135deg, #409EFF, #67c23a);
  color: #fff;
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: 600;
  font-size: 14px;
}

.user-name {
  font-size: 14px;
  color: #303133;
  font-weight: 500;
}

.user-role {
  font-size: 12px;
  color: #909399;
}

.wb-main {
  padding: 20px 24px;
  max-width: 1600px;
  margin: 0 auto;
}

.section-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 16px;
}

.section-header h3 {
  margin: 0;
  font-size: 16px;
  font-weight: 600;
  color: #303133;
  position: relative;
  padding-left: 10px;
}

.section-header h3::before {
  content: '';
  position: absolute;
  left: 0;
  top: 50%;
  transform: translateY(-50%);
  width: 3px;
  height: 14px;
  background: #409EFF;
  border-radius: 2px;
}

.section-sub {
  font-size: 12px;
  color: #909399;
}

.stats-section {
  background: #fff;
  border-radius: 8px;
  padding: 20px 24px;
  margin-bottom: 20px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.04);
}

.stats-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 16px;
}

.stat-card {
  background: #fff;
  border: 1px solid #ebeef5;
  border-top: 3px solid #409EFF;
  border-radius: 6px;
  padding: 20px;
  display: flex;
  align-items: center;
  gap: 16px;
  transition: all 0.3s;
  cursor: pointer;
}

.stat-card:hover {
  box-shadow: 0 4px 16px rgba(0, 0, 0, 0.08);
  transform: translateY(-2px);
}

.stat-icon {
  width: 54px;
  height: 54px;
  border-radius: 10px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 26px;
  flex-shrink: 0;
}

.stat-info {
  flex: 1;
}

.stat-value {
  font-size: 26px;
  font-weight: 700;
  color: #303133;
  line-height: 1.2;
}

.stat-title {
  font-size: 13px;
  color: #606266;
  margin-top: 4px;
}

.stat-trend {
  font-size: 12px;
  margin-top: 6px;
}

.stat-trend.up {
  color: #67c23a;
}

.stat-trend.down {
  color: #f56c6c;
}

.stat-trend.normal {
  color: #909399;
}

.quick-actions-section {
  background: #fff;
  border-radius: 8px;
  padding: 20px 24px;
  margin-bottom: 20px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.04);
}

.quick-actions-grid {
  display: grid;
  grid-template-columns: repeat(8, 1fr);
  gap: 12px;
}

.quick-action-card {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 18px 10px;
  border-radius: 6px;
  cursor: pointer;
  transition: all 0.2s;
  border: 1px solid transparent;
}

.quick-action-card:hover {
  background: #f5f7fa;
  border-color: #e4e7ed;
  transform: translateY(-2px);
}

.qa-icon {
  width: 46px;
  height: 46px;
  border-radius: 10px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 24px;
  margin-bottom: 8px;
}

.qa-name {
  font-size: 13px;
  color: #303133;
  text-align: center;
}

.middle-row {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 20px;
}

.panel-panel,
.recent-panel {
  background: #fff;
  border-radius: 8px;
  padding: 20px 24px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.04);
}

.todo-list {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.todo-item {
  display: flex;
  align-items: flex-start;
  gap: 12px;
  padding: 10px 12px;
  border-radius: 6px;
  border: 1px solid #f0f2f5;
  transition: background 0.2s;
}

.todo-item:hover {
  background: #fafafa;
}

.todo-item.done {
  opacity: 0.65;
}

.todo-check {
  cursor: pointer;
  font-size: 18px;
  padding-top: 2px;
}

.done-check {
  cursor: default;
}

.todo-body {
  flex: 1;
}

.todo-title {
  font-size: 14px;
  color: #303133;
  margin-bottom: 4px;
}

.todo-item.done .todo-title {
  text-decoration: line-through;
  color: #909399;
}

.todo-meta {
  display: flex;
  align-items: center;
  gap: 10px;
  font-size: 12px;
}

.type-tag {
  padding: 2px 8px;
  border-radius: 4px;
  font-size: 12px;
}

.todo-time {
  color: #909399;
}

.recent-list {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.recent-item {
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 10px 12px;
  border-radius: 6px;
  border: 1px solid #f0f2f5;
  transition: background 0.2s;
}

.recent-item:hover {
  background: #fafafa;
}

.rec-icon {
  font-size: 18px;
}

.rec-body {
  flex: 1;
}

.rec-title {
  font-size: 14px;
  color: #303133;
  margin-bottom: 4px;
}

.rec-meta {
  display: flex;
  align-items: center;
  gap: 12px;
  font-size: 12px;
  color: #909399;
}

.rec-status {
  font-size: 12px;
  font-weight: 500;
  white-space: nowrap;
}

.tab-query {
  display: flex;
  flex-direction: column;
  gap: 16px;
}

.search-panel {
  background: #fff;
  border-radius: 8px;
  padding: 20px 24px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.04);
}

.search-form {
  margin-bottom: 12px;
}

.action-row {
  padding-top: 12px;
  border-top: 1px dashed #ebeef5;
  display: flex;
  gap: 10px;
}

.table-panel {
  background: #fff;
  border-radius: 8px;
  padding: 20px 24px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.04);
}

.type-badge {
  display: inline-block;
  padding: 2px 8px;
  background: #ecf5ff;
  color: #409EFF;
  border-radius: 4px;
  font-size: 12px;
}

.table-footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-top: 16px;
}

.total-info {
  font-size: 13px;
  color: #606266;
}

.tab-stats {
  background: #fff;
  border-radius: 8px;
  padding: 20px 24px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.04);
}

.stats-placeholder {
  text-align: center;
  padding: 40px 0;
}

.ph-icon {
  font-size: 64px;
  margin-bottom: 12px;
}

.ph-title {
  font-size: 20px;
  font-weight: 600;
  color: #303133;
  margin-bottom: 8px;
}

.ph-desc {
  font-size: 13px;
  color: #909399;
  margin-bottom: 30px;
}

.ph-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 16px;
  max-width: 900px;
  margin: 0 auto;
}

.ph-card {
  background: linear-gradient(135deg, #ecf5ff, #f0f9ff);
  border-radius: 8px;
  padding: 24px;
  border: 1px solid #d9ecff;
}

.ph-card-title {
  font-size: 16px;
  font-weight: 600;
  color: #303133;
  margin-bottom: 8px;
}

.ph-card-content {
  font-size: 13px;
  color: #606266;
}

.file-info {
  padding: 8px 12px;
  background: #f0f9ff;
  border: 1px solid #b3d8ff;
  border-radius: 4px;
  font-size: 13px;
  color: #409EFF;
}

.file-size {
  color: #909399;
  margin-left: 8px;
}

@media (max-width: 1280px) {
  .stats-grid {
    grid-template-columns: repeat(2, 1fr);
  }
  .quick-actions-grid {
    grid-template-columns: repeat(4, 1fr);
  }
  .middle-row {
    grid-template-columns: 1fr;
  }
}
</style>
