<template>
  <section class="relative h-screen w-full overflow-hidden">
    <div class="absolute inset-0 w-full h-full">
      <iframe
        class="absolute top-1/2 left-1/2 w-[177.77vh] h-[56.25vw] min-h-full min-w-full -translate-x-1/2 -translate-y-1/2"
        :src="videoUrl"
        frameborder="0"
        allow="autoplay; encrypted-media"
        allowfullscreen
      ></iframe>
      <div class="absolute inset-0 bg-black/40"></div>
    </div>

    <div class="relative z-10 h-full flex items-center justify-center px-8 md:px-16">
      <div class="max-w-5xl text-center">
        <h1 class="text-white mb-8">
          <span class="block text-5xl md:text-7xl font-bold tracking-tight mb-4">
            {{ title }}
          </span>
          <span class="block text-3xl md:text-5xl font-light italic mb-6">
            {{ subtitle }}
          </span>
        </h1>
        <p class="text-xl md:text-2xl text-white/90 font-light leading-relaxed">
          {{ introText }}
          <span 
            class="inline-block px-1 pb-2 mx-1 font-bold text-white leading-none"
            style="background-image: linear-gradient(90deg, #FACC15 50%, #2563EB 50%, #2563EB 75%, #DC2626 75%); background-size: 100% 8px; background-position: bottom; background-repeat: no-repeat;"
          >
            {{ highlightedWord }}
          </span>
          {{ finalText }}
        </p>
      </div>
    </div>

  </section>
</template>

<script setup>
import { computed } from 'vue';

const props = defineProps({
  videoId: {
    type: String,
    required: true
  },
  title: {
    type: String,
    default: 'Main Title'
  },
  subtitle: {
    type: String,
    default: 'Subtitle'
  },
  introText: {
    type: String,
    default: ''
  },
  highlightedWord: {
    type: String,
    default: ''
  },
  finalText: {
    type: String,
    default: ''
  }
});

const videoUrl = computed(() => {
  return `https://www.youtube.com/embed/${props.videoId}?autoplay=1&mute=1&loop=1&controls=0&playlist=${props.videoId}&showinfo=0&rel=0&modestbranding=1`;
});
</script>

<style scoped>
</style>
