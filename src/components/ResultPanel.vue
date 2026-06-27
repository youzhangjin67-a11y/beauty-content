<template>
  <section class="result-panel">
    <div class="panel-header">
      <h2 class="panel-title">生成结果</h2>
      <span v-if="platforms.length" class="panel-badge">{{ platforms.length }} 平台</span>
    </div>

    <div v-if="!hasGenerated" class="state-box">
      <div class="state-icon">
        <svg width="48" height="48" viewBox="0 0 24 24" fill="none" stroke="#d4ccc7" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round">
          <path d="M14 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V8z"/>
          <polyline points="14 2 14 8 20 8"/>
          <line x1="12" y1="18" x2="12" y2="12"/>
          <line x1="9" y1="15" x2="15" y2="15"/>
        </svg>
      </div>
      <p class="state-text">请在左侧填写信息后点击「生成」</p>
    </div>

    <div v-else-if="isLoading" class="state-box">
      <div class="loading-spinner"></div>
      <p class="state-text">正在根据智能规则生成内容...</p>
    </div>

    <template v-else>
      <div class="tabs-wrapper">
        <button
          v-for="p in platforms"
          :key="p"
          :class="['tab-btn', { active: activeTab === p }]"
          @click="$emit('update:activeTab', p)"
        >
          {{ labelMap[p] || p }}
        </button>
      </div>
      <div class="tab-content">
        <ResultCard
          v-for="p in platforms"
          :key="p"
          v-show="activeTab === p"
          :platform="p"
          :content="results[p]"
          :platformLabel="labelMap[p] || p"
          @copy="(plat, text) => $emit('copy', plat, text)"
        />
      </div>
    </template>
  </section>
</template>

<script setup>
import ResultCard from './ResultCard.vue'

defineProps({
  platforms: { type: Array, default: () => [] },
  results: { type: Object, default: () => ({}) },
  activeTab: { type: String, default: 'amazon' },
  hasGenerated: { type: Boolean, default: false },
  isLoading: { type: Boolean, default: false },
})
defineEmits(['update:activeTab', 'copy'])

const labelMap = { amazon: '亚马逊', alibaba: '国际站', tiktok: 'TikTok' }
</script>

<style scoped>
.result-panel {
  flex: 1;
  min-width: 0;
  background: #fff;
  border-radius: 14px;
  border: 1px solid #e8e2dc;
  padding: 24px;
  display: flex;
  flex-direction: column;
  overflow: hidden;
}
.panel-header {
  display: flex;
  align-items: center;
  gap: 10px;
  margin-bottom: 20px;
  padding-bottom: 14px;
  border-bottom: 1px solid #f0eae6;
}
.panel-title {
  font-size: 15px;
  font-weight: 600;
  color: #2c2522;
}
.panel-badge {
  font-size: 11px;
  padding: 2px 10px;
  background: #f5f0f7;
  color: #8b6f8e;
  border-radius: 20px;
  font-weight: 500;
}
.state-box {
  flex: 1;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 16px;
}
.state-text {
  font-size: 14px;
  color: #8a7e78;
}
.state-icon svg { display: block; }
.loading-spinner {
  width: 40px;
  height: 40px;
  border: 3px solid #ede4ef;
  border-top-color: #8b6f8e;
  border-radius: 50%;
  animation: spin 0.8s linear infinite;
}
@keyframes spin { to { transform: rotate(360deg); } }
.tabs-wrapper {
  display: flex;
  gap: 4px;
  padding: 4px;
  background: #f7f5f3;
  border-radius: 10px;
  margin-bottom: 16px;
  flex-shrink: 0;
}
.tab-btn {
  flex: 1;
  padding: 8px 16px;
  border: none;
  border-radius: 8px;
  background: transparent;
  color: #8a7e78;
  font-size: 13px;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.2s;
}
.tab-btn.active {
  background: #409EFF;
  color: #ffffff;
  font-weight: 600;
  box-shadow: 0 1px 4px rgba(139,111,142,0.12);
}
.tab-btn:hover:not(.active) { color: #735879; }
.tab-content {
  flex: 1;
  overflow: hidden;
}
</style>
