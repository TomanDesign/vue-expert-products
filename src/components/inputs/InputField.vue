<template>
  <input
    :type="type"
    :value="modelValue"
    @input="handleInput"
    :placeholder="placeholder"
    :min="minValue"
    :max="maxValue"
    class="w-full text-gray-800 border-2 border-gray-300 rounded-md px-3 py-2 focus:outline-none focus:ring-2 focus:ring-yellow-500 focus:border-transparent"
  />
</template>

<script setup>
import { defineProps, defineEmits, computed } from 'vue'

const props = defineProps({
  type: {
    type: String,
    default: 'text'
  },
  modelValue: {
    type: [String, Number],
    default: ''
  },
  placeholder: {
    type: String,
    default: ''
  },
  min: {
    type: [String, Number],
    default: 1
  },
  max: {
    type: [String, Number],
    default: 20
  }
})

const emit = defineEmits(['update:modelValue'])
const minValue = computed(() => Number(props.min))
const maxValue = computed(() => Number(props.max))

const handleInput = (event) => {
  const inputValue = event.target.value


  if (props.type === 'number') {

    const numericValue = inputValue.replace(/[^0-9.]/g, '')
    const sanitizedValue = numericValue.replace(/\.(?=.*\.)/g, '')
    const numericInputValue = parseFloat(sanitizedValue)


    if (!isNaN(numericInputValue)) {
      let finalValue = numericInputValue

      if (numericInputValue > maxValue.value) {
        finalValue = maxValue.value
        event.target.value = String(maxValue.value)
      }

      else if (numericInputValue < minValue.value) {
        finalValue = minValue.value
        event.target.value = String(minValue.value)
      }

      emit('update:modelValue', finalValue)
    } else {

      event.target.value = ''
      emit('update:modelValue', '')
    }
  } else {

    emit('update:modelValue', inputValue)
  }
}
</script>

<style scoped>

input[type=number]::-webkit-inner-spin-button,
input[type=number]::-webkit-outer-spin-button {
  -webkit-appearance: none;
  margin: 0;
}
input[type=number] {
  -moz-appearance: textfield;
}
</style>