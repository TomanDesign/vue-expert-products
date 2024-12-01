<template>
    <div>
      <div
        v-for="(notif, index) in notifications"
        :key="index"
        :class="[
          'w-full p-4 rounded-lg mb-4 transition-all duration-300 ease-in-out',
          typeClasses[notif.type]
        ]"
      >
        {{ notif.message }}
      </div>
    </div>
  </template>

  <script setup>
  import { ref, watch } from 'vue'

  const props = defineProps({
    notifications: {
      type: Array,
      default: () => [],
    }
  })

  const typeClasses = {
    info: 'bg-blue-100 text-blue-800',
    success: 'bg-green-100 text-green-800',
    warning: 'bg-yellow-100 text-yellow-800',
    error: 'bg-red-100 text-red-800'
  }
  </script>
