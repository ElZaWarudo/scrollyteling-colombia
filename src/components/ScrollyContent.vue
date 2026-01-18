<template>
  <div class="bg-white/95 backdrop-blur-sm shadow-xl rounded-2xl p-6 border border-gray-100 transition-all duration-500 hover:shadow-2xl max-h-[90vh] overflow-y-auto custom-scrollbar">
    <div :class="['flex', 'flex-col', 'gap-4', 'h-full', invert ? 'flex-col-reverse' : '']">
      <h2 v-if="title" class="text-2xl font-extrabold text-gray-900 tracking-tight">
        {{ title }}
      </h2>
      
      <p class="text-base leading-relaxed text-gray-600 font-medium text-pretty">
        {{ text }}
      </p>
      
      <div v-if="chart" class="mt-auto pt-4 border-t border-gray-100">
        <component :is="chart" v-bind="chartProps" />
      </div>
    </div>
  </div>
</template>

<script setup>
defineProps({
  title: {
    type: String,
    default: null
  },
  text: {
    type: String,
    required: true
  },
  chart: {
    type: Object,
    default: null
  },
  chartProps: {
    type: Object,
    default: () => ({})
  },
  invert: {
    type: Boolean,
    default: false
  }
});
</script>

<style scoped>
</style>
