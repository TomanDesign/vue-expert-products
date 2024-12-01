<template>
  <div class="overflow-x-auto">
    <h3 class="text-xl font-normal text-gray-800 mb-6 text-center">Lista produktów</h3>

    <!-- Tabela dla szerszych ekranów -->
    <table class="w-full hidden sm:table">
      <thead class="bg-gray-100">
        <tr>
          <th
            v-for="column in columns"
            :key="column.key"
            @click="handleSort(column.key)"
            class="px-4 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider cursor-pointer hover:bg-gray-200 transition-colors"
          >
            <div class="flex items-center">
              {{ column.label }}
              <SortIcon
                v-if="currentSortColumn === column.key"
                :order="sortOrderMap[column.key]"
              />
            </div>
          </th>
          <th class="px-4 py-3 text-xs font-medium text-gray-500 uppercase tracking-wider">
            Akcje
          </th>
        </tr>
      </thead>
      <tbody>
        <tr v-if="sortedProducts.length === 0">
          <td colspan="3" class="text-center p-4 text-gray-500">Brak danych</td>
        </tr>
        <tr
          v-for="(product, index) in sortedProducts"
          :key="index"
          :class="[
            'border-b',
            index % 2 === 0 ? 'bg-white' : 'bg-gray-50'
          ]"
        >
          <td class="px-4 py-3 text-gray-600">{{ product.name }}</td>
          <td class="px-4 py-3 text-gray-600">{{ product.quantity }}</td>
          <td class="px-4 py-3 text-center">
            <SecondaryButton @click="$emit('edit-product', product, index)">
              <EditIcon class="w-5 h-5" />
            </SecondaryButton>
          </td>
        </tr>
        <tr class="font-bold text-gray-700 bg-white border-t-2 border-t-gray-300">
          <td class="px-4 py-3">Suma</td>
          <td class="px-4 py-3">{{ totalQuantity }}</td>
          <td class="px-4 py-3"></td>
        </tr>
      </tbody>
    </table>

    <div class="sm:hidden">
      <div class="bg-gray-100 px-4 py-3 flex justify-between items-center">
        <div
          v-for="column in columns"
          :key="column.key"
          class="text-xs font-medium text-gray-500 uppercase cursor-pointer"
          @click="handleSort(column.key)"
        >
          <div class="flex items-center">
            {{ column.label }}
            <SortIcon
              v-if="currentSortColumn === column.key"
              :order="sortOrderMap[column.key]"
            />
          </div>
        </div>
      </div>
      <div
        v-if="sortedProducts.length === 0"
        class="text-center p-4 text-gray-500"
      >
        Brak danych
      </div>
      <div
        v-for="(product, index) in sortedProducts"
        :key="index"
        class="bg-white border-b p-4 flex justify-between items-center"
      >
        <div class="flex flex-col">
          <span class="font-medium text-gray-800">{{ product.name }}</span>
          <span class="text-gray-600">Ilość: {{ product.quantity }}</span>
        </div>
        <SecondaryButton @click="$emit('edit-product', product, index)">
          <EditIcon class="w-5 h-5" />
        </SecondaryButton>
      </div>
      <div class="bg-gray-100 p-4 flex justify-between font-bold text-gray-700">
        <span>Suma</span>
        <span>{{ totalQuantity }}</span>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'
import { EditIcon } from 'lucide-vue-next'
import SecondaryButton from './buttons/SecondaryButton.vue'
import SortIcon from './icons/SortIcon.vue'

const props = defineProps({
  products: {
    type: Array,
    default: () => []
  }
})

const emit = defineEmits(['edit-product'])

const columns = [
  { key: 'name', label: 'Nazwa produktu' },
  { key: 'quantity', label: 'Ilość' }
]
const currentSortColumn = ref(null)
const sortOrderMap = ref({
  name: null,
  quantity: null
})

const handleSort = (columnKey) => {
  if (currentSortColumn.value === columnKey) {
    if (sortOrderMap.value[columnKey] === null) {
      sortOrderMap.value[columnKey] = 'asc'
    } else if (sortOrderMap.value[columnKey] === 'asc') {
      sortOrderMap.value[columnKey] = 'desc'
    } else {
      sortOrderMap.value[columnKey] = null
    }
  } else {
    Object.keys(sortOrderMap.value).forEach(key => {
      if (key !== columnKey) {
        sortOrderMap.value[key] = null
      }
    })
    currentSortColumn.value = columnKey
    sortOrderMap.value[columnKey] = 'asc'
  }
}

const sortedProducts = computed(() => {
  let result = [...props.products]
  // Sortowanie po nazwie
  if (currentSortColumn.value === 'name') {
    if (sortOrderMap.value.name === 'asc') {
      result.sort((a, b) => a.name.localeCompare(b.name))
    } else if (sortOrderMap.value.name === 'desc') {
      result.sort((a, b) => b.name.localeCompare(a.name))
    }
  }
  // Sortowanie po ilości
  if (currentSortColumn.value === 'quantity') {
    if (sortOrderMap.value.quantity === 'asc') {
      result.sort((a, b) => a.quantity - b.quantity)
    } else if (sortOrderMap.value.quantity === 'desc') {
      result.sort((a, b) => b.quantity - a.quantity)
    }
  }
  // Domyślne sortowanie po dacie dodania
  if (currentSortColumn.value === null) {
    result = [...props.products]
  }
  return result
})

const totalQuantity = computed(() =>
  props.products.reduce((sum, product) => sum + product.quantity, 0)
)
</script>