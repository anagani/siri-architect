<script setup lang="ts">
import { ref } from 'vue'
import AuroraBackground from './components/AuroraBackground.vue'
import InteractiveHoverButton from './components/InteractiveHoverButton.vue'
import FluidCursor from './components/FluidCursor.vue'
import DirectionAwareHover from './components/DirectionAwareHover.vue'
import SparklesText from './components/SparklesText.vue'

const portfolioRef = ref<HTMLElement | null>(null)

const projects = [
  {
    title: 'Modern Villa',
    description: 'Contemporary residential design',
    image: 'https://images.unsplash.com/photo-1600596542815-ffad4c1539a9?q=80&w=2675&auto=format&fit=crop'
  },
  {
    title: 'Urban Office',
    description: 'Commercial space redesign',
    image: 'https://images.unsplash.com/photo-1486406146926-c627a92ad1ab?q=80&w=2670&auto=format&fit=crop'
  },
  {
    title: 'Eco House',
    description: 'Sustainable living concept',
    image: 'https://images.unsplash.com/photo-1518780664697-55e3ad937233?q=80&w=2665&auto=format&fit=crop'
  },
  {
    title: 'Cultural Center',
    description: 'Public space architecture',
    image: 'https://images.unsplash.com/photo-1545558014-8692077e9b5c?q=80&w=2670&auto=format&fit=crop'
  }
]

function scrollToPortfolio() {
  portfolioRef.value?.scrollIntoView({ behavior: 'smooth' })
}
</script>

<template>
  <FluidCursor />
  <AuroraBackground class="min-h-screen">
    <div class="z-10 flex flex-col items-center gap-4">
      <SparklesText 
        text="Sireesha Ettay" 
        class="text-4xl md:text-6xl text-center"
        :sparkles-count="12"
      />
      <p class="text-lg text-muted-foreground">
        Welcome to my architecture portfolio.
      </p>
      <InteractiveHoverButton text="Explore My Work" @click="scrollToPortfolio" />
    </div>
  </AuroraBackground>

  <!-- Portfolio Section -->
  <section ref="portfolioRef" class="min-h-screen bg-zinc-900 py-20 px-8">
    <div class="max-w-7xl mx-auto">
      <h2 class="text-3xl md:text-5xl font-bold text-white text-center mb-16">
        Featured Projects
      </h2>
      <div class="grid grid-cols-1 md:grid-cols-2 gap-8 place-items-center">
        <DirectionAwareHover
          v-for="(project, index) in projects"
          :key="index"
          :image-url="project.image"
          class="w-full max-w-md h-80 md:h-96"
        >
          <div class="space-y-2">
            <h3 class="text-xl md:text-2xl font-bold">{{ project.title }}</h3>
            <p class="text-sm md:text-base text-gray-200">{{ project.description }}</p>
          </div>
        </DirectionAwareHover>
      </div>
    </div>
  </section>
</template>
