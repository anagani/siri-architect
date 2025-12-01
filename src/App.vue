<script setup lang="ts">
import { ref, nextTick, onMounted, onUnmounted } from 'vue'
import LampEffect from './components/LampEffect.vue'
import InteractiveHoverButton from './components/InteractiveHoverButton.vue'
import SleekLineCursor from './components/SleekLineCursor.vue'
import DirectionAwareHover from './components/DirectionAwareHover.vue'
import BentoGrid from './components/BentoGrid.vue'
import BentoGridItem from './components/BentoGridItem.vue'
import GlowBorder from './components/GlowBorder.vue'
import MorphingTabs from './components/MorphingTabs.vue'

const portfolioRef = ref<HTMLElement | null>(null)
const aboutRef = ref<HTMLElement | null>(null)
const contactRef = ref<HTMLElement | null>(null)
const homeRef = ref<HTMLElement | null>(null)
const expandedProject = ref<number | null>(null)
const bentoRefs = ref<Record<number, HTMLElement | null>>({})
const expandedImage = ref<{ src: string; title: string } | null>(null)
const activeTab = ref('Home')
const navTabs = ['Home', 'Projects', 'About', 'Contact']

// Scroll detection for active tab
function updateActiveTabOnScroll() {
  const scrollPosition = window.scrollY + window.innerHeight / 3
  
  const sections = [
    { ref: contactRef.value, name: 'Contact' },
    { ref: aboutRef.value, name: 'About' },
    { ref: portfolioRef.value, name: 'Projects' },
    { ref: homeRef.value, name: 'Home' },
  ]
  
  for (const section of sections) {
    if (section.ref && section.ref.offsetTop <= scrollPosition) {
      if (activeTab.value !== section.name) {
        activeTab.value = section.name
      }
      break
    }
  }
}

onMounted(() => {
  window.addEventListener('scroll', updateActiveTabOnScroll, { passive: true })
  updateActiveTabOnScroll() // Set initial state
})

onUnmounted(() => {
  window.removeEventListener('scroll', updateActiveTabOnScroll)
})

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

