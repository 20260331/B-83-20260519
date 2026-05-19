<script setup lang="ts">
import { computed } from 'vue'
import {
  ShoppingCart,
  Money,
  Warning,
  User,
  TrendCharts,
  Top,
  Bottom
} from '@element-plus/icons-vue'

interface Props {
  title: string
  value: number
  unit?: string
  trend?: number
  icon?: string
  color?: string
}

const props = withDefaults(defineProps<Props>(), {
  unit: '',
  trend: 0,
  icon: 'TrendCharts',
  color: '#409EFF'
})

const iconComponent = computed(() => {
  const icons: Record<string, any> = {
    ShoppingCart,
    Money,
    Warning,
    User,
    TrendCharts
  }
  return icons[props.icon] || TrendCharts
})

const formattedValue = computed(() => {
  if (props.value >= 10000) {
    return (props.value / 10000).toFixed(1) + '万'
  }
  return props.value.toLocaleString()
})

const trendClass = computed(() => {
  if (props.trend > 0) return 'trend-up'
  if (props.trend < 0) return 'trend-down'
  return 'trend-flat'
})
</script>

<template>
  <div class="stat-card">
    <div class="card-content">
      <div class="card-info">
        <span class="card-title">{{ title }}</span>
        <div class="card-value">
          <span class="value">{{ formattedValue }}</span>
          <span class="unit" v-if="unit">{{ unit }}</span>
        </div>
        <div class="card-trend" :class="trendClass">
          <el-icon v-if="trend > 0"><Top /></el-icon>
          <el-icon v-else-if="trend < 0"><Bottom /></el-icon>
          <span>{{ Math.abs(trend) }}%</span>
          <span class="trend-label">较昨日</span>
        </div>
      </div>
      <div class="card-icon" :style="{ backgroundColor: color + '15', color: color }">
        <el-icon :size="32">
          <component :is="iconComponent" />
        </el-icon>
      </div>
    </div>
  </div>
</template>

<style scoped>
.stat-card {
  background: white;
  border-radius: 12px;
  padding: 24px;
  box-shadow: 0 2px 12px rgba(0, 0, 0, 0.08);
  transition: all 0.3s ease;
  cursor: pointer;
}

.stat-card:hover {
  transform: translateY(-4px);
  box-shadow: 0 8px 24px rgba(0, 0, 0, 0.12);
}

.card-content {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.card-info {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.card-title {
  font-size: 14px;
  color: #909399;
}

.card-value {
  display: flex;
  align-items: baseline;
  gap: 4px;
}

.value {
  font-size: 32px;
  font-weight: 600;
  color: #303133;
  line-height: 1.2;
}

.unit {
  font-size: 14px;
  color: #909399;
}

.card-trend {
  display: flex;
  align-items: center;
  gap: 4px;
  font-size: 13px;
}

.trend-up {
  color: #67C23A;
}

.trend-down {
  color: #F56C6C;
}

.trend-flat {
  color: #909399;
}

.trend-label {
  color: #909399;
  margin-left: 4px;
}

.card-icon {
  width: 64px;
  height: 64px;
  border-radius: 12px;
  display: flex;
  align-items: center;
  justify-content: center;
}
</style>
