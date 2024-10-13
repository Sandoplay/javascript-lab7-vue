<template>
  <div class="mb-8 bg-white shadow-md rounded-lg p-4">
    <div class="flex justify-between items-center mb-4">
      <h2 class="text-xl font-bold">Winners</h2>
      <base-button-component
        @click="$emit('select-new-winner')"
        :disabled="isNewWinnerDisabled"
      >
        New winner
      </base-button-component>
    </div>
    <div v-if="winners.length" class="flex flex-wrap gap-2">
      <winner-item-component
        v-for="winner in winners"
        :key="winner.id"
        :winner="winner"
        @remove="$emit('remove-winner', $event)"
      />
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, type PropType } from 'vue'
import type { Participant } from '@/types'
import BaseButtonComponent from './base-button-component.vue'
import WinnerItemComponent from './winner-item-component.vue'

export default defineComponent({
  name: 'WinnersListComponent',
  components: {
    BaseButtonComponent,
    WinnerItemComponent,
  },
  props: {
    winners: {
      type: Array as PropType<Participant[]>,
      required: true,
    },
    isNewWinnerDisabled: {
      type: Boolean,
      required: true,
    },
  },
  emits: ['select-new-winner', 'remove-winner'],
})
</script>
