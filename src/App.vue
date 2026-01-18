<template>
  <HeroSection
    videoId="MCn3Thrpktg"
    title="El Dilema del Suelo Colombiano"
    subtitle="¿Café, Banano, Aguacate o Cacao?"
    introText="En busca del cultivo que"
    highlightedWord="transformará la historia"
    finalText=""
  />

  <div class="relative flex">
    
    <div class="sticky top-0 h-screen w-1/2 flex items-center justify-center bg-gray-50">
       <ScrollyMap :currentStep="currentStep" class="w-full h-full" />
    </div>

    <div class="w-1/2 relative">
      
      <div class="sticky top-0 h-screen flex flex-col justify-center px-12 pointer-events-none">
        
        <div 
          class="transition-opacity duration-700 ease-in-out pointer-events-auto"
          :class="currentStep === 'cafe' ? 'opacity-100' : 'opacity-0 pointer-events-none'"
        >
          <div class="space-y-4">
            <AptitudDonutChart title="Aptitud del suelo para el cultivo de café" />
            <CafePrecioChart title="Evolución del precio del café" />
            
            <ScrollyContent
              title="Café: la Tradición en Riesgo"
              text="Aunque el 58% del territorio mantiene una alta aptitud para su cultivo, el café enfrenta una crisis estructural. La volatilidad extrema de precios —con caídas profundas en 2019 y picos efímeros en 2022— amenaza la estabilidad de miles de familias caficultoras."
            />
          </div>
        </div>

        <div 
          class="absolute top-20 left-12 right-12 transition-opacity duration-700 ease-in-out"
          :class="currentStep === 'aguacate' ? 'opacity-100' : 'opacity-0 pointer-events-none'"
        >
          <ScrollyContent
            title="Aguacate: la Fiebre del Oro Verde"
            text="Observe el ascenso imparable del 'Oro Verde'. Mientras las exportaciones de café fluctúan, el aguacate crece exponencialmente desde 2010. Ya no es una promesa, es una realidad: miles de toneladas desafían anualmente la hegemonía histórica del grano."
          />
          <ExportacionesChart title="Exportaciones de café vs aguacate" />
        </div>

        <div 
          class="absolute top-20 left-12 right-12 transition-opacity duration-700 ease-in-out"
          :class="currentStep === 'banano' ? 'opacity-100' : 'opacity-0 pointer-events-none'"
        >
          <BananaVsAguacateChart title="Exportaciones de banano vs aguacate" />
        
          <ScrollyContent
            title="Banano: El Gigante Estable"
            text="Sin embargo, el verdadero titán permanece. El banano supera ampliamente al aguacate en volumen, consolidándose como una industria madura y resiliente. Aunque el 'Oro Verde' acapare los titulares, el banano sostiene una hegemonía silenciosa pero contundente."
          />
        </div>

        <div 
          class="absolute top-20 left-12 right-12 transition-opacity duration-700 ease-in-out"
          :class="currentStep === 'pdet' ? 'opacity-100' : 'opacity-0 pointer-events-none'"
        >
          <ScrollyContent
            title="Herencia eterna: la Huella del Conflicto"
            text="Pero la rentabilidad es solo una cara de la moneda. Las zonas rojas en el mapa revelan los 170 municipios PDET, priorizados por el Acuerdo de Paz de 2016. Son territorios marcados por el conflicto y el abandono estatal, donde la infraestructura es escasa y la deuda social inmensa. Sin embargo, son la clave para la reconciliación nacional."
          />
          <PdetProporcionChart title="Porcentaje de territorio PDET" />
        </div>

        <div 
          class="absolute top-20 left-12 right-12 transition-opacity duration-700 ease-in-out"
          :class="currentStep === 'cacao' ? 'opacity-100' : 'opacity-0 pointer-events-none'"
        >
          <ScrollyContent
            title="El cacao: una solución ancestral"
            text="Aquí yace una oportunidad oculta. Mientras el aguacate exige infraestructura compleja, el cacao demuestra una resiliencia única. Un dato revelador: el 80% de la tierra apta para cacao coincide con las zonas de conflicto. Cultivarlo no es solo agricultura; es un acto de reconstrucción social."
          />
          <CacaoConflictChart title="Resiliencia en Cifras" />
        </div>

        <div 
          class="absolute top-20 left-12 right-12 transition-opacity duration-700 ease-in-out"
          :class="currentStep === 'final' ? 'opacity-100' : 'opacity-0 pointer-events-none'"
        >
          <ScrollyContent
            title="El mapa de las oportunidades"
            text="Al cruzar la aptitud del suelo con la urgencia de la paz, revelamos la 'Mejor Apuesta' para cada municipio.

El Cacao emerge como motor de cambio en zonas de posconflicto, mientras el Café y el Banano protegen su legado productivo.

Toma el control: explora el territorio y descubre qué cultivo transformará el futuro de cada región."
          />
          <OportunidadesSummary title="Municipios por mejor opción de cultivo" />
        </div>
        
      </div>

      <div ref="coffeeStep" class="h-screen"></div>
      <div ref="avocadoStep" class="h-screen"></div>
      <div ref="bananaStep" class="h-screen"></div>
      <div ref="pdetStep" class="h-screen"></div>
      <div ref="cacaoStep" class="h-screen"></div>
      <div ref="finalStep" class="h-screen"></div>

    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue';
import HeroSection from './components/HeroSection.vue';
import ScrollyMap from './components/ScrollyMap.vue';
import ScrollyContent from './components/ScrollyContent.vue';
import ExportacionesChart from './components/ExportacionesChart.vue';
import BananaVsAguacateChart from './components/BananaVsAguacateChart.vue';
import AptitudDonutChart from './components/AptitudDonutChart.vue';
import CafePrecioChart from './components/CafePrecioChart.vue';
import PdetProporcionChart from './components/PdetProporcionChart.vue';

import CacaoConflictChart from './components/CacaoConflictChart.vue';
import OportunidadesSummary from './components/OportunidadesSummary.vue';

const currentStep = ref('cafe');
const coffeeStep = ref(null);
const avocadoStep = ref(null);
const bananaStep = ref(null);
const pdetStep = ref(null);
const cacaoStep = ref(null);
const finalStep = ref(null);

onMounted(() => {
  const options = {
    root: null,
    rootMargin: '0px',
    threshold: 0.5
  };

  const observer = new IntersectionObserver((entries) => {
    entries.forEach((entry) => {
      if (entry.isIntersecting) {
        if (entry.target === coffeeStep.value) {
          currentStep.value = 'cafe';
        } else if (entry.target === avocadoStep.value) {
          currentStep.value = 'aguacate';
        } else if (entry.target === bananaStep.value) {
          currentStep.value = 'banano';
        } else if (entry.target === pdetStep.value) {
          currentStep.value = 'pdet';
        } else if (entry.target === cacaoStep.value) {
          currentStep.value = 'cacao';
        } else if (entry.target === finalStep.value) {
          currentStep.value = 'final';
        }
      }
    });
  }, options);

  if (coffeeStep.value) observer.observe(coffeeStep.value);
  if (avocadoStep.value) observer.observe(avocadoStep.value);
  if (bananaStep.value) observer.observe(bananaStep.value);
  if (pdetStep.value) observer.observe(pdetStep.value);
  if (cacaoStep.value) observer.observe(cacaoStep.value);
  if (finalStep.value) observer.observe(finalStep.value);

  onUnmounted(() => {
    observer.disconnect();
  });
});
</script>

<style scoped>
</style>