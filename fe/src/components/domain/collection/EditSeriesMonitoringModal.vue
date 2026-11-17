<template>
  <Modal :visible="visible" title="Edit Monitoring" size="sm" @close="emit('close')">
    <form class="edit-series-monitoring" @submit.prevent="submit">
      <label>
        Region
        <select v-model="region" name="region">
          <option v-for="option in searchRegionOptions" :key="option.value" :value="option.value">
            {{ option.label }}
          </option>
        </select>
      </label>
      <label>
        Language
        <select v-model="language" name="language">
          <option
            v-for="option in preferredSearchLanguageOptions"
            :key="option.value"
            :value="option.value"
          >
            {{ option.label }}
          </option>
        </select>
      </label>
      <p class="hint">Saves the existing monitor and syncs without starting downloads.</p>
      <div class="actions">
        <button type="button" class="toolbar-btn" @click="emit('close')">Cancel</button>
        <button type="submit" class="toolbar-btn active" :disabled="busy">Save</button>
      </div>
    </form>
  </Modal>
</template>

<script setup lang="ts">
import { ref, watch } from 'vue'
import Modal from '@/components/feedback/Modal.vue'
import {
  normalizePreferredSearchLanguage,
  normalizeSearchRegion,
  preferredSearchLanguageOptions,
  searchRegionOptions,
} from '@/utils/languageMapping'

const props = defineProps<{
  visible: boolean
  region: string
  language: string
  busy?: boolean
}>()

const emit = defineEmits<{
  close: []
  save: [payload: { region: string; language: string }]
}>()

const region = ref('us')
const language = ref('english')

watch(
  () => [props.visible, props.region, props.language] as const,
  () => {
    if (!props.visible) return
    region.value = normalizeSearchRegion(props.region)
    language.value = normalizePreferredSearchLanguage(props.language)
  },
  { immediate: true },
)

function submit() {
  emit('save', { region: region.value, language: language.value })
}
</script>

<style scoped>
.edit-series-monitoring {
  display: flex;
  flex-direction: column;
  gap: 12px;
  padding: 16px;
}
label {
  display: flex;
  flex-direction: column;
  gap: 6px;
}
select {
  padding: 8px;
}
.hint {
  margin: 0;
  color: #9aa4b2;
  font-size: 12px;
}
.actions {
  display: flex;
  justify-content: flex-end;
  gap: 8px;
}
</style>
