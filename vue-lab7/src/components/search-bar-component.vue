<template>
  <div class="search-bar">
    <base-input-component
      v-model="searchTerm"
      @input="emitSearch"
      placeholder="Search by name"
      id="search"
    />
  </div>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue'
import BaseInputComponent from './base-input-component.vue'

export default defineComponent({
  name: 'SearchBarComponent',
  components: {
    BaseInputComponent,
  },
  emits: ['filter-by-name'],
  setup(_, { emit }) {
    const searchTerm = ref('')

    const emitSearch = () => {
      emit('filter-by-name', searchTerm.value)
    }

    return {
      searchTerm,
      emitSearch,
    }
  },
})
</script>
