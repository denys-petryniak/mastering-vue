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
