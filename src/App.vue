<template>
  <div class="min-h-screen bg-gray-100 flex flex-col w-full p-5">
    <Header />
    <div class="flex-row flex flex-wrap justify-center py-8">
      <div class="w-full bg-white rounded-xl border border-gray-300/80 p-6 mb-2">
        <ProductForm @product-added="addProduct" />
      </div>
      <div class="w-full bg-white rounded-xl border border-gray-300/80 p-6">
        <ProductTable
          :products="products"
          @edit-product="openEditModal"
        />
      </div>
    </div>
    <ProductModal
      v-if="editModalOpen"
      :product="editedProduct"
      @close="closeEditModal"
      @save="applyChanges"
      @remove="removeProduct"
    />
  </div>
</template>

<script setup>
import { ref } from 'vue'
import Header from './components/Header.vue'
import ProductForm from './components/ProductForm.vue'
import ProductTable from './components/ProductTable.vue'
import ProductModal from './components/ProductModal.vue'

const products = ref([])
const editModalOpen = ref(false)
const editedProduct = ref(null)

const addProduct = (product) => {
  products.value.push({
    ...product,
    addedAt: new Date()
  })
}

const openEditModal = (product, index) => {
  editedProduct.value = {
    ...product,
    originalIndex: index
  }
  editModalOpen.value = true
}

const closeEditModal = () => {
  editModalOpen.value = false
  editedProduct.value = null
}

const applyChanges = (updatedProduct) => {
  if (editedProduct.value) {
    products.value[editedProduct.value.originalIndex] = updatedProduct
    closeEditModal()
  }
}

const removeProduct = () => {
  if (editedProduct.value) {
    products.value.splice(editedProduct.value.originalIndex, 1)
    closeEditModal()
  }
}
</script>