<template>
  <div class="mb-8">
    <Notification :notifications="notifications" />
    <h2 class="text-xl font-normal text-gray-800 mb-6 text-center">Dodaj nowy produkt</h2>
    <div class="flex flex-col sm:flex-row items-end space-y-4 sm:space-y-0 sm:space-x-4">
      <div class="flex flex-col w-full">
        <label class="mb-2 text-sm font-medium text-gray-700">Nazwa produktu</label>
        <InputField v-model="productName" placeholder="Wprowadź nazwę produktu" />
      </div>
      <div class="flex flex-col w-full sm:w-1/3">
        <label class="mb-2 text-sm font-medium text-gray-700">Ilość</label>
        <InputField v-model="productQuantity" type="number" placeholder="Ilość" min="1" max="99" />
      </div>
      <PrimaryButton @click="addProduct" class="mt-4 sm:mt-0">
        <PlusIcon class="mr-2 w-5 h-5" />
        Dodaj produkt
      </PrimaryButton>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { PlusIcon } from 'lucide-vue-next'
import PrimaryButton from './buttons/PrimaryButton.vue'
import InputField from './inputs/InputField.vue'
import Notification from './Notification.vue'

const emit = defineEmits(['product-added'])

const productName = ref('')
const productQuantity = ref(null)
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

const addProduct = () => {
  if (!productName.value || productQuantity.value === null || productQuantity.value <= 0) {
    addNotification('Proszę wypełnić wszystkie pola poprawnie', 'error')
    return
  }

  emit('product-added', {
    name: productName.value,
    quantity: Number(productQuantity.value)
  })

  addNotification(`Dodano produkt: ${productName.value}`, 'success')

  productName.value = ''
  productQuantity.value = null
}
</script>
