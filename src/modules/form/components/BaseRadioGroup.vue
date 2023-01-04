<script setup lang="ts">
type modelValue = string | number

interface Option {
  label: string
  value: modelValue
}

interface Props {
  options: Option[]
  name: string
  modelValue: modelValue
  vertical?: boolean
}

withDefaults(defineProps<Props>(), {
  vertical: false,
})

const emit = defineEmits<{
  (e: 'update:modelValue', value: modelValue): void
}>()

function updateModelValue(value: modelValue) {
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
