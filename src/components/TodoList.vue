<script setup lang="ts">
import { ref, computed } from 'vue'
import { ElMessage, ElMessageBox } from 'element-plus'
import {
  Clock,
  Check,
  Delete,
  Plus,
  Warning,
  CircleClose,
  CircleCheck
} from '@element-plus/icons-vue'

interface TodoItem {
  id: number
  title: string
  priority: 'high' | 'medium' | 'low'
  status: 'pending' | 'processing' | 'completed'
  deadline: string
  assignee: string
}

const todos = ref<TodoItem[]>([
  { id: 1, title: '审核订单 #20240115001', priority: 'high', status: 'pending', deadline: '今天 17:00', assignee: '张三' },
  { id: 2, title: '回复客户咨询 - 李先生', priority: 'high', status: 'processing', deadline: '今天 16:00', assignee: '李四' },
  { id: 3, title: '更新商品库存信息', priority: 'medium', status: 'pending', deadline: '明天 10:00', assignee: '王五' },
  { id: 4, title: '准备月度销售报表', priority: 'medium', status: 'processing', deadline: '本周三', assignee: '赵六' },
  { id: 5, title: '系统维护通知', priority: 'low', status: 'completed', deadline: '已完成', assignee: '管理员' },
  { id: 6, title: '新员工培训安排', priority: 'low', status: 'pending', deadline: '下周一', assignee: '人事部' }
])

const newTodoTitle = ref('')
const showAddInput = ref(false)

const priorityConfig = {
  high: { label: '高', color: '#F56C6C', bgColor: '#FEF0F0' },
  medium: { label: '中', color: '#E6A23C', bgColor: '#FDF6EC' },
  low: { label: '低', color: '#67C23A', bgColor: '#F0F9EB' }
}

const statusConfig = {
  pending: { label: '待处理', icon: Clock, color: '#909399' },
  processing: { label: '处理中', icon: Warning, color: '#E6A23C' },
  completed: { label: '已完成', icon: Check, color: '#67C23A' }
}

const pendingCount = computed(() => todos.value.filter(t => t.status !== 'completed').length)
const completedCount = computed(() => todos.value.filter(t => t.status === 'completed').length)

const handleStatusChange = (todo: TodoItem, newStatus: TodoItem['status']) => {
  todo.status = newStatus
  const statusLabel = statusConfig[newStatus].label
  ElMessage.success(`已将任务状态更新为：${statusLabel}`)
}

const handleDelete = (id: number) => {
  ElMessageBox.confirm('确定要删除这条待办吗？', '提示', {
    confirmButtonText: '确定',
    cancelButtonText: '取消',
    type: 'warning'
  }).then(() => {
    todos.value = todos.value.filter(t => t.id !== id)
    ElMessage.success('删除成功')
  }).catch(() => {})
}

const handleAddTodo = () => {
  if (!newTodoTitle.value.trim()) {
    ElMessage.warning('请输入待办内容')
    return
  }
  const newTodo: TodoItem = {
    id: Date.now(),
    title: newTodoTitle.value.trim(),
    priority: 'medium',
    status: 'pending',
    deadline: '今天',
    assignee: '我'
  }
  todos.value.unshift(newTodo)
  newTodoTitle.value = ''
  showAddInput.value = false
  ElMessage.success('添加成功')
}

const getPriorityStyle = (priority: TodoItem['priority']) => {
  return priorityConfig[priority]
}
</script>

