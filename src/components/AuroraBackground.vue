<template>
  <main>
    <div
      v-bind="props"
      :class="
        cn(
          'transition-bg relative flex h-[100vh] flex-col items-center justify-center bg-zinc-900 text-slate-50',
          props.class,
        )
      "
    >
      <div
        :style="styles"
        class="absolute inset-0 overflow-hidden"
      >
        <div
          :class="
            cn(
              `after:animate-aurora pointer-events-none absolute -inset-[10px] [background-image:var(--dark-gradient),var(--aurora)] [background-size:300%,_200%] [background-position:50%_50%,50%_50%] opacity-60 blur-[10px] filter will-change-transform [--aurora:repeating-linear-gradient(100deg,var(--blue-500)_10%,var(--indigo-300)_15%,var(--blue-300)_20%,var(--violet-200)_25%,var(--blue-400)_30%)] [--dark-gradient:repeating-linear-gradient(100deg,var(--black)_0%,var(--black)_7%,var(--transparent)_10%,var(--transparent)_12%,var(--black)_16%)] after:absolute after:inset-0 after:[background-image:var(--dark-gradient),var(--aurora)] after:[background-size:200%,_100%] after:[background-attachment:fixed] after:mix-blend-difference after:content-['']`,
              props.radialGradient &&
                `[mask-image:radial-gradient(ellipse_at_100%_0%,black_10%,var(--transparent)_70%)]`,
            )
          "
        />
      </div>
      <slot />
    </div>
  </main>
</template>

<script setup lang="ts">
import { cn } from "@/lib/utils";
import { computed } from "vue";

interface AuroraBackgroundProps {
  radialGradient?: boolean;
  class?: string;
}

const props = withDefaults(defineProps<AuroraBackgroundProps>(), {
  radialGradient: true,
});

const styles = computed(() => {
  return {
    "--aurora":
      "repeating-linear-gradient(100deg,#1e3a8a_10%,#4338ca_15%,#1e40af_20%,#5b21b6_25%,#1d4ed8_30%)",
    "--dark-gradient":
      "repeating-linear-gradient(100deg,#000_0%,#000_7%,transparent_10%,transparent_12%,#000_16%)",
    "--white-gradient":
      "repeating-linear-gradient(100deg,#fff_0%,#fff_7%,transparent_10%,transparent_12%,#fff_16%)",

    "--blue-300": "#1e40af",
    "--blue-400": "#1d4ed8",
    "--blue-500": "#1e3a8a",
    "--indigo-300": "#4338ca",
    "--violet-200": "#5b21b6",
    "--black": "#000",
    "--white": "#fff",
    "--transparent": "transparent",
    "--animate-aurora": "aurora 60s linear infinite",
  };
});
</script>
