<script setup>
const props = defineProps({
  outline: {
    type: Boolean,
    default: false,
  },
})
</script>

<template>
  <div class="badge badge-primary" :class="{ 'badge-outline': outline }">
    <slot></slot>
  </div>
</template>
