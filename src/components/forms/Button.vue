<template>
  <button :type="type"
          :disabled="disabled"
          class="block rounded-md px-4 py-2 border border-gray-300 shadow-sm
          disabled:opacity-50
          focus:border-indigo-300 focus:ring focus:ring-indigo-200 focus:ring-opacity-50"
  >
    <slot></slot>
  </button>
</template>

<script setup>
defineProps({
  type: {
    type: String,
    default: 'button'
  },
  disabled: {
    type: Boolean,
    default: false
  }
})
</script>