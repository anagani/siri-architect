<script setup lang="ts">
defineProps<{
  title: string
}>()
</script>

<template>
  <h1 class="text-4xl md:text-7xl text-center font-bold text-black leading-relaxed pb-2" style="font-family: 'Great Vibes', cursive; font-weight: 400; text-shadow: 0 4px 20px rgba(255, 255, 255, 0.8), 0 0 40px rgba(255, 255, 255, 0.6)">
    {{ title }}
  </h1>
</template>
