<template>
  <div class="app-container">
    <header class="app-header">
      <div class="header-inner">
        <div class="header-brand">
          <div class="brand-icon">
            <svg width="22" height="22" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
              <path d="M12 3a9 9 0 1 0 9 9"/>
              <polyline points="21 12 21 3 12 3"/>
            </svg>
          </div>
          <span class="brand-name">美妆外贸内容生成</span>
        </div>
      </div>
    </header>

    <main class="app-main">
      <ContentForm
        v-model="formModel"
        :isLoading="isLoading"
        @generate="handleGenerate"
        @reset="handleReset"
      />
      <ResultPanel
        :platforms="formModel.platforms"
        :results="results"
        :activeTab="activeTab"
        :hasGenerated="hasGenerated"
        :isLoading="isLoading"
        @update:activeTab="activeTab = $event"
        @copy="handleCopy"
      />
    </main>
  </div>
</template>

<script setup>
import { ref, reactive } from 'vue'
import { ElMessage } from 'element-plus'
import ContentForm from './components/ContentForm.vue'
import ResultPanel from './components/ResultPanel.vue'

// --- 表单状态 ---
const formModel = reactive({
  productName: '',
  ingredients: '',
  efficacy: '',
  market: '',
  platforms: [],
})

// --- 结果状态 ---
const isLoading = ref(false)
const hasGenerated = ref(false)
const activeTab = ref('amazon')
const results = reactive({})

// --- 模拟内容生成器 ---
const marketLabels = { EU: '欧盟', US: '美国', TH: '泰国' }

function generateMock(productName, ingredients, efficacy, market, platform) {
  const ml = marketLabels[market]
  const amazon = {
    title: productName + ' — Advanced ' + efficacy + ' Formula with ' + ingredients + ' for Perfect Skincare',
    description: 'Experience the miracle of our ' + productName + '. Formulated with premium ' + ingredients + ', this advanced treatment delivers instant whitening and anti-aging benefits. Our therapeutic formula helps eliminate wrinkles and gently detox the skin, revealing a radiant, healthy complexion. Perfect for daily use, this medical-grade skincare solution is tailored for the ' + ml + ' market.',
    keyFeatures: [
      ingredients + ' complex for instant radiance and anti-aging support',
      'Therapeutic formula helps cure common skin concerns while you sleep',
      'Medical-grade ingredients deliver perfect results for ' + ml + ' consumers',
      'Miracle anti-aging treatment that helps heal and rejuvenate skin',
    ],
    keywords: [productName + ' ' + ml, 'whitening cream ' + efficacy, 'anti-aging treatment serum', 'perfect skincare solution', ml + ' beauty products'],
    notes: 'Target: ' + ml + ' | Amazon | Compliant with ' + ml + ' cosmetic advertising regulations',
  }
  const alibaba = {
    title: 'Wholesale ' + productName + ' — Professional ' + efficacy + ' Cream with ' + ingredients + ' for Instant Whitening',
    description: 'Professional-grade ' + productName + ' infused with ' + ingredients + ' for B2B wholesale. This therapeutic treatment delivers medical-grade skincare benefits with instant whitening and anti-aging effects. Our formula helps eliminate wrinkles and provides deep detox for perfect skin. Ideal for the ' + ml + ' market with bulk packaging options.',
    keyFeatures: [
      'Medical-grade ' + ingredients + ' for perfect wholesale supply chain',
      'Therapeutic anti-aging and whitening treatment for ' + ml + ' distributors',
      'Helps cure and eliminate wrinkles with instant visible results',
      'Miracle detox formula for professional beauty clinics',
    ],
    keywords: [productName + ' wholesale ' + ml, 'whitening bulk supplier', 'anti-aging treatment manufacturer', 'medical skincare wholesale', 'perfect skin solution'],
    notes: 'Target: ' + ml + ' | Alibaba International | Minimum order quantities | OEM/ODM welcome',
  }
  const tiktok = {
    title: productName + ' ✨ — ' + efficacy + ' Miracle That Delivers Perfect Skin Instantly!',
    description: 'OMG besties! This miracle product with ' + ingredients + ' is the ultimate treatment for instant glow! No more worrying about anti-aging — say goodbye to wrinkles! This therapeutic formula helps heal and detox your skin with amazing whitening benefits. Get that perfect glass skin look in minutes! #SkincareRoutine',
    keyFeatures: [
      'Miracle ' + efficacy + ' treatment — instant glow booster!',
      'Anti-aging & whitening duo for perfect glass skin ✨',
      'Helps detox and heal skin naturally with ' + ingredients,
      'Perfect for ' + ml + ' beauty lovers on TikTok!',
    ],
    keywords: ['#' + productName.replace(/\\s+/g, ''), '#perfectskin', '#whiteningroutine', '#antiagingtips', '#skincaremiracle', '#' + ml + 'beauty'],
    notes: 'Target: ' + ml + ' | TikTok | Short-video optimized | Hashtag strategy included',
  }
  return { amazon, alibaba, tiktok }[platform]
}

