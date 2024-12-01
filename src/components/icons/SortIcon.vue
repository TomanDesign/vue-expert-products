<template>
    <div class="ml-2">
      <ChevronUpIcon v-if="order === 'asc'" class="w-4 h-4" />
      <ChevronDownIcon v-else-if="order === 'desc'" class="w-4 h-4" />
    </div>
  </template>

  <script setup>
  import { ChevronUpIcon, ChevronDownIcon } from 'lucide-vue-next'

  defineProps({
    order: {
      type: String,
      default: ''
    }
  })
  </script>