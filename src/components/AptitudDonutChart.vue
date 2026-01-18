<template>
  <div class="w-full mt-4">
    <h3 class="text-2xl font-bold text-gray-800 mb-4 text-center">
      {{ title }}
    </h3>
    <div ref="vegaContainer" class="w-full"></div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import embed from 'vega-embed';

const props = defineProps({
  title: {
    type: String,
    default: 'Aptitud del suelo para el cultivo de café'
  }
});

const vegaContainer = ref(null);

const spec = {
  $schema: "https://vega.github.io/schema/vega-lite/v5.json",
  width: "container",
  height: 50,
  background: "transparent",
  
  data: {
    url: "/datos_agricolas_colombia.json"
  },
  
  transform: [
    {
      aggregate: [{ op: "count", as: "count" }],
      groupby: ["aptitud_cafe"]
    },
    {
      joinaggregate: [{ op: "sum", field: "count", as: "total" }]
    },
    {
      calculate: "datum.count / datum.total * 100",
      as: "percentage"
    },
    {
      calculate: "datum.aptitud_cafe + ': ' + format(datum.percentage, '.1f') + '%'",
      as: "label"
    }
  ],
  
  layer: [
    {
      mark: { type: "arc", innerRadius: 45, outerRadius: 65 },
      encoding: {
        theta: { field: "count", type: "quantitative" },
        color: {
          field: "aptitud_cafe",
          type: "nominal",
          scale: {
            domain: ["Aptitud Alta", "Aptitud Media", "Aptitud Baja", "No Apta", "Exclusión Legal"],
            range: ["#3E2723", "#795548", "#D7CCC8", "#EEEEEE", "#BDBDBD"]
          },
          legend: {
            title: "Aptitud",
            orient: "right",
            labelFontSize: 10,
            titleFontSize: 11
          }
        },
        tooltip: [
          { field: "aptitud_cafe", type: "nominal", title: "Aptitud" },
          { field: "count", type: "quantitative", title: "Municipios" },
          { field: "percentage", type: "quantitative", title: "Porcentaje", format: ".2f" }
        ]
      }
    },
    {
      mark: { 
        type: "text", 
        align: "center", 
        baseline: "middle",
        fontSize: 16,
        fontWeight: "bold",
        color: "#3E2723"
      },
      transform: [
        { filter: "datum.aptitud_cafe === 'Aptitud Alta'" },
        { calculate: "format(datum.percentage, '.1f') + '%'", as: "percentageText" }
      ],
      encoding: {
        text: { field: "percentageText", type: "nominal" }
      }
    },
    {
      mark: { 
        type: "text", 
        align: "center", 
        baseline: "middle",
        fontSize: 8,
        fontWeight: "normal",
        color: "#666",
        dy: 15
      },
      transform: [
        { filter: "datum.aptitud_cafe === 'Aptitud Alta'" }
      ],
      encoding: {
        text: { value: "Aptitud Alta" }
      }
    }
  ],
  
  config: {
    view: {
      stroke: "transparent"
    }
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
