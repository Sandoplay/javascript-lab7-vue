<template>
  <div id="app" class="container mx-auto p-4">
    <!-- Winners Block -->
    <div class="mb-8 bg-white shadow-md rounded-lg p-4">
      <div class="flex justify-between items-center mb-4">
        <h2 class="text-xl font-bold">Winners</h2>
        <button
          @click="selectNewWinner"
          :disabled="isNewWinnerDisabled"
          class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded disabled:opacity-50 disabled:cursor-not-allowed"
        >
          New winner
        </button>
      </div>
      <div v-if="winners.length" class="flex space-x-2">
        <div
          v-for="winner in winners"
          :key="winner.id"
          class="bg-gray-100 rounded-full px-3 py-1 text-sm font-semibold text-gray-700"
        >
          {{ winner.name }}
          <button @click="removeWinner(winner.id)" class="ml-2 text-red-500">
            &times;
          </button>
        </div>
      </div>
    </div>

    <!-- Registration Form Block -->
    <div class="mb-8 bg-white shadow-md rounded-lg p-4">
      <h2 class="text-xl font-bold mb-4">REGISTER FORM</h2>
      <p class="text-gray-600 mb-4">Please fill in all the fields.</p>
      <form @submit.prevent="submitForm" class="space-y-4">
        <div>
          <label for="name" class="block text-sm font-medium text-gray-700">Name</label>
          <input
            v-model="form.name"
            type="text"
            id="name"
            required
            class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-indigo-300 focus:ring focus:ring-indigo-200 focus:ring-opacity-50"
          >
        </div>
        <div>
          <label for="dateOfBirth" class="block text-sm font-medium text-gray-700">Date of Birth</label>
          <input
            v-model="form.dateOfBirth"
            type="date"
            id="dateOfBirth"
            required
            class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-indigo-300 focus:ring focus:ring-indigo-200 focus:ring-opacity-50"
          >
        </div>
        <div>
          <label for="email" class="block text-sm font-medium text-gray-700">Email</label>
          <input
            v-model="form.email"
            type="email"
            id="email"
            required
            class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-indigo-300 focus:ring focus:ring-indigo-200 focus:ring-opacity-50"
          >
        </div>
        <div>
          <label for="phone" class="block text-sm font-medium text-gray-700">Phone number</label>
          <input
            v-model="form.phone"
            type="tel"
            id="phone"
            required
            class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-indigo-300 focus:ring focus:ring-indigo-200 focus:ring-opacity-50"
          >
        </div>
        <div>
          <button
            type="submit"
            class="w-full bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded"
          >
            Save
          </button>
        </div>
      </form>
      <div
        v-if="errors.length"
        class="mt-4 bg-red-100 border border-red-400 text-red-700 px-4 py-3 rounded relative"
        role="alert"
      >
        <strong class="font-bold">Please correct the following error(s):</strong>
        <ul class="mt-2 list-disc list-inside">
          <li v-for="error in errors" :key="error">{{ error }}</li>
        </ul>
      </div>
    </div>

    <!-- Participants List Block -->
    <div class="bg-white shadow-md rounded-lg p-4">
      <h2 class="text-xl font-bold mb-4">Participants</h2>
      <table class="min-w-full divide-y divide-gray-200">
        <thead class="bg-gray-50">
          <tr>
            <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">#</th>
            <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Name</th>
            <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Date of Birth</th>
            <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Email</th>
            <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Phone number</th>
          </tr>
        </thead>
        <tbody class="bg-white divide-y divide-gray-200">
          <tr
            v-for="(participant, index) in participants"
            :key="participant.id"
          >
            <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">{{ index + 1 }}</td>
            <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">{{ participant.name }}</td>
            <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">{{ participant.dateOfBirth }}</td>
            <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">{{ participant.email }}</td>
            <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">{{ participant.phone }}</td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, computed } from 'vue'

interface Participant {
  id: number
  name: string
  dateOfBirth: string
  email: string
  phone: string
}

export default defineComponent({
  name: 'App',
  setup() {
    const participants = ref<Participant[]>([
      {
        id: 1,
        name: 'John Doe',
        dateOfBirth: '1990-01-01',
        email: 'john@example.com',
        phone: '1234567890',
      },
      {
        id: 2,
        name: 'Jane Smith',
        dateOfBirth: '1985-05-15',
        email: 'jane@example.com',
        phone: '9876543210',
      },
      {
        id: 3,
        name: 'Bob Johnson',
        dateOfBirth: '1995-12-31',
        email: 'bob@example.com',
        phone: '5555555555',
      },
    ])
    const winners = ref<Participant[]>([])
    const errors = ref<string[]>([])

    const form = ref({
      name: '',
      dateOfBirth: '',
      email: '',
      phone: '',
    })

    const isNewWinnerDisabled = computed(() => {
      return winners.value.length >= 3 || participants.value.length === 0
    })

    const validateForm = (): string[] => {
      const newErrors: string[] = []
      const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/
      const phoneRegex = /^\d{10}$/

      if (!form.value.name) newErrors.push('Name is required')
      if (!form.value.dateOfBirth) newErrors.push('Date of Birth is required')
      if (!emailRegex.test(form.value.email)) newErrors.push('Invalid email format')
      if (!phoneRegex.test(form.value.phone)) newErrors.push('Invalid phone number format')

      const today = new Date()
      const birthDate = new Date(form.value.dateOfBirth)
      
      if (birthDate > today) {
        newErrors.push('Date of Birth cannot be in the future')
      }
      
      const maxAge = new Date()
      maxAge.setFullYear(maxAge.getFullYear() - 150)
      if (birthDate < maxAge) {
        newErrors.push('Date of Birth cannot be more than 150 years ago')
      }

      return newErrors
    }

    const submitForm = () => {
      errors.value = validateForm()
      if (errors.value.length === 0) {
        participants.value.push({
          ...form.value,
          id: Date.now(),
        })
        resetForm()
      }
    }

    const resetForm = () => {
      form.value = {
        name: '',
        dateOfBirth: '',
        email: '',
        phone: '',
      }
      errors.value = []
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

    return {
      participants,
      winners,
      errors,
      form,
      isNewWinnerDisabled,
      submitForm,
      selectNewWinner,
      removeWinner,
    }
  },
})
</script>
