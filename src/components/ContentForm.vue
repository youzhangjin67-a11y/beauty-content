<template>
  <section class="form-panel">
    <div class="panel-header">
      <h2 class="panel-title">信息填写</h2>
    </div>

    <el-form
      ref="formRef"
      :model="modelValue"
      :rules="rules"
      label-position="top"
      size="large"
      class="beauty-form"
    >
      <el-form-item label="产品名称" prop="productName">
        <el-input
          v-model="modelValue.productName"
          placeholder="请输入产品名称，如：亮肤精华液"
          maxlength="50"
          show-word-limit
          clearable
        />
      </el-form-item>

      <el-form-item label="核心成分" prop="ingredients">
        <el-input
          v-model="modelValue.ingredients"
          placeholder="请输入核心成分，如：烟酰胺、维生素C"
          clearable
        />
      </el-form-item>

      <el-form-item label="主打功效" prop="efficacy">
        <el-input
          v-model="modelValue.efficacy"
          placeholder="请输入主打功效，如：提亮肤色、保湿"
          clearable
        />
      </el-form-item>

      <el-form-item label="目标市场" prop="market">
        <el-select
          v-model="modelValue.market"
          placeholder="请选择目标市场"
          clearable
          style="width:100%"
        >
          <el-option label="欧盟 (EU)" value="EU" />
          <el-option label="美国 (US)" value="US" />
          <el-option label="泰国 (TH)" value="TH" />
        </el-select>
      </el-form-item>

      <el-form-item label="目标平台" prop="platforms">
        <el-checkbox-group v-model="modelValue.platforms">
          <el-checkbox value="amazon" label="亚马逊" />
          <el-checkbox value="alibaba" label="国际站" />
          <el-checkbox value="tiktok" label="TikTok" />
        </el-checkbox-group>
      </el-form-item>
    </el-form>

    <div class="action-buttons">
      <button class="btn btn-primary" :disabled="isLoading" @click="handleGenerate">
        <template v-if="isLoading">
          <span class="btn-loading"></span>
          生成中...
        </template>
        <template v-else>
          <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <path d="M12 3a9 9 0 1 0 9 9"/>
            <polyline points="21 12 21 3 12 3"/>
          </svg>
          生成
        </template>
      </button>
      <button class="btn btn-secondary" :disabled="isLoading" @click="handleReset">
        <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
          <polyline points="23 4 23 10 17 10"/>
          <path d="M20.49 15a9 9 0 1 1-2.12-9.36L23 10"/>
        </svg>
        重置
      </button>
    </div>
  </section>
</template>

<script setup>
import { ref } from 'vue'
import { ElMessage } from 'element-plus'

const props = defineProps({
  modelValue: { type: Object, required: true },
  isLoading: { type: Boolean, default: false },
})
const emit = defineEmits(['generate', 'reset', 'update:modelValue'])

const formRef = ref(null)

const rules = {
  productName: [
    { required: true, message: '请输入产品名称', trigger: 'blur' },
    { max: 50, message: '长度不超过 50 个字符', trigger: 'blur' },
  ],
  ingredients: [{ required: true, message: '请输入核心成分', trigger: 'blur' }],
  efficacy: [{ required: true, message: '请输入主打功效', trigger: 'blur' }],
  market: [{ required: true, message: '请选择目标市场', trigger: 'change' }],
  platforms: [{
    type: 'array', required: true,
    validator: (_, v, cb) => { if (!v || !v.length) cb(new Error('请至少选择一个目标平台')); else cb() },
    trigger: 'change',
  }],
}

function handleGenerate() {
  formRef.value.validate((valid) => {
    if (!valid) { ElMessage.warning('请完善表单信息后再生成'); return }
    emit('generate', { ...props.modelValue })
  })
}

function handleReset() {
  formRef.value.resetFields()
  emit('reset')
}
</script>

<style scoped>
.form-panel {
  width: 360px;
  min-width: 280px;
  max-width: 100%;
  flex-shrink: 1;
  background: #fff;
  border-radius: 14px;
  border: 1px solid #e8e2dc;
  padding: 24px;
  display: flex;
  flex-direction: column;
}
.panel-header {
  margin-bottom: 20px;
  padding-bottom: 14px;
  border-bottom: 1px solid #f0eae6;
}
.panel-title {
  font-size: 15px;
  font-weight: 600;
  color: #2c2522;
}
.beauty-form { flex: 1; }
.beauty-form :deep(.el-form-item__label) {
  font-size: 13px;
  font-weight: 500;
  color: #5c4f4a;
  padding-bottom: 4px;
}
.beauty-form :deep(.el-input__wrapper) {
  border-radius: 10px;
  border: 1px solid #e8e2dc;
  box-shadow: none;
  transition: border-color 0.25s, box-shadow 0.25s;
}
.beauty-form :deep(.el-input__wrapper:hover) {
  border-color: #c9b5d0;
}
.beauty-form :deep(.el-input__wrapper.is-focus) {
  border-color: #8b6f8e;
  box-shadow: 0 0 0 3px rgba(139,111,142,0.12);
}
.beauty-form :deep(.el-select .el-input__wrapper),
.beauty-form :deep(.el-checkbox__label) {
  font-size: 13px;
}
.action-buttons {
  flex-shrink: 0;
  display: flex;
  gap: 10px;
  padding-top: 18px;
  border-top: 1px solid #f0eae6;
}
.btn {
  flex: 1;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 6px;
  padding: 10px 16px;
  border: none;
  border-radius: 10px;
  font-size: 14px;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.2s;
}
.btn:disabled { opacity: 0.6; cursor: not-allowed; }
.btn-primary {
  background: #409EFF;
  color: #fff;
}
.btn-primary:hover:not(:disabled) { background: #96cbff; }
.btn-secondary {
  background: #f7f5f3;
  color: #5c4f4a;
  border: 1px solid #e8e2dc;
}
.btn-secondary:hover:not(:disabled) {
  background: #f0ece8;
  border-color: #d4ccc7;
}
.btn-loading {
  width: 14px; height: 14px;
  border: 2px solid rgba(255,255,255,0.3);
  border-top-color: #fff;
  border-radius: 50%;
  animation: spin 0.7s linear infinite;
}
@keyframes spin { to { transform: rotate(360deg); } }
</style>
