<template>
  <view class="container">
    <view class="header">
      <text class="title">WOT-UI Input Number Bug Demo</text>
      <text class="subtitle">wd-input-number 组件 Bug 反馈</text>
    </view>

    <view class="content">
      <!-- 基础演示 -->
      <view class="demo-section">
        <text class="section-title">基础配置演示</text>
        <view class="form-group">
          <text class="label">当前值: {{ value }}</text>
          <view class="input-wrapper">
            <wd-input-number
              v-model="value"
              :precision="2"
              :step="0.01"
              :min="2"
              :immediate-change="false"
              placeholder="请输入数字"
            />
          </view>
          <view class="config-info">
            <text class="config-item">• precision: 2</text>
            <text class="config-item">• step: 0.01</text>
            <text class="config-item">• min: 2</text>
            <text class="config-item">• immediate-change: false</text>
          </view>
        </view>
      </view>

      <!-- 操作面板 -->
      <view class="demo-section">
        <text class="section-title">测试操作</text>
        <view class="button-group">
          <wd-button
            type="primary"
            @click="handleReset"
            custom-class="btn"
          >
            重置值 (设为 2)
          </wd-button>
          <wd-button
            type="info"
            @click="handleIncrease"
            custom-class="btn"
          >
            增加 0.01
          </wd-button>
          <wd-button
            type="warning"
            @click="handleDecrease"
            custom-class="btn"
          >
            减少 0.01
          </wd-button>
          <wd-button
            type="danger"
            @click="handleSetMax"
            custom-class="btn"
          >
            设为最大值 (10)
          </wd-button>
        </view>
      </view>

      <!-- 状态监控 -->
      <view class="demo-section">
        <text class="section-title">状态监控</text>
        <view class="status-box">
          <view class="status-item">
            <text class="status-label">当前值:</text>
            <text class="status-value">{{ value }}</text>
          </view>
          <view class="status-item">
            <text class="status-label">最近改变时间:</text>
            <text class="status-value">{{ lastChangeTime || '未改变' }}</text>
          </view>
          <view class="status-item">
            <text class="status-label">改变次数:</text>
            <text class="status-value">{{ changeCount }}</text>
          </view>
          <view class="status-item">
            <text class="status-label">改变历史:</text>
            <text class="status-value">{{ changeHistory.join(' → ') || '无' }}</text>
          </view>
        </view>
      </view>

      <!-- 问题描述 -->
      <view class="demo-section bug-description">
        <text class="section-title">已知问题</text>
        <view class="issue-list">
          <view class="issue-item">
            <text class="issue-marker">⚠️</text>
            <view class="issue-content">
              <text class="issue-title">精度问题</text>
              <text class="issue-detail">在某些情况下，小数点精度可能不准确</text>
            </view>
          </view>
          <view class="issue-item">
            <text class="issue-marker">⚠️</text>
            <view class="issue-content">
              <text class="issue-title">immediate-change 配置</text>
              <text class="issue-detail">设置 immediate-change:false 时，值变化的触发时机可能异常</text>
            </view>
          </view>
          <view class="issue-item">
            <text class="issue-marker">⚠️</text>
            <view class="issue-content">
              <text class="issue-title">最小值验证</text>
              <text class="issue-detail">当 min 值设置后，删除输入框内容可能导致验证失效</text>
            </view>
          </view>
        </view>
      </view>

      <!-- 测试日志 -->
      <view class="demo-section">
        <text class="section-title">操作日志</text>
        <view class="log-box">
          <view
            v-for="(log, index) in logs"
            :key="index"
            class="log-item"
          >
            <text class="log-time">{{ log.time }}</text>
            <text class="log-message">{{ log.message }}</text>
          </view>
          <view v-if="logs.length === 0" class="log-empty">
            <text>暂无操作日志</text>
          </view>
        </view>
      </view>
    </view>
  </view>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'

const value = ref(2)
const changeCount = ref(0)
const changeHistory = ref<number[]>([])
const lastChangeTime = ref('')
const logs = ref<Array<{ time: string; message: string }>>([])

const handleReset = () => {
  value.value = 2
  addLog('值已重置为 2')
}

const handleIncrease = () => {
  value.value = Math.round((value.value + 0.01) * 100) / 100
  addLog(`值增加至 ${value.value}`)
}

