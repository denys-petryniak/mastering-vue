<script setup lang="ts">
import { v4 as uuidv4 } from 'uuid'
import type { ModelValue } from '../types'

interface Props {
  label?: string
  modelValue: ModelValue
  value: ModelValue
}

const props = defineProps<Props>()

const emit = defineEmits<{
  (event: 'update:modelValue', value: ModelValue): void
}>()

const uuid = uuidv4()

function updateModelValue() {
  emit('update:modelValue', props.value)
}
</script>

<template>
  <input
    v-bind="{
      ...$attrs,
      onChange: updateModelValue,
    }"
    :id="uuid"
    :value="value"
    :checked="modelValue === value"
    type="radio"
    class="radio"
  >
  <label
    v-if="label"
    :for="uuid"
    class="inline-label"
  >{{ label }}</label>
</template>
