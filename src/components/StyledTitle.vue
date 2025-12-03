<script setup lang="ts">
defineProps<{
  title: string
}>()
</script>

<template>
  <h1 class="text-4xl md:text-7xl text-center font-bold bg-gradient-to-b from-cyan-300 via-cyan-400 to-cyan-600 bg-clip-text text-transparent leading-relaxed pb-2" style="font-family: 'Great Vibes', cursive; font-weight: 400">
    {{ title }}
  </h1>
</template>
