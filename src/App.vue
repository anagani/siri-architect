<script setup lang="ts">
import { ref, nextTick } from 'vue'
import AuroraBackground from './components/AuroraBackground.vue'
import InteractiveHoverButton from './components/InteractiveHoverButton.vue'
import FluidCursor from './components/FluidCursor.vue'
import DirectionAwareHover from './components/DirectionAwareHover.vue'
import SparklesText from './components/SparklesText.vue'
import BentoGrid from './components/BentoGrid.vue'
import BentoGridItem from './components/BentoGridItem.vue'
import GlowBorder from './components/GlowBorder.vue'

const portfolioRef = ref<HTMLElement | null>(null)
const expandedProject = ref<number | null>(null)
const bentoRefs = ref<Record<number, HTMLElement | null>>({})
const expandedImage = ref<{ src: string; title: string } | null>(null)

const projects = [
  {
    title: 'Modern Villa',
    description: 'Contemporary residential design',
    image: 'https://images.unsplash.com/photo-1600596542815-ffad4c1539a9?q=80&w=2675&auto=format&fit=crop',
    details: {
      location: 'Beverly Hills, CA',
      year: '2024',
      area: '8,500 sq ft',
      features: [
        { title: 'Open Floor Plan', description: 'Seamless indoor-outdoor living spaces', image: 'https://images.unsplash.com/photo-1600607687939-ce8a6c25118c?q=80&w=800&auto=format&fit=crop' },
        { title: 'Infinity Pool', description: 'Stunning hillside views', image: 'https://images.unsplash.com/photo-1600566753190-17f0baa2a6c3?q=80&w=800&auto=format&fit=crop' },
        { title: 'Smart Home', description: 'Fully automated systems', image: 'https://images.unsplash.com/photo-1558618666-fcd25c85cd64?q=80&w=800&auto=format&fit=crop' },
        { title: 'Green Roof', description: 'Sustainable garden terrace', image: 'https://images.unsplash.com/photo-1600585154340-be6161a56a0c?q=80&w=800&auto=format&fit=crop' },
      ]
    }
  },
  {
    title: 'Urban Office',
    description: 'Commercial space redesign',
    image: 'https://images.unsplash.com/photo-1486406146926-c627a92ad1ab?q=80&w=2670&auto=format&fit=crop',
    details: {
      location: 'Manhattan, NY',
      year: '2023',
      area: '45,000 sq ft',
      features: [
        { title: 'Collaborative Spaces', description: 'Modern open workspaces', image: 'https://images.unsplash.com/photo-1497366216548-37526070297c?q=80&w=800&auto=format&fit=crop' },
        { title: 'Sky Lounge', description: 'Rooftop relaxation area', image: 'https://images.unsplash.com/photo-1497366811353-6870744d04b2?q=80&w=800&auto=format&fit=crop' },
        { title: 'Glass Facade', description: 'Floor-to-ceiling windows', image: 'https://images.unsplash.com/photo-1545324418-cc1a3fa10c00?q=80&w=800&auto=format&fit=crop' },
        { title: 'LEED Certified', description: 'Platinum sustainability rating', image: 'https://images.unsplash.com/photo-1497215842964-222b430dc094?q=80&w=800&auto=format&fit=crop' },
      ]
    }
  },
  {
    title: 'Eco House',
    description: 'Sustainable living concept',
    image: 'https://images.unsplash.com/photo-1518780664697-55e3ad937233?q=80&w=2665&auto=format&fit=crop',
    details: {
      location: 'Portland, OR',
      year: '2024',
      area: '3,200 sq ft',
      features: [
        { title: 'Solar Panels', description: 'Net-zero energy design', image: 'https://images.unsplash.com/photo-1509391366360-2e959784a276?q=80&w=800&auto=format&fit=crop' },
        { title: 'Rainwater System', description: 'Sustainable water management', image: 'https://images.unsplash.com/photo-1416879595882-3373a0480b5b?q=80&w=800&auto=format&fit=crop' },
        { title: 'Natural Materials', description: 'Reclaimed wood & stone', image: 'https://images.unsplash.com/photo-1600585154526-990dced4db0d?q=80&w=800&auto=format&fit=crop' },
        { title: 'Living Walls', description: 'Indoor vertical gardens', image: 'https://images.unsplash.com/photo-1524758631624-e2822e304c36?q=80&w=800&auto=format&fit=crop' },
      ]
    }
  },
  {
    title: 'Cultural Center',
    description: 'Public space architecture',
    image: 'https://images.unsplash.com/photo-1545558014-8692077e9b5c?q=80&w=2670&auto=format&fit=crop',
    details: {
      location: 'Chicago, IL',
      year: '2023',
      area: '120,000 sq ft',
      features: [
        { title: 'Grand Atrium', description: 'Six-story central space', image: 'https://images.unsplash.com/photo-1507003211169-0a1dd7228f2d?q=80&w=800&auto=format&fit=crop' },
        { title: 'Exhibition Halls', description: 'Flexible gallery spaces', image: 'https://images.unsplash.com/photo-1518998053901-5348d3961a04?q=80&w=800&auto=format&fit=crop' },
        { title: 'Amphitheater', description: 'Outdoor performance venue', image: 'https://images.unsplash.com/photo-1514320291840-2e0a9bf2a9ae?q=80&w=800&auto=format&fit=crop' },
        { title: 'Sculpture Garden', description: 'Curated outdoor art space', image: 'https://images.unsplash.com/photo-1551913902-c92207136625?q=80&w=800&auto=format&fit=crop' },
      ]
    }
  }
]