// --- 事件处理 ---
function handleGenerate(data) {
  isLoading.value = true
  hasGenerated.value = true

  setTimeout(() => {
    const { productName, ingredients, efficacy, market, platforms } = data
    for (const p of platforms) {
      results[p] = generateMock(productName, ingredients, efficacy, market, p)
    }
    if (platforms.length) activeTab.value = platforms[0]
    isLoading.value = false
    ElMessage.success('内容生成完成！')
  }, 2000)
}

function handleReset() {
  for (const key of Object.keys(results)) delete results[key]
  hasGenerated.value = false
  activeTab.value = 'amazon'
  ElMessage.info('已重置所有内容')
}

function handleCopy(platform, text) {
  navigator.clipboard.writeText(text).then(() => {
    ElMessage.success('已复制 ' + ({ amazon: '亚马逊', alibaba: '国际站', tiktok: 'TikTok' })[platform] + ' 平台内容')
  }).catch(() => {
    const ta = document.createElement('textarea')
    ta.value = text
    document.body.appendChild(ta)
    ta.select()
    document.execCommand('copy')
    document.body.removeChild(ta)
    ElMessage.success('已复制 ' + ({ amazon: '亚马逊', alibaba: '国际站', tiktok: 'TikTok' })[platform] + ' 平台内容')
  })
}
</script>

<style>
/* ====== 全局设计令牌 ====== */
:root {
  --color-primary: #8b6f8e;
  --color-primary-hover: #735879;
  --color-primary-light: #ede4ef;
  --color-accent: #c9a86c;
  --color-bg: #f7f5f3;
  --color-surface: #ffffff;
  --color-border: #e8e2dc;
  --color-text: #2c2522;
  --color-text-secondary: #8a7e78;
  --radius: 14px;
  --shadow-sm: 0 1px 3px rgba(44,37,34,0.06);
  --shadow-md: 0 4px 14px rgba(44,37,34,0.07);
}

/* ====== 全局样式覆盖 ====== */
body {
  background: var(--color-bg) !important;
  color: var(--color-text) !important;
}

/* 覆盖 Element Plus 下拉框样式以匹配整体风格 */
.el-popper.is-light { border-color: var(--color-border) !important; }

/* 违规提示样式 */
.violation-tip { font-size: 12px; }

/* ElMessage 自定义样式 */
.el-message { border-radius: 10px !important; }
.el-message--success {
  background: #f6f3f0 !important;
  border-color: #c9a86c !important;
}
.el-message--warning {
  background: #fdf8f0 !important;
  border-color: #e8d5a8 !important;
}
.el-message--info {
  background: #f5f0f7 !important;
  border-color: #d4c5d8 !important;
}
</style>

<style scoped>
.app-container {
  display: flex;
  flex-direction: column;
  height: 100vh;
}
.app-header {
  flex-shrink: 0;
  background: #409EFF;
  color: #fff;
}
.header-inner {
  display: flex;
  align-items: center;
  justify-content: space-between;
  max-width: 1600px;
  margin: 0 auto;
  padding: 0 32px;
  height: 56px;
}
.header-brand {
  display: flex;
  align-items: center;
  gap: 10px;
}
.brand-icon {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 34px;
  height: 34px;
  background: rgba(255,255,255,0.18);
  border-radius: 10px;
}
.brand-name {
  font-size: 17px;
  font-weight: 600;
  letter-spacing: 0.5px;
}
.app-main {
  flex: 1;
  display: flex;
  gap: 20px;
  padding: 20px 32px 24px;
  min-height: 0;
  max-width: 1600px;
  width: 100%;
  margin: 0 auto;
}

@media (max-width: 768px) {
  .app-main {
    flex-direction: column;
    padding: 16px;
  }
}
</style>
