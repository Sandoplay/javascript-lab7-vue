<template>
  <button
    :class="[
      'px-4 py-2 rounded font-bold transition-colors',
      variant === 'primary'
        ? 'bg-blue-500 text-white hover:bg-blue-600'
        : 'bg-gray-200 text-gray-800 hover:bg-gray-300',
      disabled ? 'opacity-50 cursor-not-allowed' : 'cursor-pointer',
    ]"
    :disabled="disabled"
    @click="$emit('click')"
  >
    <slot></slot>
  </button>
</template>

<script lang="ts">
import { defineComponent } from 'vue'

export default defineComponent({
  name: 'BaseButtonComponent',
  props: {
    variant: {
      type: String,
      default: 'primary',
      validator: (value: string) => ['primary', 'secondary'].includes(value),
    },
    disabled: {
      type: Boolean,
      default: false,
    },
  },
  emits: ['click'],
})
</script>
