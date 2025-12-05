<script setup lang="ts">
import { ref, nextTick, onMounted, onUnmounted } from 'vue'
import Tetris from './components/Tetris.vue'
import InteractiveHoverButton from './components/InteractiveHoverButton.vue'
import SleekLineCursor from './components/SleekLineCursor.vue'
import DirectionAwareHover from './components/DirectionAwareHover.vue'
import BentoGrid from './components/BentoGrid.vue'
import BentoGridItem from './components/BentoGridItem.vue'
import GlowBorder from './components/GlowBorder.vue'
import MorphingTabs from './components/MorphingTabs.vue'
import StyledTitle from './components/StyledTitle.vue'
import GradientButton from './components/GradientButton.vue'

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
    title: 'Mather Square',
    description: 'Modern commercial complex with innovative facade design',
    image: './images/mather%20square/mathers%20square.png',
    details: {
      location: 'Hyderabad, India',
      year: '2025',
      area: '75,000 sq ft',
      features: [
        { title: 'Exterior View', description: 'Contemporary architectural facade with dynamic lighting', image: './images/mather%20square/mathers%20square.png' },
        { title: 'Night Render', description: 'Stunning evening visualization showcasing ambient lighting', image: './images/mather%20square/Enscape_2025-12-01-23-22-07.png' },
      ]
    }
  },
  {
    title: 'Modern Residence',
    description: 'Elegant contemporary home with open living spaces',
    image: './images/residence-1/rahul_view_1.effectsResult.jpg',
    details: {
      location: 'Bangalore, India',
      year: '2024',
      area: '4,200 sq ft',
      features: [
        { title: 'Front Elevation', description: 'Modern facade with clean geometric lines', image: './images/residence-1/rahul_view_1.effectsResult.jpg' },
        { title: 'Garden View', description: 'Seamless indoor-outdoor living connection', image: './images/residence-1/rahul_view_3.effectsResult.jpg' },
        { title: 'Side Perspective', description: 'Architectural details and material palette', image: './images/residence-1/rahul_view_4.effectsResult.jpg' },
        { title: 'Entrance', description: 'Welcoming entryway with landscape integration', image: './images/residence-1/rahul_view_5.effectsResult.jpg' },
        { title: 'Rear View', description: 'Private outdoor spaces and terrace design', image: './images/residence-1/rahul_view_16.effectsResult.jpg' },
        { title: 'Panoramic View', description: 'Full property perspective showcasing scale', image: './images/residence-1/rahul_view_17.effectsResult.jpg' },
      ]
    }
  },
  {
    title: 'Luxury Villa Interior',
    description: 'Sophisticated interior design with premium finishes',
    image: './images/residence-2/LIVING%202B.jpg',
    details: {
      location: 'Mumbai, India',
      year: '2024',
      area: '5,500 sq ft',
      features: [
        { title: 'Living Room', description: 'Spacious living area with elegant furnishings', image: './images/residence-2/LIVING%202B.jpg' },
        { title: 'Master Bedroom', description: 'Luxurious master suite with custom millwork', image: './images/residence-2/BEDROOM%201.jpg' },
        { title: 'Bedroom Alcove', description: 'Intimate seating area with ambient lighting', image: './images/residence-2/BEDROOM%201B.jpg' },
        { title: 'Guest Bedroom', description: 'Comfortable guest quarters with modern aesthetics', image: './images/residence-2/BEDROOM%202.jpg' },
        { title: 'Gourmet Kitchen', description: 'State-of-the-art kitchen with premium appliances', image: './images/residence-2/KITCHEN%201.jpg' },
        { title: 'Kitchen Island', description: 'Central island with breakfast bar seating', image: './images/residence-2/KITCHEN%202.jpg' },
        { title: 'Kitchen Details', description: 'Custom cabinetry and stone countertops', image: './images/residence-2/KITCHEN%203.jpg' },
        { title: 'Foyer', description: 'Grand entrance with statement lighting', image: './images/residence-2/foyr%20c.jpg' },
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
  
  <!-- Fixed Tetris Background for entire page -->
  <div class="fixed inset-0 z-0">
    <Tetris
      class="h-full w-full"
      :base="40"
      square-color="#1a1a1a"
    />
  </div>
  
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

  <!-- Home Section -->
  <div ref="homeRef" class="relative z-10 min-h-screen flex flex-col items-center justify-center bg-slate-950/80">
    <div class="flex flex-col items-center gap-6 px-4">
      <StyledTitle title="Sireesha Ettay" />
      <InteractiveHoverButton text="Explore my portfolio" @click="scrollToPortfolio" />
    </div>
  </div>

  <!-- Portfolio Section -->
  <section ref="portfolioRef" class="relative z-10 min-h-screen bg-slate-950/90 py-20 px-8">
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

  <!-- About Section -->
  <section ref="aboutRef" class="relative z-10 min-h-screen bg-slate-900/90 py-20 px-8">
    <div class="max-w-4xl mx-auto">
      <h2 class="text-3xl md:text-5xl font-bold text-white text-center mb-12">
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
        <div class="bg-slate-800/50 rounded-2xl p-8 border border-white/20">
          <h3 class="text-xl font-semibold text-white mb-6">Expertise</h3>
          <ul class="space-y-4">
            <li class="flex items-center gap-3 text-gray-300">
              <span class="w-2 h-2 bg-white rounded-full"></span>
              Sustainable Architecture
            </li>
            <li class="flex items-center gap-3 text-gray-300">
              <span class="w-2 h-2 bg-white rounded-full"></span>
              Residential Design
            </li>
            <li class="flex items-center gap-3 text-gray-300">
              <span class="w-2 h-2 bg-white rounded-full"></span>
              Commercial Spaces
            </li>
            <li class="flex items-center gap-3 text-gray-300">
              <span class="w-2 h-2 bg-white rounded-full"></span>
              Urban Planning
            </li>
            <li class="flex items-center gap-3 text-gray-300">
              <span class="w-2 h-2 bg-white rounded-full"></span>
              Interior Architecture
            </li>
          </ul>
        </div>
      </div>
    </div>
  </section>

  <!-- Contact Section -->
  <section ref="contactRef" class="relative z-10 min-h-screen bg-slate-950/90 py-20 px-8 flex items-center">
    <div class="max-w-4xl mx-auto text-center">
      <h2 class="text-3xl md:text-5xl font-bold text-white mb-8">
        Get In Touch
      </h2>
      <p class="text-lg text-gray-300 mb-12 max-w-2xl mx-auto">
        Have a project in mind? I'd love to hear about it. Let's create 
        something extraordinary together.
      </p>
      <div class="flex justify-center items-center">
        <GradientButton
          :colors="['#ff0080', '#ff8c00', '#40e0d0', '#0080ff', '#ff00ff', '#ff0080']"
          :duration="3000"
          :border-radius="24"
          :border-width="2"
          :blur="6"
          bg-color="#ffffff"
        >
          <a href="mailto:sireesha.ettay@gmail.com" class="flex items-center gap-3 text-black no-underline">
            <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
              <rect width="20" height="16" x="2" y="4" rx="2"/><path d="m22 7-8.97 5.7a1.94 1.94 0 0 1-2.06 0L2 7"/>
            </svg>
            sireesha.ettay@gmail.com
          </a>
        </GradientButton>
      </div>
      <div class="mt-16 pt-8 border-t border-slate-800">
        <p class="text-gray-500 text-sm">
          © 2025 Sireesha Ettay. All rights reserved.
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
            <div class="absolute -inset-1 bg-gradient-to-r from-gray-400 via-gray-300 to-gray-400 rounded-2xl blur-lg opacity-50 animate-pulse" />
            
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
