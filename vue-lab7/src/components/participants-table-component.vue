<template>
  <div class="bg-white shadow-md rounded-lg p-4">
    <h2 class="text-xl font-bold mb-4">Participants</h2>
    <div class="mb-4 flex justify-between items-center">
      <search-bar-component @filter-by-name="setSearchTerm" />
      <div>
        <base-button-component @click="sortBy('name')">
          Sort by Name
          <i :class="getSortIcon('name')"></i>
        </base-button-component>
        <base-button-component @click="sortBy('dateOfBirth')">
          Sort by Date of Birth
          <i :class="getSortIcon('dateOfBirth')"></i>
        </base-button-component>
      </div>
    </div>
    <table class="min-w-full divide-y divide-gray-200">
      <thead class="bg-gray-50">
        <tr>
          <th
            class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider"
          >
            #
          </th>
          <th
            class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider"
          >
            Name
          </th>
          <th
            class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider"
          >
            Date of Birth
          </th>
          <th
            class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider"
          >
            Email
          </th>
          <th
            class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider"
          >
            Phone number
          </th>
          <th
            class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider"
          >
            Actions
          </th>
        </tr>
      </thead>
      <tbody class="bg-white divide-y divide-gray-200">
        <tr
          v-for="(participant, index) in filteredParticipants"
          :key="participant.id"
        >
          >
          <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">
            {{ index + 1 }}
          </td>
          <td
            class="px-6 py-4 whitespace-nowrap text-sm font-medium"
            :class="{
              'text-green-900': isWinner(participant),
              'text-gray-900': !isWinner(participant),
            }"
          >
            {{ participant.name }}
          </td>
          <td
            class="px-6 py-4 whitespace-nowrap text-sm"
            :class="{
              'text-green-900': isWinner(participant),
              'text-gray-500': !isWinner(participant),
            }"
          >
            {{ formatDate(participant.dateOfBirth) }}
          </td>
          <td
            class="px-6 py-4 whitespace-nowrap text-sm"
            :class="{
              'text-green-900': isWinner(participant),
              'text-gray-500': !isWinner(participant),
            }"
          >
            {{ participant.email }}
          </td>
          <td
            class="px-6 py-4 whitespace-nowrap text-sm"
            :class="{
              'text-green-900': isWinner(participant),
              'text-gray-500': !isWinner(participant),
            }"
          >
            {{ participant.phone }}
          </td>
          <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">
            <base-button-component @click="editParticipant(participant)"
              >Edit</base-button-component
            >
            <base-button-component
              @click="deleteParticipant(participant)"
              variant="secondary"
              >Delete</base-button-component
            >
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script lang="ts">
import { defineComponent, computed, ref, type PropType } from 'vue'
import type { Participant } from '@/types'
import BaseButtonComponent from './base-button-component.vue'
import SearchBarComponent from './search-bar-component.vue'

export default defineComponent({
  name: 'ParticipantsTableComponent',
  components: {
    BaseButtonComponent,
    SearchBarComponent,
  },
  props: {
    participants: {
      type: Array as PropType<Participant[]>,
      required: true,
    },
    winners: {
      type: Array as PropType<Participant[]>,
      required: true,
    },
  },
  emits: ['edit', 'delete'],
  setup(props, { emit }) {
    const searchTerm = ref('')
    const sortKey = ref<'name' | 'dateOfBirth'>('name')
    const sortDirection = ref<'asc' | 'desc'>('asc')

    const setSearchTerm = (term: string) => {
      searchTerm.value = term
    }

    const sortBy = (key: 'name' | 'dateOfBirth') => {
      if (sortKey.value === key) {
        sortDirection.value = sortDirection.value === 'asc' ? 'desc' : 'asc'
      } else {
        sortKey.value = key
        sortDirection.value = 'asc'
      }
    }

    const getSortIcon = (key: 'name' | 'dateOfBirth') => {
      if (sortKey.value !== key) return 'fa fa-sort'
      return sortDirection.value === 'asc' ? 'fa fa-sort-up' : 'fa fa-sort-down'
    }

    const filteredParticipants = computed(() => {
      const result = props.participants.filter(p =>
        p.name.toLowerCase().includes(searchTerm.value.toLowerCase()),
      )

      result.sort((a, b) => {
        let compareResult = 0
        if (sortKey.value === 'name') {
          compareResult = a.name.localeCompare(b.name)
        } else if (sortKey.value === 'dateOfBirth') {
          compareResult =
            new Date(a.dateOfBirth).getTime() -
            new Date(b.dateOfBirth).getTime()
        }
        return sortDirection.value === 'asc' ? compareResult : -compareResult
      })

      return result
    })

    const isWinner = (participant: Participant) => {
      return participant.winner
    }

    const formatDate = (dateString: string) => {
      const date = new Date(dateString)
      return date.toLocaleDateString()
    }

    const editParticipant = (participant: Participant) => {
      emit('edit', participant)
    }

    const deleteParticipant = (participant: Participant) => {
      emit('delete', participant)
    }

    return {
      filteredParticipants,
      setSearchTerm,
      sortBy,
      getSortIcon,
      isWinner,
      formatDate,
      editParticipant,
      deleteParticipant,
    }
  },
})
</script>
