<template>
  <div class="w-full mt-8">
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
    default: 'Exportaciones de café vs aguacate'
  }
});

const vegaContainer = ref(null);

const spec = {
  $schema: "https://vega.github.io/schema/vega-lite/v5.json",
  width: "container",
  height: 250,
  background: "transparent",
  
  data: {
    url: "/historico_exportaciones.json",
    format: { 
      type: "json",
      property: "datos_anuales"
    }
  },
  
  transform: [
    { filter: "datum['año'] >= 2010" },
    {
      fold: ["cafe_toneladas", "aguacate_toneladas"],
      as: ["cropType", "tons"]
    },
    {
      calculate: "datum.tons / 1000",
      as: "tonsInThousands"
    },
    {
      calculate: "datum.cropType == 'cafe_toneladas' ? 'Café' : 'Aguacate'",
      as: "readableCropName"
    }
  ],
  
  mark: {
    type: "line",
    point: {
      filled: true,
      size: 80
    },
    strokeWidth: 3
  },
  
  encoding: {
    x: {
      field: "año",
      type: "ordinal",
      axis: {
        title: "Año",
        labelAngle: -45,
        labelFontSize: 11,
        titleFontSize: 13,
        titleFontWeight: "bold"
      }
    },
    y: {
      field: "tonsInThousands",
      type: "quantitative",
      axis: {
        title: "Miles de Toneladas",
        labelFontSize: 11,
        titleFontSize: 13,
        titleFontWeight: "bold",
        format: ",.0f"
      },
      scale: {
        zero: false
      }
    },
    color: {
      field: "cropType",
      type: "nominal",
      scale: {
        domain: ["cafe_toneladas", "aguacate_toneladas"],
        range: ["#5D4037", "#2E7D32"]
      },
      legend: {
        title: "Cultivo",
        labelExpr: "datum.label == 'cafe_toneladas' ? 'Café' : 'Aguacate'",
        orient: "top",
        labelFontSize: 12,
        titleFontSize: 13,
        symbolSize: 100
      }
    },
    tooltip: [
      { field: "año", type: "ordinal", title: "Año" },
      {
        field: "readableCropName",
        type: "nominal",
        title: "Cultivo"
      },
      {
        field: "tons",
        type: "quantitative",
        title: "Toneladas",
        format: ",.0f"
      }
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
