<script setup lang="ts">
import type { ErrorObject } from '@vuelidate/core'
import { v4 as uuidv4 } from 'uuid'

const props = defineProps<Props>()

const emit = defineEmits<{
  (e: 'update:modelValue', value: modelValue): void
}>()

const uuid = uuidv4()

type modelValue = string | number

interface Props {
  label: string
  modelValue: modelValue
  errors?: ErrorObject[]
}

function updateModelValue(event: Event) {
  emit('update:modelValue', (event.target as HTMLInputElement).value)
}

const isError = computed(() => Boolean(props.errors?.length))
</script>

<template>
  <label
    v-if="label"
    :for="uuid"
    class="label"
  >{{ label }}</label>
  <input
    v-bind="$attrs"
    :id="uuid"
    :value="modelValue"
    :placeholder="label"
    :aria-describedby="isError ? `${uuid}-error` : undefined"
    :aria-invalid="isError ? true : undefined"
    :class="{ error: isError }"
    class="field"
    @input="updateModelValue"
  >
  <template v-if="isError">
    <p
      v-for="error of errors"
      :id="`${uuid}-error`"
      :key="error.$uid"
      aria-live="assertive"
      class="errorMessage"
    >
      {{ error.$message }}
    </p>
  </template>
</template>

<!-- <style scoped>
.label {
  @apply block text-sm font-medium text-gray-700 dark:text-gray-200;
}
.field {
  @apply transition px-4 py-2 w-[250px] text-center bg-transparent border rounded border-gray-200 hover:ring-2 hover:ring-teal-400 focus:outline-none focus:ring-2 focus:ring-teal-400 dark:border-gray-700;
}
</style> -->
