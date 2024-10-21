<template>
    <div class="mb-8 bg-white shadow-md rounded-lg p-4">
      <h2 class="text-xl font-bold mb-4">
        {{ isEditing ? 'Edit Participant' : 'REGISTER FORM' }}
      </h2>
      <p class="text-gray-600 mb-4">Please fill in all the fields.</p>
      <form @submit.prevent="submitForm" class="space-y-4">
        <div>
          <base-input-component
            v-model="form.name"
            label="Name"
            id="name"
            type="text"
            required
          />
          <div
            v-if="errors.name"
            class="mt-1 bg-red-100 border border-red-400 text-red-700 px-4 py-3 rounded relative"
            role="alert"
          >
            <p>{{ errors.name }}</p>
          </div>
        </div>
  
        <div>
          <base-input-component
            v-model="form.dateOfBirth"
            label="Date of Birth"
            id="dateOfBirth"
            type="date"
            required
          />
          <div
            v-if="errors.dateOfBirth"
            class="mt-1 bg-red-100 border border-red-400 text-red-700 px-4 py-3 rounded relative"
            role="alert"
          >
            <p>{{ errors.dateOfBirth }}</p>
          </div>
        </div>
  
        <div>
          <base-input-component
            v-model="form.email"
            label="Email"
            id="email"
            type="email"
            required
            :disabled="isEditing"
            :readonly="isEditing"
          />
          <div
            v-if="errors.email"
            class="mt-1 bg-red-100 border border-red-400 text-red-700 px-4 py-3 rounded relative"
            role="alert"
          >
            <p>{{ errors.email }}</p>
          </div>
        </div>
  
        <div>
          <base-input-component
            v-model="form.phone"
            label="Phone number"
            id="phone"
            type="tel"
            required
          />
          <div
            v-if="errors.phone"
            class="mt-1 bg-red-100 border border-red-400 text-red-700 px-4 py-3 rounded relative"
            role="alert"
          >
            <p>{{ errors.phone }}</p>
          </div>
        </div>
  
        <base-button-component type="submit">{{
          isEditing ? 'Update' : 'Save'
        }}</base-button-component>
      </form>
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
  
  interface FormErrors {
    name: string
    dateOfBirth: string
    email: string
    phone: string
  }
  
  export default defineComponent({
    name: 'EditFormComponent',
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
      const errors = ref<FormErrors>({
        name: '',
        dateOfBirth: '',
        email: '',
        phone: '',
      })
      const isEditing = ref(false)
      const originalEmail = ref('')
  
      watch(
        () => props.initialData,
        newValue => {
          if (newValue) {
            form.value = { ...newValue }
            originalEmail.value = newValue.email
            isEditing.value = true
          } else {
            form.value = {
              id: 0,
              name: '',
              dateOfBirth: '',
              email: '',
              phone: '',
            }
            originalEmail.value = ''
            isEditing.value = false
          }
          // Clear errors when form is reset or populated with new data
          Object.keys(errors.value).forEach(key => {
            errors.value[key as keyof FormErrors] = ''
          })
        },
        { immediate: true },
      )
  
      const validateForm = (): boolean => {
        const newErrors: FormErrors = {
          name: '',
          dateOfBirth: '',
          email: '',
          phone: '',
        }
        const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/
        const phoneRegex = /^\d{10}$/
  
        if (!form.value.name.trim()) newErrors.name = 'Name is required'
        if (!form.value.dateOfBirth)
          newErrors.dateOfBirth = 'Date of Birth is required'
        if (!isEditing.value) {
          if (!form.value.email.trim()) {
            newErrors.email = 'Email is required'
          } else if (!emailRegex.test(form.value.email)) {
            newErrors.email = 'Invalid email format'
          }
        } else if (form.value.email !== originalEmail.value) {
          newErrors.email = 'Email cannot be changed'
        }
        if (!form.value.phone.trim()) {
          newErrors.phone = 'Phone number is required'
        } else if (!phoneRegex.test(form.value.phone)) {
          newErrors.phone = 'Invalid phone number format (10 digits required)'
        }
  
        if (form.value.dateOfBirth) {
          const today = new Date()
          const birthDate = new Date(form.value.dateOfBirth)
          if (birthDate > today) {
            newErrors.dateOfBirth = 'Date of Birth cannot be in the future'
          }
  
          const maxAge = new Date()
          maxAge.setFullYear(maxAge.getFullYear() - 150)
          if (birthDate < maxAge) {
            newErrors.dateOfBirth =
              'Date of Birth cannot be more than 150 years ago'
          }
        }
  
        errors.value = newErrors
        return !Object.values(newErrors).some(error => error !== '')
      }
  
      const submitForm = () => {
        if (validateForm()) {
          if (isEditing.value) {
            // Ensure email hasn't changed
            form.value.email = originalEmail.value
          }
          emit('submit', { ...form.value })
          if (!isEditing.value) {
            form.value = {
              id: 0,
              name: '',
              dateOfBirth: '',
              email: '',
              phone: '',
            }
            // Clear errors after successful submission
            Object.keys(errors.value).forEach(key => {
              errors.value[key as keyof FormErrors] = ''
            })
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
  