const handleDecrease = () => {
  if (value.value > 2) {
    value.value = Math.round((value.value - 0.01) * 100) / 100
    addLog(`值减少至 ${value.value}`)
  } else {
    addLog('值已达到最小值 2，无法继续减少')
  }
}

const handleSetMax = () => {
  value.value = 10
  addLog('值设为最大值 10')
}

const addLog = (message: string) => {
  const now = new Date()
  const time = now.toLocaleTimeString('zh-CN')
  logs.value.unshift({ time, message })
  if (logs.value.length > 10) {
    logs.value.pop()
  }
}

// 监听 value 变化
const originalValue = ref(value.value)
const watchValue = computed(() => value.value)

// 由于使用 computed 监听，需要额外的 watch
import { watch } from 'vue'

watch(watchValue, (newValue) => {
  if (newValue !== originalValue.value) {
    originalValue.value = newValue
    changeCount.value++
    changeHistory.value.push(newValue)
    if (changeHistory.value.length > 10) {
      changeHistory.value.shift()
    }
    const now = new Date()
    lastChangeTime.value = now.toLocaleTimeString('zh-CN')
    addLog(`值变更: ${newValue}`)
  }
})
</script>

<style lang="scss" scoped>
.container {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
  background-color: #f5f7fa;
}

.header {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  padding: 30px 20px;
  color: white;

  .title {
    font-size: 28px;
    font-weight: bold;
    display: block;
    margin-bottom: 8px;
  }

  .subtitle {
    font-size: 14px;
    opacity: 0.9;
  }
}

.content {
  flex: 1;
  padding: 20px;
}

.demo-section {
  background: white;
  border-radius: 8px;
  padding: 20px;
  margin-bottom: 16px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);

  .section-title {
    font-size: 16px;
    font-weight: bold;
    color: #333;
    display: block;
    margin-bottom: 16px;
    padding-bottom: 10px;
    border-bottom: 2px solid #667eea;
  }
}

.form-group {
  .label {
    display: block;
    font-size: 14px;
    color: #666;
    margin-bottom: 12px;
    font-weight: 500;
  }

  .input-wrapper {
    margin-bottom: 12px;
  }

  .config-info {
    margin-top: 12px;
    padding: 10px;
    background-color: #f0f2f5;
    border-radius: 4px;

    .config-item {
      display: block;
      font-size: 12px;
      color: #666;
      line-height: 1.6;
      font-family: monospace;
    }
  }
}

.button-group {
  display: flex;
  flex-direction: column;
  gap: 10px;

  .btn {
    width: 100%;
  }
}

.status-box {
  background-color: #f0f2f5;
  border-radius: 4px;
  padding: 12px;

  .status-item {
    display: flex;
    justify-content: space-between;
    margin-bottom: 8px;
    font-size: 14px;

    &:last-child {
      margin-bottom: 0;
    }

    .status-label {
      color: #666;
      font-weight: 500;
    }

    .status-value {
      color: #333;
      font-family: monospace;
      font-weight: bold;
    }
  }
}

.bug-description {
  .issue-list {
    gap: 12px;
  }

  .issue-item {
    display: flex;
    gap: 12px;
    padding: 12px;
    background-color: #fff9f0;
    border-left: 3px solid #ff7a45;
    border-radius: 4px;

    .issue-marker {
      font-size: 18px;
      flex-shrink: 0;
    }

    .issue-content {
      flex: 1;

      .issue-title {
        display: block;
        font-size: 14px;
        color: #333;
        font-weight: bold;
        margin-bottom: 4px;
      }

      .issue-detail {
        display: block;
        font-size: 12px;
        color: #666;
      }
    }
  }
}

.log-box {
  background-color: #f5f5f5;
  border-radius: 4px;
  padding: 12px;
  max-height: 200px;
  overflow-y: auto;
  font-family: monospace;
  font-size: 12px;

  .log-item {
    display: flex;
    gap: 8px;
    margin-bottom: 8px;
    padding-bottom: 8px;
    border-bottom: 1px solid #e0e0e0;

    &:last-child {
      border-bottom: none;
      margin-bottom: 0;
      padding-bottom: 0;
    }

    .log-time {
      color: #999;
      flex-shrink: 0;
    }

    .log-message {
      color: #333;
      flex: 1;
    }
  }

  .log-empty {
    text-align: center;
    color: #999;
    font-size: 14px;
    padding: 20px 0;
  }
}
</style>
