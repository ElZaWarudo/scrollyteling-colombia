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

defineProps({
  title: {
    type: String,
    default: 'Comparativo de Exportaciones'
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
      fold: ["banano_toneladas", "aguacate_toneladas"],
      as: ["cropType", "tons"]
    },
    {
      calculate: "datum.tons / 1000",
      as: "tonsInThousands"
    },
    {
      calculate: "datum.cropType == 'banano_toneladas' ? 'Banano' : 'Aguacate'",
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
        domain: ["banano_toneladas", "aguacate_toneladas"],
        range: ["#FFEB3B", "#2E7D32"]
      },
      legend: {
        title: "Cultivo",
        labelExpr: "datum.label == 'banano_toneladas' ? 'Banano' : 'Aguacate'",
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
    await embed(vegaContainer.value, spec, { actions: false });
    
    const vegaEmbed = vegaContainer.value.querySelector('.vega-embed');
    if (vegaEmbed) {
      vegaEmbed.style.width = '100%';
      vegaEmbed.style.display = 'flex';
      vegaEmbed.style.justifyContent = 'center';
    }
  }
});
</script>

<style scoped>
</style>
