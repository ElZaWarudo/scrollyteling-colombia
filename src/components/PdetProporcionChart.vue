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
    default: 'Proporción de Municipios PDET'
  }
});

const vegaContainer = ref(null);

const spec = {
  $schema: "https://vega.github.io/schema/vega-lite/v5.json",
  width: "container",
  height: 200,
  background: "transparent",
  
  data: {
    url: "/datos_agricolas_colombia.json"
  },
  
  transform: [
    {
      calculate: "datum.es_pdet === true ? 'PDET' : 'Resto del País'",
      as: "category"
    },
    {
      aggregate: [{ op: "count", as: "count" }],
      groupby: ["category"]
    },
    {
      joinaggregate: [{ op: "sum", field: "count", as: "total" }]
    },
    {
      calculate: "datum.count / datum.total * 100",
      as: "percentage"
    }
  ],
  
  layer: [
    {
      mark: { type: "arc", innerRadius: 60, outerRadius: 90 },
      encoding: {
        theta: { field: "count", type: "quantitative", stack: true },
        color: {
          field: "category",
          type: "nominal",
          scale: {
            domain: ["PDET", "Resto del País"],
            range: ["#C62828", "#ECEFF1"] 
          },
          legend: {
            title: "Categoría",
            orient: "bottom"
          }
        },
        order: { field: "category", sort: "descending" },
        tooltip: [
          { field: "category", type: "nominal", title: "Categoría" },
          { field: "count", type: "quantitative", title: "Municipios" },
          { field: "percentage", type: "quantitative", title: "Porcentaje", format: ".1f" }
        ]
      }
    },
    {
      mark: { 
        type: "text", 
        align: "center", 
        baseline: "middle",
        fontSize: 24,
        fontWeight: "bold",
        color: "#C62828"
      },
      transform: [
        { filter: "datum.category === 'PDET'" },
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
        fontSize: 12,
        fontWeight: "normal",
        color: "#666",
        dy: 20
      },
      transform: [
        { filter: "datum.category === 'PDET'" }
      ],
      encoding: {
        text: { value: "Son PDET" }
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
