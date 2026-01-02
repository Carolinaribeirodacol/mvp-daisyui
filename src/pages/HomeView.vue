<script setup>
const text = ref('')
const textWithComputed = computed(() => text.value)
</script>

<template>
  <div class="p-4">
    Home <input v-model="text" type="text" placeholder="Type here" class="input" />
    {{ textWithComputed }}
  </div>
</template>
