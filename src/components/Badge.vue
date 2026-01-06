<script setup>
const props = defineProps({
  color: {
    type: String,
    default: 'white',
  },
  background: {
    type: String,
    default: 'primary',
    required: false,
  },
  outline: {
    type: Boolean,
    default: false,
  },
})
</script>

<template>
  <div
    class="badge badge-primary"
    :class="[
      `text-${props.color}`,
      { 'badge-outline': props.outline },
      `badge-${props.background}`,
    ]"
  >
    <slot></slot>
  </div>
</template>
