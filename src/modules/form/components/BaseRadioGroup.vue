<script setup lang="ts">
import type { ModelValue, RadioOption } from '../types'

interface Props {
  options: RadioOption[]
  name: string
  modelValue: ModelValue
  vertical?: boolean
}

withDefaults(defineProps<Props>(), {
  vertical: false,
})

const emit = defineEmits<{
  (event: 'update:modelValue', value: ModelValue): void
}>()

function updateModelValue(value: ModelValue) {
  emit('update:modelValue', value)
}
</script>

<template>
  <component
    :is="vertical ? 'div' : 'span'"
    v-for="option in options"
    :key="option.value"
    :class="{ horizontal: !vertical }"
  >
    <BaseRadio
      :label="option.label"
      :value="option.value"
      :name="name"
      :model-value="modelValue"
      @update:modelValue="updateModelValue(option.value)"
    />
  </component>
</template>

<style scoped>
.horizontal {
  @apply mr-5;
}
</style>
