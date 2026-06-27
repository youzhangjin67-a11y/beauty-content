<template>
  <div class="result-card">
    <button class="copy-btn" :title="'复制' + platformLabel + '平台内容'" @click="handleCopy">
      <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
        <rect x="9" y="9" width="13" height="13" rx="2" ry="2"/>
        <path d="M5 15H4a2 2 0 0 1-2-2V4a2 2 0 0 1 2-2h9a2 2 0 0 1 2 2v1"/>
      </svg>
    </button>

    <div class="card-body">
      <div class="field-group">
        <div class="field-header">
          <label class="field-label">标题</label>
          <button class="mini-copy-btn" title="复制标题" @click="handleCopyField('title')">
            <svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
              <rect x="9" y="9" width="13" height="13" rx="2" ry="2"/>
              <path d="M5 15H4a2 2 0 0 1-2-2V4a2 2 0 0 1 2-2h9a2 2 0 0 1 2 2v1"/>
            </svg>
          </button>
        </div>
        <p class="field-value"><HighlightText :text="content.title" /></p>
      </div>

      <div class="field-group">
        <div class="field-header">
          <label class="field-label">描述</label>
          <button class="mini-copy-btn" title="复制描述" @click="handleCopyField('description')">
            <svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
              <rect x="9" y="9" width="13" height="13" rx="2" ry="2"/>
              <path d="M5 15H4a2 2 0 0 1-2-2V4a2 2 0 0 1 2-2h9a2 2 0 0 1 2 2v1"/>
            </svg>
          </button>
        </div>
        <p class="field-value"><HighlightText :text="content.description" /></p>
      </div>

      <div class="field-group">
        <div class="field-header">
          <label class="field-label">核心卖点</label>
          <button class="mini-copy-btn" title="复制核心卖点" @click="handleCopyField('keyFeatures')">
            <svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
              <rect x="9" y="9" width="13" height="13" rx="2" ry="2"/>
              <path d="M5 15H4a2 2 0 0 1-2-2V4a2 2 0 0 1 2-2h9a2 2 0 0 1 2 2v1"/>
            </svg>
          </button>
        </div>
        <ul class="feature-list">
          <li v-for="(f, i) in content.keyFeatures" :key="i"><HighlightText :text="f" /></li>
        </ul>
      </div>

      <div class="field-group">
        <div class="field-header">
          <label class="field-label">搜索关键词</label>
          <button class="mini-copy-btn" title="复制搜索关键词" @click="handleCopyField('keywords')">
            <svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
              <rect x="9" y="9" width="13" height="13" rx="2" ry="2"/>
              <path d="M5 15H4a2 2 0 0 1-2-2V4a2 2 0 0 1 2-2h9a2 2 0 0 1 2 2v1"/>
            </svg>
          </button>
        </div>
        <div class="keywords-list">
          <span v-for="(kw, i) in content.keywords" :key="i" class="kw-pill"><HighlightText :text="kw" /></span>
        </div>
      </div>

      <div class="field-group notes">
        <div class="field-header">
          <label class="field-label">备注</label>
          <button class="mini-copy-btn" title="复制备注" @click="handleCopyField('notes')">
            <svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
              <rect x="9" y="9" width="13" height="13" rx="2" ry="2"/>
              <path d="M5 15H4a2 2 0 0 1-2-2V4a2 2 0 0 1 2-2h9a2 2 0 0 1 2 2v1"/>
            </svg>
          </button>
        </div>
        <p class="field-value"><HighlightText :text="content.notes" /></p>
      </div>
    </div>
  </div>
</template>

<script setup>
import HighlightText from './HighlightText.vue'

const props = defineProps({
  platform: { type: String, required: true },
  content: { type: Object, required: true },
  platformLabel: { type: String, default: '' },
})
const emit = defineEmits(['copy'])

const platformLabels = { amazon: '亚马逊', alibaba: '国际站', tiktok: 'TikTok' }

function handleCopy() {
  const c = props.content
  const text = [
    '【标题】' + c.title, '',
    '【描述】' + c.description, '',
    '【核心卖点】',
    ...c.keyFeatures.map(f => '  \u2022 ' + f), '',
    '【搜索关键词】' + c.keywords.join(', '), '',
    '【备注】' + c.notes,
  ].join('\n')
  emit('copy', props.platform, text)
}

function handleCopyField(field) {
  const c = props.content
  let text = ''
  
  if (field === 'keyFeatures') {
    text = c.keyFeatures.join('\n')
  } else if (field === 'keywords') {
    text = c.keywords.join(', ')
  } else {
    text = c[field]
  }
  
  emit('copy', props.platform, text)
}
</script>

<style scoped>
.result-card {
  position: relative;
  background: #fff;
  border: 1px solid #e8e2dc;
  border-radius: 12px;
  padding: 24px;
  height: 100%;
  overflow-y: auto;
  transition: box-shadow 0.25s;
}
.result-card:hover {
  box-shadow: 0 4px 20px rgba(139,111,142,0.08);
}
.copy-btn {
  position: absolute;
  top: 14px;
  right: 14px;
  z-index: 5;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 34px;
  height: 34px;
  border: 1px solid #e8e2dc;
  border-radius: 8px;
  background: #fff;
  color: #8a7e78;
  cursor: pointer;
  transition: all 0.2s;
}
.copy-btn:hover {
  background: #f5f0f7;
  border-color: #c9b5d0;
  color: #8b6f8e;
}
.card-body { padding-right: 50px; }
.field-group { margin-bottom: 18px; }
.field-group:last-child { margin-bottom: 0; }
.field-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 6px;
}
.field-header .field-label { margin-bottom: 0; }
.mini-copy-btn {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 26px;
  height: 26px;
  border: 1px solid #e8e2dc;
  border-radius: 6px;
  background: #fafafa;
  color: #8a7e78;
  cursor: pointer;
  transition: all 0.2s;
}
.mini-copy-btn:hover {
  background: #f5f0f7;
  border-color: #c9b5d0;
  color: #8b6f8e;
}
.field-label {
  display: block;
  font-size: 11px;
  font-weight: 600;
  color: #8a7e78;
  letter-spacing: 0.8px;
  text-transform: uppercase;
  margin-bottom: 6px;
}
.field-value {
  font-size: 14px;
  line-height: 1.75;
  color: #2c2522;
  word-break: break-word;
}
.feature-list { list-style: none; padding: 0; margin: 0; }
.feature-list li {
  position: relative;
  padding-left: 16px;
  margin-bottom: 6px;
  font-size: 14px;
  line-height: 1.65;
  color: #2c2522;
}
.feature-list li::before {
  content: '';
  position: absolute;
  left: 2px;
  top: 10px;
  width: 6px;
  height: 6px;
  border-radius: 50%;
  background: #c9a86c;
}
.keywords-list {
  display: flex;
  flex-wrap: wrap;
  gap: 6px;
}
.kw-pill {
  display: inline-block;
  padding: 3px 10px;
  font-size: 12px;
  background: #f5f0f7;
  color: #735879;
  border-radius: 20px;
  line-height: 1.6;
}
.notes .field-value {
  font-size: 12px;
  color: #8a7e78;
  font-style: italic;
}
</style>
