<template>
  <div
    class="bg-gray-100 rounded-full px-3 py-1 text-sm font-semibold text-gray-700 flex items-center"
  >
    {{ winner.name }}
    <button
      @click="$emit('remove', winner.id)"
      class="ml-2 text-red-500 font-bold"
    >
      &times;
    </button>
  </div>
</template>

<script lang="ts">
import { defineComponent, PropType } from 'vue'
import type { Participant } from '@/types'

export default defineComponent({
  name: 'WinnerItemComponent',
  props: {
    winner: {
      type: Object as PropType<Participant>,
      required: true,
    },
  },
  emits: ['remove'],
})
</script>
