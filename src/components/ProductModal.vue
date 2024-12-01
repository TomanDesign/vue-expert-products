<template>
    <div
      class="fixed inset-0 z-50 flex items-center justify-center bg-black bg-opacity-25"
      @click.self="$emit('close')"
    >
      <div
        class="bg-white rounded-lg shadow-xl w-[300px] p-6 relative"
        @click.stop
      >
        <button
          @click="$emit('close')"
          class="absolute top-4 right-4 text-gray-500 hover:text-gray-700 transition-colors"
        >
          <XIcon class="w-6 h-6" />
        </button>

        <h2 class="text-2xl font-bold mb-6 text-center text-gray-800">
          Edycja produktu
        </h2>

        <Notification :notifications="notifications" />

        <div class="space-y-4">
          <div>
            <label class="block mb-2 text-sm font-medium text-gray-700">
              Nazwa produktu
            </label>
            <InputField
              v-model="editedName"
              placeholder="Nazwa produktu"
            />
          </div>

          <div>
            <label class="block mb-2 text-sm font-medium text-gray-700">
              Ilość
            </label>
            <InputField
              v-model="editedQuantity"
              type="number"
              placeholder="Ilość"
            />
          </div>

          <div class="flex justify-between space-x-4">
            <DangerButton @click="$emit('remove')">
              <TrashIcon class="mr-2 w-5 h-5" />
              Usuń
            </DangerButton>

            <PrimaryButton @click="saveChanges">
              <SaveIcon class="mr-2 w-5 h-5" />
              Zapisz
            </PrimaryButton>
          </div>
        </div>
      </div>
    </div>
  </template>

  <script setup>
  import { ref, watch } from 'vue'
  import InputField from './inputs/InputField.vue'
  import PrimaryButton from './buttons/PrimaryButton.vue'
  import DangerButton from './buttons/DangerButton.vue'
  import Notification from './Notification.vue'

  const props = defineProps({
    product: {
      type: Object,
      required: true
    }
  })

  const emit = defineEmits(['close', 'save', 'remove'])

  const editedName = ref(props.product.name)
  const editedQuantity = ref(props.product.quantity)
  const notifications = ref([])

  const addNotification = (message, type) => {
    const existingIndex = notifications.value.findIndex(notif => notif.type === type)

    if (existingIndex !== -1) {
      notifications.value[existingIndex] = { message, type }
    } else {
      notifications.value.push({ message, type })
    }

    setTimeout(() => {
      notifications.value = notifications.value.filter(notif => notif.type !== type)
    }, 3000)
  }

  const saveChanges = () => {
    if (!editedName.value || (editedQuantity.value === null || !editedQuantity.value)) {
      addNotification(`Proszę wypełnić wszystkie pola`, 'success')
      return
    }

    emit('save', {
      name: editedName.value,
      quantity: editedQuantity.value,
      addedAt: props.product.addedAt
    })
  }
  </script>