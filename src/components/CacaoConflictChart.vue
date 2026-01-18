<template>
  <div class="w-full mt-4">
    <h3 class="text-2xl font-bold text-gray-800 mb-2 text-center">
      {{ title }}
    </h3>
    <p class="text-sm text-gray-500 text-center mb-4">
      Porcentaje de municipios con <span class="font-bold">Alta Aptitud</span> que son PDET
    </p>
    <div ref="vegaContainer" class="w-full"></div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import embed from 'vega-embed';

const props = defineProps({
  title: {
    type: String,
    default: 'Aptitud en Zonas de Conflicto'
  }
});

const vegaContainer = ref(null);

const spec = {
  $schema: "https://vega.github.io/schema/vega-lite/v5.json",
  width: "container",
  height: 250,
  background: "transparent",
  
  data: {
    url: "/datos_agricolas_colombia.json"
  },
  
  transform: [
    {
      fold: ["aptitud_cacao", "aptitud_aguacate", "aptitud_cafe", "aptitud_banano"],
      as: ["cropKey", "aptitudeValue"]
    },
    {
      filter: "datum.aptitudeValue === 'Aptitud Alta'"
    },
    {
      calculate: "{'aptitud_cacao': 'Cacao', 'aptitud_aguacate': 'Aguacate', 'aptitud_cafe': 'Café', 'aptitud_banano': 'Banano'}[datum.cropKey]",
      as: "cropName"
    },
    {
      calculate: "datum.es_pdet === true ? 'Zona PDET' : 'Otros Municipios'",
      as: "zone"
    }
  ],
  
  layer: [
    {
      mark: "bar",
      encoding: {
        y: { 
          field: "cropName", 
          type: "nominal", 
          title: null,
          sort: ["Cacao", "Café", "Banano", "Aguacate"]
        },
        x: { 
          aggregate: "count", 
          field: "cropName", 
          type: "quantitative", 
          stack: "normalize",
          axis: { format: "%", title: "Distribución de Municipios Aptos" }
        },
        color: {
          field: "zone",
          type: "nominal",
          scale: { "domain": ["Zona PDET", "Otros Municipios"], "range": ["#C62828", "#CFD8DC"] },
          legend: { title: null, orient: "top" }
        },
        tooltip: [
          { field: "cropName", title: "Cultivo" },
          { field: "zone", title: "Zona" },
          { aggregate: "count", title: "No. Municipios" }
        ]
      }
    },
    {
      mark: { type: "text", align: "left", dx: 5, color: "white", fontWeight: "bold" },
      encoding: {
        y: { field: "cropName", type: "nominal" },
        x: { aggregate: "count", field: "cropName", type: "quantitative", stack: "normalize" },
        text: { 
            aggregate: "count", 
            type: "quantitative", 
            format: ".1%",
            condition: { test: "datum.zone === 'Otros Municipios'", value: "" }
        },
        color: { value: "white" },
        detail: { field: "zone" }
      },
      transform: [
          { filter: "datum.zone === 'Zona PDET'" }
      ]
    }
  ],
  
  config: {
    view: { stroke: "transparent" },
    axis: { domain: false, tickSize: 0 }
  }
};

onMounted(async () => {
  if (vegaContainer.value) {
    try {
      await embed(vegaContainer.value, spec, {
        actions: false,
        renderer: 'svg'
      });
    } catch (error) {
      console.error('Error loading chart:', error);
    }
  }
});
</script>

<style scoped>
</style>
