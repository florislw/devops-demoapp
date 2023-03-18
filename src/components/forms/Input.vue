<template>
  <div>
    <label v-if="label" class="text-gray-800 mb-1 block">
      {{  label }}
    </label>
    <input
        :value="modelValue"
        @input="$emit('update:modelValue', $event.target.value)"
        :type="type"
        :required="required"
        :min="min"
        :max="max"
        class="block w-full rounded-md border-gray-300 shadow-sm focus:border-indigo-300 focus:ring focus:ring-indigo-200 focus:ring-opacity-50" />
  </div>
</template>

<script setup>
defineProps({
  modelValue: {},
  type: {
    type: String,
    default: 'text'
  },
  label: String,
  required: {
    type: Boolean,
    default: false,
  },
  min: {},
  max: {}
})

defineEmits(['update:modelValue'])
</script>

<style scoped>

</style>