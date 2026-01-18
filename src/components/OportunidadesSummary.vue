<template>
  <div class="w-full mt-6">
    <h3 class="text-xl font-bold text-gray-800 mb-2 text-center">
      {{ title }}
    </h3>
    <p class="text-xs text-center text-gray-500 mb-4 px-4">
      Municipios donde cada cultivo es la opción ideal por aptitud y contexto.
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
    default: 'Distribución de Mejores Apuestas'
  }
});

const vegaContainer = ref(null);

const spec = {
  $schema: "https://vega.github.io/schema/vega-lite/v5.json",
  width: "container",
  height: 180,
  background: "transparent",
  
  data: {
    url: "/datos_agricolas_colombia.json"
  },
  
  transform: [
    {
      calculate: "datum.aptitud_cacao === 'Aptitud Alta' ? 'Cacao' : (datum.aptitud_cafe === 'Aptitud Alta' ? 'Café' : (datum.aptitud_banano === 'Aptitud Alta' ? 'Banano' : (datum.aptitud_aguacate === 'Aptitud Alta' ? 'Aguacate' : 'Otros')))",
      as: "Mejor_Apuesta"
    },
    {
      filter: "datum.Mejor_Apuesta !== 'Otros'"
    }
  ],
  
  mark: {
    type: "bar",
    cornerRadiusEnd: 4,
    tooltip: true
  },
  
  encoding: {
    y: {
      field: "Mejor_Apuesta",
      type: "nominal",
      sort: "-x",
      axis: {
        title: null,
        labelFontSize: 12,
        labelFontWeight: "bold",
        labelColor: "#374151"
      }
    },
    x: {
      aggregate: "count",
      field: "Mejor_Apuesta",
      type: "quantitative",
      axis: {
        title: "Número de Municipios",
        grid: false
      }
    },
    color: {
      field: "Mejor_Apuesta",
      type: "nominal",
      scale: {
        domain: ["Cacao", "Café", "Banano", "Aguacate"],
        range: ["#BF360C", "#3E2723", "#F9A825", "#2E7D32"]
      },
      legend: null
    },
    text: {
      aggregate: "count",
      field: "Mejor_Apuesta",
      type: "quantitative"
    }
  },
  
  layer: [
    {
      mark: "bar"
    },
    {
      mark: {
        type: "text",
        align: "left",
        dx: 4,
        fontSize: 12,
        fontWeight: "bold",
        color: "#4B5563"
      },
      encoding: {
        text: { aggregate: "count", field: "Mejor_Apuesta" }
      }
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
