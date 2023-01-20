<script setup lang="ts">
import { v4 as uuidv4 } from 'uuid'
import type { ErrorObject } from '@vuelidate/core'
import type { ModelValue } from '../types'

interface Props {
  label: string
  modelValue: ModelValue
  errors?: ErrorObject[]
}

const props = defineProps<Props>()

const emit = defineEmits<{
  (event: 'update:modelValue', value: ModelValue): void
}>()

const isError = computed(() => Boolean(props.errors?.length))

const uuid = uuidv4()

function updateModelValue(event: Event) {
  emit('update:modelValue', (event.target as HTMLInputElement).value)
}
</script>

<template>
  <label
    v-if="label"
    :for="uuid"
    class="label"
  >{{ label }}</label>
  <input
    v-bind="{
      ...$attrs,
      onInput: updateModelValue,
    }"
    :id="uuid"
    :value="modelValue"
    :placeholder="label"
    :aria-describedby="isError ? `${uuid}-error` : undefined"
    :class="{ error: isError }"
    :aria-invalid="isError ? true : undefined"
    class="field"
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
