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
    default: 'Evolución del precio del café'
  }
});

const vegaContainer = ref(null);

const spec = {
  $schema: "https://vega.github.io/schema/vega-lite/v5.json",
  width: "container",
  height: 120,
  background: "transparent",
  
  data: {
    url: "/historico_exportaciones.json",
    format: { 
      type: "json",
      property: "datos_anuales"
    }
  },
  
  transform: [
    { filter: "datum['año'] >= 2010" }
  ],
  
  layer: [
    {
      mark: {
        type: "line",
        point: {
          filled: true,
          size: 80
        },
        strokeWidth: 2,
        color: "#5D4037"
      },
      encoding: {
        x: {
          field: "año",
          type: "ordinal",
          axis: {
            title: "Año",
            labelAngle: -45,
            labelFontSize: 9,
            titleFontSize: 11,
            titleFontWeight: "bold"
          }
        },
        y: {
          field: "cafe_precio_usd",
          type: "quantitative",
          axis: {
            title: "Precio (USD/lb)",
            labelFontSize: 9,
            titleFontSize: 11,
            titleFontWeight: "bold",
            format: "$,.2f"
          },
          scale: {
            zero: false,
            domain: [1, 3.5]
          }
        }
      }
    },
    {
      mark: {
        type: "text",
        align: "left",
        dx: 5,
        dy: -8,
        fontSize: 8,
        fontWeight: "bold",
        color: "#D32F2F"
      },
      transform: [
        { filter: "datum['año'] === 2019" }
      ],
      encoding: {
        x: { field: "año", type: "ordinal" },
        y: { field: "cafe_precio_usd", type: "quantitative" },
        text: { value: "Crisis" }
      }
    },
    {
      mark: {
        type: "text",
        align: "left",
        dx: 5,
        dy: -8,
        fontSize: 8,
        fontWeight: "bold",
        color: "#388E3C"
      },
      transform: [
        { filter: "datum['año'] === 2022" }
      ],
      encoding: {
        x: { field: "año", type: "ordinal" },
        y: { field: "cafe_precio_usd", type: "quantitative" },
        text: { value: "Pico" }
      }
    }
  ],
  
  encoding: {
    tooltip: [
      { field: "año", type: "ordinal", title: "Año" },
      { field: "cafe_precio_usd", type: "quantitative", title: "Precio USD/lb", format: "$,.2f" }
    ]
  },
  
  config: {
    view: {
      stroke: "transparent"
    },
    axis: {
      grid: true,
      gridOpacity: 0.2
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