<template>
  <div class="todo-list">
    <div class="section-header">
      <div class="header-left">
        <h3 class="section-title">待办事项</h3>
        <el-tag size="small" type="info">{{ pendingCount }} 项待处理</el-tag>
        <el-tag size="small" type="success">{{ completedCount }} 项已完成</el-tag>
      </div>
      <el-button type="primary" :icon="Plus" size="small" @click="showAddInput = !showAddInput">
        新增待办
      </el-button>
    </div>

    <div v-if="showAddInput" class="add-todo">
      <el-input
        v-model="newTodoTitle"
        placeholder="输入新的待办事项..."
        @keyup.enter="handleAddTodo"
      >
        <template #append>
          <el-button type="primary" @click="handleAddTodo">添加</el-button>
        </template>
      </el-input>
    </div>

    <div class="todo-items">
      <div
        v-for="todo in todos"
        :key="todo.id"
        class="todo-item"
        :class="{ 'completed': todo.status === 'completed' }"
      >
        <div class="todo-main">
          <div class="todo-check" @click="handleStatusChange(todo, todo.status === 'completed' ? 'pending' : 'completed')">
            <el-icon v-if="todo.status === 'completed'" class="checked"><CircleCheck /></el-icon>
            <el-icon v-else class="unchecked"><CircleClose /></el-icon>
          </div>
          <div class="todo-content">
            <span class="todo-title">{{ todo.title }}</span>
            <div class="todo-meta">
              <el-tag
                size="small"
                :style="{ backgroundColor: getPriorityStyle(todo.priority).bgColor, color: getPriorityStyle(todo.priority).color, borderColor: getPriorityStyle(todo.priority).color }"
              >
                {{ getPriorityStyle(todo.priority).label }}优先级
              </el-tag>
              <span class="todo-deadline">
                <el-icon><Clock /></el-icon>
                {{ todo.deadline }}
              </span>
              <span class="todo-assignee">
                <el-icon><User /></el-icon>
                {{ todo.assignee }}
              </span>
            </div>
          </div>
        </div>
        <div class="todo-actions">
          <el-tooltip content="标记为待处理">
            <el-button
              :type="todo.status === 'pending' ? 'primary' : 'default'"
              size="small"
              :icon="Clock"
              circle
              @click="handleStatusChange(todo, 'pending')"
            />
          </el-tooltip>
          <el-tooltip content="标记为处理中">
            <el-button
              :type="todo.status === 'processing' ? 'warning' : 'default'"
              size="small"
              :icon="Warning"
              circle
              @click="handleStatusChange(todo, 'processing')"
            />
          </el-tooltip>
          <el-tooltip content="标记为已完成">
            <el-button
              :type="todo.status === 'completed' ? 'success' : 'default'"
              size="small"
              :icon="Check"
              circle
              @click="handleStatusChange(todo, 'completed')"
            />
          </el-tooltip>
          <el-tooltip content="删除">
            <el-button type="danger" size="small" :icon="Delete" circle @click="handleDelete(todo.id)" />
          </el-tooltip>
        </div>
      </div>
    </div>

    <div v-if="todos.length === 0" class="empty-state">
      <el-empty description="暂无待办事项" />
    </div>
  </div>
</template>

<style scoped>
.todo-list {
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

.add-todo {
  margin-bottom: 16px;
}

.todo-items {
  flex: 1;
  overflow-y: auto;
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.todo-item {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  padding: 16px;
  background: #fafafa;
  border-radius: 8px;
  transition: all 0.3s ease;
}

.todo-item:hover {
  background: #f5f7fa;
}

.todo-item.completed {
  opacity: 0.6;
}

.todo-item.completed .todo-title {
  text-decoration: line-through;
}

.todo-main {
  display: flex;
  gap: 12px;
  flex: 1;
}

.todo-check {
  cursor: pointer;
  flex-shrink: 0;
}

.unchecked {
  color: #c0c4cc;
  font-size: 20px;
}

.checked {
  color: #67C23A;
  font-size: 20px;
}

.todo-content {
  flex: 1;
}

.todo-title {
  font-size: 14px;
  color: #303133;
  line-height: 1.5;
  margin-bottom: 8px;
  display: block;
}

.todo-meta {
  display: flex;
  align-items: center;
  gap: 12px;
  flex-wrap: wrap;
}

.todo-deadline,
.todo-assignee {
  display: flex;
  align-items: center;
  gap: 4px;
  font-size: 12px;
  color: #909399;
}

.todo-actions {
  display: flex;
  gap: 4px;
  flex-shrink: 0;
}

.empty-state {
  flex: 1;
  display: flex;
  align-items: center;
  justify-content: center;
}
</style>