function scrollToPortfolio() {
  portfolioRef.value?.scrollIntoView({ behavior: 'smooth' })
}

function toggleProject(index: number) {
  if (expandedProject.value === index) {
    expandedProject.value = null
  } else {
    expandedProject.value = index
    // Scroll to the bento grid after it renders
    nextTick(() => {
      setTimeout(() => {
        bentoRefs.value[index]?.scrollIntoView({ behavior: 'smooth', block: 'center' })
      }, 100)
    })
  }
}

function openImage(src: string, title: string) {
  expandedImage.value = { src, title }
}

function closeImage() {
  expandedImage.value = null
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
        <template v-for="(project, index) in projects" :key="index">
          <div class="w-full flex flex-col items-center">
            <DirectionAwareHover
              :image-url="project.image"
              :is-expanded="expandedProject === index"
              class="w-full max-w-md h-80 md:h-96 cursor-pointer"
              :class="{ 'ring-2 ring-white/50': expandedProject === index }"
              @click="toggleProject(index)"
            >
              <div class="space-y-2">
                <h3 class="text-xl md:text-2xl font-bold">{{ project.title }}</h3>
                <p class="text-sm md:text-base text-gray-200">{{ project.description }}</p>
                <p class="text-xs text-gray-400 mt-2">Click to {{ expandedProject === index ? 'collapse' : 'expand' }}</p>
              </div>
            </DirectionAwareHover>
            
            <!-- Expanded Bento Grid -->
            <Transition name="expand">
              <div 
                v-if="expandedProject === index" 
                :ref="el => bentoRefs[index] = el as HTMLElement"
                class="w-full mt-8"
              >
                <div class="bg-zinc-800/50 rounded-2xl p-6 backdrop-blur-sm border border-white/10">
                  <div class="flex justify-between items-center mb-6">
                    <div>
                      <h3 class="text-2xl font-bold text-white">{{ project.title }}</h3>
                      <p class="text-gray-400">{{ project.details.location }} • {{ project.details.year }} • {{ project.details.area }}</p>
                    </div>
                    <button 
                      @click.stop="expandedProject = null"
                      class="text-gray-400 hover:text-white transition-colors p-2"
                    >
                      <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                        <path d="M18 6 6 18"/><path d="m6 6 12 12"/>
                      </svg>
                    </button>
                  </div>
                  
                  <BentoGrid class="md:auto-rows-[16rem]">
                    <BentoGridItem 
                      v-for="(feature, featureIndex) in project.details.features" 
                      :key="featureIndex"
                      :class="featureIndex === 0 ? 'md:col-span-2' : ''"
                      class="bg-zinc-900/80 border-zinc-700/50 hover:border-zinc-600"
                    >
                      <template #header>
                        <div 
                          class="relative h-32 w-full overflow-hidden rounded-lg cursor-pointer"
                          @click="openImage(feature.image, feature.title)"
                        >
                          <img 
                            :src="feature.image" 
                            :alt="feature.title"
                            class="h-full w-full object-cover transition-transform duration-300 group-hover/bento:scale-110"
                          />
                          <div class="absolute inset-0 bg-gradient-to-t from-zinc-900/80 to-transparent" />
                          <div class="absolute inset-0 flex items-center justify-center opacity-0 group-hover/bento:opacity-100 transition-opacity">
                            <div class="bg-black/50 rounded-full p-2">
                              <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="white" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                                <path d="m21 21-6-6m6 6v-4.8m0 4.8h-4.8"/>
                                <path d="M3 16.2V21m0 0h4.8M3 21l6-6"/>
                                <path d="M21 7.8V3m0 0h-4.8M21 3l-6 6"/>
                                <path d="M3 7.8V3m0 0h4.8M3 3l6 6"/>
                              </svg>
                            </div>
                          </div>
                        </div>
                      </template>
                      <template #title>
                        <span class="text-white">{{ feature.title }}</span>
                      </template>
                      <template #description>
                        <span class="text-gray-400">{{ feature.description }}</span>
                      </template>
                    </BentoGridItem>
                  </BentoGrid>
                </div>
              </div>
            </Transition>
          </div>
        </template>
      </div>
    </div>
  </section>

  <!-- Image Lightbox Modal with Glow Border -->
  <Teleport to="body">
    <Transition name="modal">
      <div 
        v-if="expandedImage" 
        class="fixed inset-0 z-[100] flex items-center justify-center p-4 md:p-8"
        @click="closeImage"
      >
        <!-- Backdrop -->
        <div class="absolute inset-0 bg-black/80 backdrop-blur-sm" />
        
        <!-- Modal Content -->
        <div 
          class="relative max-w-5xl w-full max-h-[90vh] rounded-2xl overflow-hidden"
          @click.stop
        >
          <!-- Glow Border Effect -->
          <GlowBorder 
            :color="['#8B5CF6', '#EC4899', '#3B82F6']"
            :border-width="3"
            :border-radius="16"
            :duration="3"
          />
          
          <!-- Image Container -->
          <div class="relative bg-zinc-900 rounded-2xl overflow-hidden">
            <!-- Close Button -->
            <button 
              @click="closeImage"
              class="absolute top-4 right-4 z-10 bg-black/50 hover:bg-black/70 text-white rounded-full p-2 transition-colors"
            >
              <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                <path d="M18 6 6 18"/><path d="m6 6 12 12"/>
              </svg>
            </button>
            
            <!-- Image -->
            <img 
              :src="expandedImage.src" 
              :alt="expandedImage.title"
              class="w-full h-auto max-h-[80vh] object-contain"
            />
            
            <!-- Title Bar -->
            <div class="absolute bottom-0 left-0 right-0 bg-gradient-to-t from-black/80 to-transparent p-6">
              <h3 class="text-xl md:text-2xl font-bold text-white">{{ expandedImage.title }}</h3>
            </div>
          </div>
        </div>
      </div>
    </Transition>
  </Teleport>
</template>

<style>
.expand-enter-active,
.expand-leave-active {
  transition: all 0.4s ease-out;
  overflow: hidden;
}

.expand-enter-from,
.expand-leave-to {
  opacity: 0;
  max-height: 0;
  transform: translateY(-20px);
}

.expand-enter-to,
.expand-leave-from {
  opacity: 1;
  max-height: 1000px;
  transform: translateY(0);
}

/* Modal animations */
.modal-enter-active,
.modal-leave-active {
  transition: all 0.3s ease-out;
}

.modal-enter-from,
.modal-leave-to {
  opacity: 0;
}

.modal-enter-from .relative,
.modal-leave-to .relative {
  transform: scale(0.9);
}

.modal-enter-to .relative,
.modal-leave-from .relative {
  transform: scale(1);
}
</style>
