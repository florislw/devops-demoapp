<template>
  <div v-if="type === 'checkbox' || type === 'radio'">
    <input
        :checked="modelValue"
        @change="$emit('update:modelValue', $event.target.checked)"
        :type="type"
        :required="required"
        :id="id"
        class="rounded-md align-baseline border-gray-300 shadow-sm focus:border-indigo-300 focus:ring focus:ring-indigo-200 focus:ring-opacity-50"/>
    <label v-if="label" :for="id" class="text-gray-800 inline-block ml-2">
      {{ label }}
    </label>
  </div>
  <div v-else>
    <label v-if="label" :for="id" class="text-gray-800 mb-1 block">
      {{ label }}
    </label>
    <input
        :value="modelValue"
        @input="$emit('update:modelValue', $event.target.value)"
        :type="type"
        :required="required"
        :min="min"
        :max="max"
        :id="id"
        class="block w-full rounded-md border-gray-300 shadow-sm focus:border-indigo-300 focus:ring focus:ring-indigo-200 focus:ring-opacity-50"/>
  </div>
</template>

<script setup>
defineProps( {
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
  max: {},
  id: {}
} )

defineEmits( ['update:modelValue'] )
</script>

<style scoped>

</style>