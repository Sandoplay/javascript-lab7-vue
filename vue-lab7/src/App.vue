<template>
  <div id="app" class="container mx-auto p-4">
    <winners-list-component
      :winners="winners"
      :is-new-winner-disabled="isNewWinnerDisabled"
      @select-new-winner="selectNewWinner"
      @remove-winner="removeWinner"
    />

    <registration-form-component @submit="addParticipant" />

    <participants-table-component
      :participants="participants"
      @edit="openEditModal"
      @delete="openDeleteModal"
    />

    <!-- Модальне вікно для редагування -->
    <div
      v-if="isEditModalOpen"
      class="fixed inset-0 bg-gray-600 bg-opacity-50 overflow-y-auto h-full w-full"
    >
      <div
        class="relative top-20 mx-auto p-5 border w-96 shadow-lg rounded-md bg-white"
      >
        <h2 class="text-lg font-bold mb-4">Edit Participant</h2>
        <registration-form-component
          :initial-data="currentParticipant"
          @submit="updateParticipant"
        />
        <base-button-component @click="closeEditModal" class="mt-4"
          >Close</base-button-component
        >
      </div>
    </div>

    <!-- Модальне вікно для видалення -->
    <div
      v-if="isDeleteModalOpen"
      class="fixed inset-0 bg-gray-600 bg-opacity-50 overflow-y-auto h-full w-full"
    >
      <div
        class="relative top-20 mx-auto p-5 border w-96 shadow-lg rounded-md bg-white"
      >
        <h2 class="text-lg font-bold mb-4">Confirm Deletion</h2>
        <p>
          Are you sure you want to delete participant "{{
            currentParticipant?.name
          }}", {{ currentParticipant?.email }}?
        </p>
        <div class="mt-4 flex justify-end space-x-2">
          <base-button-component @click="confirmDelete"
            >Yes</base-button-component
          >
          <base-button-component @click="closeDeleteModal" variant="secondary"
            >No</base-button-component
          >
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import {
  defineComponent,
  ref,
  computed,
  onMounted,
  onUnmounted,
  watch,
} from 'vue'
import type { Participant } from '@/types'
import WinnersListComponent from './components/winners-list-component.vue'
import RegistrationFormComponent from './components/registration-form-component.vue'
import ParticipantsTableComponent from './components/participants-table-component.vue'
import BaseButtonComponent from './components/base-button-component.vue'

export default defineComponent({
  name: 'App',
  components: {
    WinnersListComponent,
    RegistrationFormComponent,
    ParticipantsTableComponent,
    BaseButtonComponent,
  },
  setup() {
    const participants = ref<Participant[]>([])
    const winners = ref<Participant[]>([])
    const isEditModalOpen = ref(false)
    const isDeleteModalOpen = ref(false)
    const currentParticipant = ref<Participant | null>(null)

    const isNewWinnerDisabled = computed(() => {
      return winners.value.length >= 3 || participants.value.length === 0
    })

    const handleKeyDown = (event: KeyboardEvent) => {
      if (event.key === 'Escape') {
        closeEditModal()
        closeDeleteModal()
      }
    }

    onMounted(() => {
      window.addEventListener('keydown', handleKeyDown)
    })

    onUnmounted(() => {
      window.removeEventListener('keydown', handleKeyDown)
    })

    const loadParticipants = () => {
      const savedParticipants = localStorage.getItem('participants')
      if (savedParticipants) {
        participants.value = JSON.parse(savedParticipants)
      }
    }

    const saveParticipants = () => {
      localStorage.setItem('participants', JSON.stringify(participants.value))
    }

    onMounted(() => {
      loadParticipants()
    })

    watch(
      participants,
      () => {
        saveParticipants()
      },
      { deep: true },
    )

    const addParticipant = (newParticipant: Participant) => {
      if (participants.value.some(p => p.email === newParticipant.email)) {
        alert('A participant with this email already exists')
        return
      }
      participants.value.push(newParticipant)
    }

    const selectNewWinner = () => {
      if (!isNewWinnerDisabled.value) {
        const randomIndex = Math.floor(
          Math.random() * participants.value.length,
        )
        const winner = participants.value[randomIndex]
        winners.value.push(winner)
        participants.value = participants.value.filter(p => p.id !== winner.id)
      }
    }

    const removeWinner = (winnerId: number) => {
      const winner = winners.value.find(w => w.id === winnerId)
      if (winner) {
        winners.value = winners.value.filter(w => w.id !== winnerId)
        participants.value.push(winner)
      }
    }

    const openEditModal = (participant: Participant) => {
      currentParticipant.value = { ...participant }
      isEditModalOpen.value = true
    }

    const closeEditModal = () => {
      currentParticipant.value = null
      isEditModalOpen.value = false
    }

    const updateParticipant = (updatedParticipant: Participant) => {
      const index = participants.value.findIndex(
        p => p.id === updatedParticipant.id,
      )
      if (index !== -1) {
        participants.value[index] = updatedParticipant
      }
      closeEditModal()
    }

    const openDeleteModal = (participant: Participant) => {
      currentParticipant.value = participant
      isDeleteModalOpen.value = true
    }

    const closeDeleteModal = () => {
      currentParticipant.value = null
      isDeleteModalOpen.value = false
    }

    const confirmDelete = () => {
      if (currentParticipant.value) {
        participants.value = participants.value.filter(
          p => p.id !== currentParticipant.value!.id,
        )
      }
      closeDeleteModal()
    }

    return {
      participants,
      winners,
      isNewWinnerDisabled,
      isEditModalOpen,
      isDeleteModalOpen,
      currentParticipant,
      addParticipant,
      selectNewWinner,
      removeWinner,
      openEditModal,
      closeEditModal,
      updateParticipant,
      openDeleteModal,
      closeDeleteModal,
      confirmDelete,
    }
  },
})
</script>

<style lang="scss">
@import 'tailwindcss/base';
@import 'tailwindcss/components';
@import 'tailwindcss/utilities';

</style>
