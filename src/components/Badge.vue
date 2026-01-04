<script setup>
const props = defineProps({
  color: {
    type: String,
    default: 'secondary',
  },
  outline: {
    type: Boolean,
    default: false,
  },
})
</script>

<template>
  <div
    class="badge badge-secondary"
    :class="[`text-${props.color}`, { 'badge-outline': props.outline }]"
  >
    <slot></slot>
  </div>
</template>
