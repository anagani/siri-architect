<script setup lang="ts">
import AuroraBackground from './components/AuroraBackground.vue'
import InteractiveHoverButton from './components/InteractiveHoverButton.vue'
import FluidCursor from './components/FluidCursor.vue'
</script>

<template>
  <FluidCursor />
  <AuroraBackground>
    <div class="z-10 flex flex-col items-center gap-4">
      <h1 class="text-4xl font-bold text-center md:text-6xl">
        Sireesha Ettay.
      </h1>
      <p class="text-lg text-muted-foreground">
        Welcome to my architecture portfolio.
      </p>
      <InteractiveHoverButton text="Explore My Work" />
    </div>
  </AuroraBackground>
</template>
