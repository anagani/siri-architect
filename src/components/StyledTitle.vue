<script setup lang="ts">
defineProps<{
  title: string
}>()
</script>

<template>
  <h1 class="text-4xl md:text-7xl text-center font-bold bg-gradient-to-b from-white via-gray-200 to-gray-400 bg-clip-text text-transparent leading-relaxed pb-2" style="font-family: 'Great Vibes', cursive; font-weight: 400">
    {{ title }}
  </h1>
</template>
