<template>
  <div class="mb-8 bg-white shadow-md rounded-lg p-4">
    <h2 class="text-xl font-bold mb-4">
      {{ isEditing ? 'Edit Participant' : 'REGISTER FORM' }}
    </h2>
    <p class="text-gray-600 mb-4">Please fill in all the fields.</p>
    <form @submit.prevent="submitForm" class="space-y-4">
      <base-input-component
        v-model="form.name"
        label="Name"
        id="name"
        type="text"
        required
      />
      <base-input-component
        v-model="form.dateOfBirth"
        label="Date of Birth"
        id="dateOfBirth"
        type="date"
        required
      />
      <base-input-component
        v-model="form.email"
        label="Email"
        id="email"
        type="email"
        required
      />
      <base-input-component
        v-model="form.phone"
        label="Phone number"
        id="phone"
        type="tel"
        required
      />
      <base-button-component type="submit">{{
        isEditing ? 'Update' : 'Save'
      }}</base-button-component>
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
</template>

<script lang="ts">
import { defineComponent, ref, watch, type PropType } from 'vue'
import BaseInputComponent from './base-input-component.vue'
import BaseButtonComponent from './base-button-component.vue'

interface Participant {
  id: number
  name: string
  dateOfBirth: string
  email: string
  phone: string
}

export default defineComponent({
  name: 'RegistrationFormComponent',
  components: {
    BaseInputComponent,
    BaseButtonComponent,
  },
  props: {
    initialData: {
      type: Object as PropType<Participant | null>,
      default: null,
    },
  },
  emits: ['submit'],
  setup(props, { emit }) {
    const form = ref<Participant>({
      id: 0,
      name: '',
      dateOfBirth: '',
      email: '',
      phone: '',
    })
    const errors = ref<string[]>([])
    const isEditing = ref(false)

    watch(
      () => props.initialData,
      newValue => {
        if (newValue) {
          form.value = { ...newValue }
          isEditing.value = true
        } else {
          form.value = {
            id: 0,
            name: '',
            dateOfBirth: '',
            email: '',
            phone: '',
          }
          isEditing.value = false
        }
      },
      { immediate: true },
    )

    const validateForm = (): boolean => {
      errors.value = []
      const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/
      const phoneRegex = /^\d{10}$/

      if (!form.value.name) errors.value.push('Name is required')
      if (!form.value.dateOfBirth)
        errors.value.push('Date of Birth is required')
      if (!emailRegex.test(form.value.email))
        errors.value.push('Invalid email format')
      if (!phoneRegex.test(form.value.phone))
        errors.value.push('Invalid phone number format')

      const today = new Date()
      const birthDate = new Date(form.value.dateOfBirth)
      if (birthDate > today)
        errors.value.push('Date of Birth cannot be in the future')

      const maxAge = new Date()
      maxAge.setFullYear(maxAge.getFullYear() - 150)
      if (birthDate < maxAge)
        errors.value.push('Date of Birth cannot be more than 150 years ago')

      return errors.value.length === 0
    }

    const submitForm = () => {
      if (validateForm()) {
        emit('submit', { ...form.value })
        if (!isEditing.value) {
          form.value = {
            id: 0,
            name: '',
            dateOfBirth: '',
            email: '',
            phone: '',
          }
        }
      }
    }

    return {
      form,
      errors,
      isEditing,
      submitForm,
    }
  },
})
</script>
