<template>
  <span class="highlight-wrapper">
    <template v-for="(seg, i) in segments" :key="i">
      <template v-if="seg.type === 'normal'">{{ seg.value }}</template>
      <el-tooltip
        v-else
        :content="'⚠ 建议替换为：' + seg.suggestion"
        placement="top"
        :show-after="200"
        :popper-class="'violation-tip'"
      >
        <span class="violation-word">{{ seg.value }}</span>
      </el-tooltip>
    </template>
  </span>
</template>

<script setup>
import { computed } from 'vue'

const props = defineProps({ text: { type: String, default: '' } })

const WORD_DEFS = [
  { pat: 'whitening', sug: 'brightening / radiance-enhancing' },
  { pat: 'anti[\\s-]?aging', sug: 'age-defying / timeless' },
  { pat: 'cure', sug: 'improve / support' },
  { pat: 'heal(?:s|ing)?', sug: 'soothe / restore / calm' },
  { pat: 'treat(?:ment)?s?', sug: 'care / regimen' },
  { pat: 'detox', sug: 'purify / cleanse' },
  { pat: 'therapeutic', sug: 'nourishing / soothing' },
  { pat: 'medical', sug: 'clinical-graded / professional' },
  { pat: 'perfect', sug: 'advanced / refined' },
  { pat: 'miracle', sug: 'remarkable / effective' },
  { pat: 'instant', sug: 'gradual / progressive' },
  { pat: 'eliminate\\s+wrinkles', sug: 'reduce appearance of fine lines' },
]

const patterns = WORD_DEFS.map((d) => ({
  regex: new RegExp('\\b' + d.pat + '\\b', 'gi'),
  sug: d.sug,
}))

const segments = computed(() => {
  const t = props.text
  if (!t) return [{ type: 'normal', value: '' }]
  const matches = []
  for (const p of patterns) {
    p.regex.lastIndex = 0
    let m
    while ((m = p.regex.exec(t)) !== null) {
      matches.push({ start: m.index, end: m.index + m[0].length, value: m[0], sug: p.sug })
    }
  }
  if (!matches.length) return [{ type: 'normal', value: t }]
  matches.sort((a, b) => a.start - b.start)
  const merged = []
  for (const m of matches) {
    if (merged.length && m.start < merged[merged.length - 1].end) {
      if (m.end > merged[merged.length - 1].end) merged[merged.length - 1] = m
    } else { merged.push(m) }
  }
  const segs = []
  let pos = 0
  for (const m of merged) {
    if (m.start > pos) segs.push({ type: 'normal', value: t.slice(pos, m.start) })
    segs.push({ type: 'violation', value: m.value, suggestion: m.sug })
    pos = m.end
  }
  if (pos < t.length) segs.push({ type: 'normal', value: t.slice(pos) })
  return segs
})
</script>

<style scoped>
.violation-word {
  color: #c95d63;
  font-weight: 600;
  text-decoration: underline wavy #c95d6380;
  cursor: pointer;
  padding: 0 2px;
  border-radius: 2px;
  transition: background-color 0.2s, color 0.2s;
}
.violation-word:hover {
  background-color: #fdf0f0;
  color: #b8454b;
}
</style>