function handleNavClick(tab: string) {
  activeTab.value = tab
  if (tab === 'Home') {
    window.scrollTo({ top: 0, behavior: 'smooth' })
  } else if (tab === 'Projects') {
    portfolioRef.value?.scrollIntoView({ behavior: 'smooth' })
  } else if (tab === 'About') {
    aboutRef.value?.scrollIntoView({ behavior: 'smooth' })
  } else if (tab === 'Contact') {
    contactRef.value?.scrollIntoView({ behavior: 'smooth' })
  }
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
  <SleekLineCursor />
  
  <!-- Fixed Navigation -->
  <nav class="fixed top-6 right-6 z-50">
    <MorphingTabs 
      :tabs="navTabs" 
      :active-tab="activeTab" 
      :margin="12"
      :blur-std-deviation="5"
      @update:active-tab="handleNavClick"
    />
  </nav>

  <div ref="homeRef">
    <LampEffect :delay="0.3" :duration="0.8">
      <div class="flex flex-col items-center gap-6">
        <h1 class="text-4xl md:text-7xl text-center font-bold bg-gradient-to-b from-cyan-300 via-cyan-400 to-cyan-600 bg-clip-text text-transparent leading-relaxed pb-2">
          Sireesha Ettay
        </h1>
        <p class="text-lg md:text-xl text-cyan-200/80 text-center max-w-md">
          Welcome to my architecture portfolio.
        </p>
        <InteractiveHoverButton text="Explore My Work" @click="scrollToPortfolio" />
      </div>
    </LampEffect>
  </div>

  <!-- Portfolio Section -->
  <section ref="portfolioRef" class="min-h-screen bg-slate-950 py-20 px-8">
    <div class="max-w-7xl mx-auto">
      <h2 class="text-3xl md:text-5xl font-bold text-cyan-300 text-center mb-16">
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

  <!-- About Section -->
  <section ref="aboutRef" class="min-h-screen bg-slate-900 py-20 px-8">
    <div class="max-w-4xl mx-auto">
      <h2 class="text-3xl md:text-5xl font-bold text-cyan-300 text-center mb-12">
        About Me
      </h2>
      <div class="grid md:grid-cols-2 gap-12 items-center">
        <div class="space-y-6">
          <p class="text-lg text-gray-300 leading-relaxed">
            I'm Sireesha Ettay, a passionate architect with over a decade of experience 
            designing spaces that inspire and transform communities.
          </p>
          <p class="text-lg text-gray-300 leading-relaxed">
            My philosophy centers on sustainable design, blending modern aesthetics 
            with environmental responsibility. Every project is an opportunity to 
            create something meaningful that stands the test of time.
          </p>
          <p class="text-lg text-gray-300 leading-relaxed">
            From residential villas to cultural centers, I believe architecture 
            should tell a story and evoke emotion while serving its practical purpose.
          </p>
        </div>
        <div class="bg-slate-800/50 rounded-2xl p-8 border border-cyan-500/20">
          <h3 class="text-xl font-semibold text-cyan-400 mb-6">Expertise</h3>
          <ul class="space-y-4">
            <li class="flex items-center gap-3 text-gray-300">
              <span class="w-2 h-2 bg-cyan-400 rounded-full"></span>
              Sustainable Architecture
            </li>
            <li class="flex items-center gap-3 text-gray-300">
              <span class="w-2 h-2 bg-cyan-400 rounded-full"></span>
              Residential Design
            </li>
            <li class="flex items-center gap-3 text-gray-300">
              <span class="w-2 h-2 bg-cyan-400 rounded-full"></span>
              Commercial Spaces
            </li>
            <li class="flex items-center gap-3 text-gray-300">
              <span class="w-2 h-2 bg-cyan-400 rounded-full"></span>
              Urban Planning
            </li>
            <li class="flex items-center gap-3 text-gray-300">
              <span class="w-2 h-2 bg-cyan-400 rounded-full"></span>
              Interior Architecture
            </li>
          </ul>
        </div>
      </div>
    </div>
  </section>

  <!-- Contact Section -->
  <section ref="contactRef" class="min-h-screen bg-slate-950 py-20 px-8 flex items-center">
    <div class="max-w-4xl mx-auto text-center">
      <h2 class="text-3xl md:text-5xl font-bold text-cyan-300 mb-8">
        Get In Touch
      </h2>
      <p class="text-lg text-gray-300 mb-12 max-w-2xl mx-auto">
        Have a project in mind? I'd love to hear about it. Let's create 
        something extraordinary together.
      </p>
      <div class="flex flex-col md:flex-row gap-6 justify-center items-center">
        <a 
          href="mailto:sireesha@example.com" 
          class="flex items-center gap-3 px-8 py-4 bg-cyan-500/10 border border-cyan-500/40 rounded-full text-cyan-300 hover:bg-cyan-500/20 hover:border-cyan-400 transition-all"
        >
          <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <rect width="20" height="16" x="2" y="4" rx="2"/><path d="m22 7-8.97 5.7a1.94 1.94 0 0 1-2.06 0L2 7"/>
          </svg>
          sireesha@example.com
        </a>
        <a 
          href="tel:+1234567890" 
          class="flex items-center gap-3 px-8 py-4 bg-cyan-500/10 border border-cyan-500/40 rounded-full text-cyan-300 hover:bg-cyan-500/20 hover:border-cyan-400 transition-all"
        >
          <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <path d="M22 16.92v3a2 2 0 0 1-2.18 2 19.79 19.79 0 0 1-8.63-3.07 19.5 19.5 0 0 1-6-6 19.79 19.79 0 0 1-3.07-8.67A2 2 0 0 1 4.11 2h3a2 2 0 0 1 2 1.72 12.84 12.84 0 0 0 .7 2.81 2 2 0 0 1-.45 2.11L8.09 9.91a16 16 0 0 0 6 6l1.27-1.27a2 2 0 0 1 2.11-.45 12.84 12.84 0 0 0 2.81.7A2 2 0 0 1 22 16.92z"/>
          </svg>
          +1 (234) 567-890
        </a>
      </div>
      <div class="mt-16 pt-8 border-t border-slate-800">
        <p class="text-gray-500 text-sm">
          © 2024 Sireesha Ettay. All rights reserved.
        </p>
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
          class="relative max-w-5xl w-full max-h-[90vh] p-1"
          @click.stop
        >
          <!-- Outer glow container -->
          <div class="relative rounded-2xl overflow-visible">
            <!-- Glow Border Effect -->
            <GlowBorder 
              :color="['#A855F7', '#EC4899', '#3B82F6', '#10B981']"
              :border-width="4"
              :border-radius="16"
              :duration="2"
            />
            
            <!-- Outer glow shadow -->
            <div class="absolute -inset-1 bg-gradient-to-r from-purple-500 via-pink-500 to-blue-500 rounded-2xl blur-lg opacity-50 animate-pulse" />
            
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
