<script setup lang="ts">
import { v4 as uuidv4 } from 'uuid'

const props = defineProps<Props>()

const emit = defineEmits<{
  (e: 'update:modelValue', value: modelValue): void
}>()

const uuid = uuidv4()

type modelValue = string | number

interface Props {
  label?: string
  modelValue: modelValue
  value: modelValue
}

function updateModelValue() {
  emit('update:modelValue', props.value)
}
</script>

<template>
  <input
    v-bind="$attrs"
    :id="uuid"
    type="radio"
    :value="value"
    :checked="modelValue === value"
    @change="updateModelValue"
  >
  <label
    v-if="label"
    :for="uuid"
  >{{ label }}</label>
</template>
