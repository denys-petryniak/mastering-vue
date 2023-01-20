<script setup lang="ts">
import { v4 as uuidv4 } from 'uuid'

interface Props {
  label?: string
  modelValue: boolean
}

withDefaults(defineProps<Props>(), {
  modelValue: false,
})

const emit = defineEmits<{
  (event: 'update:modelValue', value: boolean): void
}>()

const uuid = uuidv4()

function updateModelValue(event: Event) {
  emit('update:modelValue', (event.target as HTMLInputElement).checked)
}
</script>

<template>
  <input
    :id="uuid"
    :checked="modelValue"
    type="checkbox"
    class="checkbox"
    @change="updateModelValue"
  >
  <label
    v-if="label"
    :for="uuid"
    class="inline-label"
  >{{ label }}</label>
</template>
