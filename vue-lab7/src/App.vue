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
    <form @submit.prevent="submitForm" class="space-y-4" novalidate>
      <div>
        <label for="name" class="block text-sm font-medium text-gray-700">Name</label>
        <input
          v-model="form.name"
          type="text"
          id="name"
          class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-indigo-300 focus:ring focus:ring-indigo-200 focus:ring-opacity-50"
          @blur="validateField('name')"
        >
        <p v-if="errors.name" class="mt-1 text-sm text-red-600">{{ errors.name }}</p>
      </div>
      <div>
        <label for="dateOfBirth" class="block text-sm font-medium text-gray-700">Date of Birth</label>
        <input
          v-model="form.dateOfBirth"
          type="date"
          id="dateOfBirth"
          class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-indigo-300 focus:ring focus:ring-indigo-200 focus:ring-opacity-50"
          @blur="validateField('dateOfBirth')"
        >
        <p v-if="errors.dateOfBirth" class="mt-1 text-sm text-red-600">{{ errors.dateOfBirth }}</p>
      </div>
      <div>
        <label for="email" class="block text-sm font-medium text-gray-700">Email</label>
        <input
          v-model="form.email"
          type="email"
          id="email"
          class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-indigo-300 focus:ring focus:ring-indigo-200 focus:ring-opacity-50"
          @blur="validateField('email')"
        >
        <p v-if="errors.email" class="mt-1 text-sm text-red-600">{{ errors.email }}</p>
      </div>
      <div>
        <label for="phone" class="block text-sm font-medium text-gray-700">Phone number</label>
        <input
          v-model="form.phone"
          type="tel"
          id="phone"
          class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-indigo-300 focus:ring focus:ring-indigo-200 focus:ring-opacity-50"
          @blur="validateField('phone')"
        >
        <p v-if="errors.phone" class="mt-1 text-sm text-red-600">{{ errors.phone }}</p>
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
            :class="{ 'bg-green-100': isWinner(participant) }"
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

interface FormErrors {
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
    const errors = ref<FormErrors>({
      name: '',
      dateOfBirth: '',
      email: '',
      phone: '',
    })

    const form = ref({
      name: '',
      dateOfBirth: '',
      email: '',
      phone: '',
    })

    const isNewWinnerDisabled = computed(() => {
      return winners.value.length >= 3 || participants.value.length === 0
    })

    const validateField = (field: keyof FormErrors) => {
      const newErrors = { ...errors.value }
      
      switch (field) {
        case 'name':
          newErrors.name = form.value.name.trim() ? '' : 'Name is required'
          break
        case 'dateOfBirth':
          if (!form.value.dateOfBirth) {
            newErrors.dateOfBirth = 'Date of Birth is required'
          } else {
            const birthDate = new Date(form.value.dateOfBirth)
            const today = new Date()
            const maxAge = new Date()
            maxAge.setFullYear(maxAge.getFullYear() - 150)
            
            if (birthDate > today) {
              newErrors.dateOfBirth = 'Date of Birth cannot be in the future'
            } else if (birthDate < maxAge) {
              newErrors.dateOfBirth = 'Date of Birth cannot be more than 150 years ago'
            } else {
              newErrors.dateOfBirth = ''
            }
          }
          break
        case 'email':
          const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/
          newErrors.email = form.value.email ? (emailRegex.test(form.value.email) ? '' : 'Invalid email format') : 'Email is required'
          break
        case 'phone':
          const phoneRegex = /^\d{10}$/
          newErrors.phone = form.value.phone ? (phoneRegex.test(form.value.phone) ? '' : 'Invalid phone number format (10 digits required)') : 'Phone number is required'
          break
      }
      
      errors.value = newErrors
    }

    const validateForm = (): boolean => {
      ['name', 'dateOfBirth', 'email', 'phone'].forEach(field => validateField(field as keyof FormErrors))
      return !Object.values(errors.value).some(error => error !== '')
    }

    const submitForm = () => {
      if (validateForm()) {
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
      errors.value = {
        name: '',
        dateOfBirth: '',
        email: '',
        phone: '',
      }
    }

    const selectNewWinner = () => {
      if (!isNewWinnerDisabled.value) {
        const availableParticipants = participants.value.filter(p => !isWinner(p))
        if (availableParticipants.length > 0) {
          const randomIndex = Math.floor(Math.random() * availableParticipants.length)
          const winner = availableParticipants[randomIndex]
          winners.value.push(winner)
        }
      }
    }

    const removeWinner = (winnerId: number) => {
      winners.value = winners.value.filter(w => w.id !== winnerId)
    }

    const isWinner = (participant: Participant) => {
      return winners.value.some(w => w.id === participant.id)
    }

    return {
      participants,
      winners,
      errors,
      form,
      isNewWinnerDisabled,
      validateField,
      submitForm,
      selectNewWinner,
      removeWinner,
      isWinner,
    }
  },
})
</script>

<style>
@import 'tailwindcss/base';
@import 'tailwindcss/components';
@import 'tailwindcss/utilities';
</style>