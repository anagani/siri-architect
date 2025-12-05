<script setup lang="ts">
defineProps<{
  title: string
}>()
</script>

<template>
  <h1 class="text-4xl md:text-7xl text-center font-bold text-white leading-relaxed pb-2" style="font-family: 'Great Vibes', cursive; font-weight: 400; text-shadow: 0 4px 20px rgba(0, 0, 0, 0.8), 0 0 40px rgba(0, 0, 0, 0.6)">
    {{ title }}
  </h1>
</template>
