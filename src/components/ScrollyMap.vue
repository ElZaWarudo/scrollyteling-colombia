<template>
  <div class="relative w-full h-full min-h-screen">
    <div 
      ref="vegaContainerCoffee" 
      class="transition-opacity duration-700 ease-in-out"
      :class="currentStep === 'cafe' ? 'opacity-100' : 'opacity-0'"
      :style="{ position: 'absolute', top: 0, left: 0, width: '100%', height: '100%', pointerEvents: currentStep === 'cafe' ? 'auto' : 'none' }"
    ></div>
    
    <div 
      ref="vegaContainerAvocado" 
      class="transition-opacity duration-700 ease-in-out"
      :class="currentStep === 'aguacate' ? 'opacity-100' : 'opacity-0'"
      :style="{ position: 'absolute', top: 0, left: 0, width: '100%', height: '100%', pointerEvents: currentStep === 'aguacate' ? 'auto' : 'none' }"
    ></div>

    <div 
      ref="vegaContainerPdet" 
      class="transition-opacity duration-700 ease-in-out"
      :class="currentStep === 'pdet' ? 'opacity-100' : 'opacity-0'"
      :style="{ position: 'absolute', top: 0, left: 0, width: '100%', height: '100%', pointerEvents: currentStep === 'pdet' ? 'auto' : 'none' }"
    ></div>

    <div 
      ref="vegaContainerCacao" 
      class="transition-opacity duration-700 ease-in-out"
      :class="currentStep === 'cacao' ? 'opacity-100' : 'opacity-0'"
      :style="{ position: 'absolute', top: 0, left: 0, width: '100%', height: '100%', pointerEvents: currentStep === 'cacao' ? 'auto' : 'none' }"
    ></div>

    <div 
      ref="vegaContainerBanana" 
      class="transition-opacity duration-700 ease-in-out"
      :class="currentStep === 'banano' ? 'opacity-100' : 'opacity-0'"
      :style="{ position: 'absolute', top: 0, left: 0, width: '100%', height: '100%', pointerEvents: currentStep === 'banano' ? 'auto' : 'none' }"
    ></div>

    <div 
      ref="vegaContainerFinal" 
      class="transition-opacity duration-700 ease-in-out"
      :class="currentStep === 'final' ? 'opacity-100' : 'opacity-0'"
      :style="{ position: 'absolute', top: 0, left: 0, width: '100%', height: '100%', pointerEvents: currentStep === 'final' ? 'all' : 'none' }"
    ></div>
  </div>
</template>

<script setup>
import { onMounted, ref, toRefs } from 'vue';
import embed from 'vega-embed';

const props = defineProps({
  currentStep: {
    type: String,
    default: 'cafe' 
  }
});

const { currentStep } = toRefs(props);
const vegaContainerCoffee = ref(null);
const vegaContainerAvocado = ref(null);
const vegaContainerPdet = ref(null);
const vegaContainerCacao = ref(null);
const vegaContainerBanana = ref(null);
const vegaContainerFinal = ref(null);

const configurations = {
  cafe: {
    field: 'aptitud_cafe',
    title: 'Aptitud Café',
    domain: ["Aptitud Alta", "Aptitud Media", "Aptitud Baja", "No Apta", "Exclusión Legal"],
    range: ["#3E2723", "#795548", "#D7CCC8", "#EEEEEE", "#FFFFFF"],
    background: "transparent",
    stroke: "#37474F",
    strokeWidth: 0.8
  },
  aguacate: {
    field: 'aptitud_aguacate',
    title: 'Aptitud Aguacate',
    domain: ["Aptitud Alta", "Aptitud Media", "Aptitud Baja", "No Apta", "Exclusión Legal"],
    range: ["#2E7D32", "#66BB6A", "#C8E6C9", "#EEEEEE", "#FFFFFF"],
    background: "transparent",
    stroke: "#37474F",
    strokeWidth: 0.8
  },
  pdet: {
    field: 'es_pdet',
    title: 'Municipios PDET',
    domain: ["PDET", "No PDET"],
    range: ["#C62828", "#ECEFF1"],
    background: "#FFFFFF",
    stroke: "#78909C",
    strokeWidth: 0.8
  },
  cacao: {
    field: 'aptitud_cacao',
    title: 'Aptitud Cacao',
    domain: ["Aptitud Alta", "Aptitud Media", "Aptitud Baja", "No Apta", "Exclusión Legal"],
    range: ["#BF360C", "#D84315", "#FFCCBC", "#EEEEEE", "#FFFFFF"],
    background: "transparent",
    stroke: "#37474F",
    strokeWidth: 0.8
  },
  banano: {
    field: 'aptitud_banano',
    title: 'Aptitud Banano',
    domain: ["Aptitud Alta", "Aptitud Media", "Aptitud Baja", "No Apta", "Exclusión Legal"],
    range: ["#F9A825", "#FDD835", "#FFF59D", "#EEEEEE", "#FFFFFF"],
    background: "transparent",
    stroke: "#37474F",
    strokeWidth: 0.8
  }
};

const generateSpec = (step) => {
  if (step === 'final') {
    return {
      $schema: "https://vega.github.io/schema/vega-lite/v5.json",
      width: "container",
      height: "container",
      autosize: { type: "none" },
      background: "transparent",
      
      params: [
        {
          name: "zoom",
          value: 2000,
          bind: { input: "range", min: 500, max: 6000, step: 50, name: "Zoom: " }
        },
        {
          name: "center_lon",
          value: -74.2,
          bind: { input: "range", min: -80, max: -66, step: 0.1, name: "Eje X: " }
        },
        {
          name: "center_lat",
          value: 4.6,
          bind: { input: "range", min: -5, max: 13, step: 0.1, name: "Eje Y: " }
        }
      ],

      projection: {
        type: "mercator",
        scale: { expr: "zoom" },
        center: { expr: "[center_lon, center_lat]" }
      },

      data: {
        url: "/colombia.topojson",
        format: { type: "topojson", feature: "MGN_ANM_MPIOS" } 
      },

      transform: [
        {
          lookup: "properties.MPIO_CDPMP",
          from: {
            data: { url: "/datos_agricolas_colombia.json" },
            key: "Código Dane Municipio",
            fields: ["Municipio", "es_pdet", "aptitud_cafe", "aptitud_aguacate", "aptitud_banano", "aptitud_cacao"]
          }
        },
        {
          calculate: "datum.es_pdet ? 'Sí' : 'No'",
          as: "Es_PDET_Texto"
        },
        {
            calculate: "datum.aptitud_cacao === 'Aptitud Alta' ? 'Cacao' : (datum.aptitud_cafe === 'Aptitud Alta' ? 'Café' : (datum.aptitud_banano === 'Aptitud Alta' ? 'Banano' : (datum.aptitud_aguacate === 'Aptitud Alta' ? 'Aguacate' : 'No hay')))",
            as: "Mejor_Apuesta"
        }
      ],
      
      mark: { 
        type: "geoshape", 
        stroke: "white",
        strokeWidth: 0.5,
        cursor: "pointer"
      },
      
      encoding: {
        color: {
            field: "Mejor_Apuesta",
            type: "nominal",
            scale: {
                domain: ["Cacao", "Café", "Banano", "Aguacate", "No hay"],
                range: ["#BF360C", "#3E2723", "#F9A825", "#2E7D32", "#CFD8DC"]
            },
            legend: { title: "Mejor opción para el municipio", orient: "bottom-left" }
        },
        tooltip: [
          { field: "Municipio", type: "nominal", title: "Municipio" },
          { field: "Es_PDET_Texto", title: "¿Es PDET?" },
          { field: "Mejor_Apuesta", title: "Mejor Apuesta" },
        ]
      }
    };
  }

  const config = configurations[step] || configurations.cafe;

  if (step === 'pdet') {
    return {
      $schema: "https://vega.github.io/schema/vega-lite/v5.json",
      width: "container",
      height: "container",
      autosize: { type: "none" },
      background: config.background,

      projection: {
        type: "mercator",
        scale: 2000,
        center: [-74.2, 4.6]
      },
      
      data: {
        url: "/colombia.topojson",
        format: { type: "topojson", feature: "MGN_ANM_MPIOS" } 
      },
      
      transform: [
        {
          lookup: "properties.MPIO_CDPMP",
          from: {
            data: { url: "/datos_agricolas_colombia.json" },
            key: "Código Dane Municipio",
            fields: ["es_pdet", "Municipio"]
          }
        },
        {
          calculate: "datum.es_pdet === true ? 'PDET' : 'No PDET'",
          as: "estado_pdet"
        }
      ],

      mark: { 
        type: "geoshape", 
        stroke: config.stroke || "#white", 
        strokeWidth: config.strokeWidth || 0.2 
      },
      encoding: {
        color: {
          field: "estado_pdet",
          type: "nominal",
          scale: {
            domain: config.domain,
            range: config.range
          },
          legend: { title: config.title, orient: "bottom-left" }
        },
        tooltip: [
          { field: "Municipio", type: "nominal" },
          { field: "estado_pdet", type: "nominal", title: "Estado" }
        ]
      }
    };
  }

  return {
      $schema: "https://vega.github.io/schema/vega-lite/v5.json",
      width: "container",
      height: "container",
      autosize: { type: "none" },
      background: config.background,

      projection: {
        type: "mercator",
        scale: 2000,
        center: [-74.2, 4.6]
      },
    
    data: {
      url: "/colombia.topojson",
      format: { type: "topojson", feature: "MGN_ANM_MPIOS" } 
    },
    
    transform: [
      {
        lookup: "properties.MPIO_CDPMP",
        from: {
          data: { url: "/datos_agricolas_colombia.json" },
          key: "Código Dane Municipio",
          fields: [config.field, "Municipio", "total_has_" + step]
        }
      }
    ],

    mark: { 
      type: "geoshape", 
      stroke: config.stroke || "#white", 
      strokeWidth: config.strokeWidth || 0.2 
    },
    encoding: {
      color: {
        field: config.field,
        type: "nominal",
        scale: { domain: config.domain, range: config.range },
        legend: { title: config.title, orient: "bottom-left" }
      },
      tooltip: [
        { field: "Municipio", type: "nominal" },
        { field: config.field, type: "nominal", title: "Aptitud" },
        { field: "total_has_" + step, type: "quantitative", title: "Hectáreas Aptas", format: ",.0f" }
      ]
    }
  };
};

const renderChart = async (step, containerRef) => {
  if (containerRef.value) {
    const spec = generateSpec(step);
    await embed(containerRef.value, spec, { actions: false });
    
    if (containerRef.value) {
      const vegaEmbed = containerRef.value.querySelector('.vega-embed');
      if (vegaEmbed) {
        vegaEmbed.style.position = 'absolute';
        vegaEmbed.style.width = '100%';
        vegaEmbed.style.height = '100%';
        vegaEmbed.style.display = 'block';
      }
    }
  }
};

onMounted(async () => {
  await Promise.all([
    renderChart('cafe', vegaContainerCoffee),
    renderChart('aguacate', vegaContainerAvocado),
    renderChart('pdet', vegaContainerPdet),
    renderChart('cacao', vegaContainerCacao),
    renderChart('banano', vegaContainerBanana),
    renderChart('final', vegaContainerFinal)
  ]);
});
</script>

<style scoped>
/* Forzar que los controles de Vega (sliders) floten sobre el mapa */
:deep(.vega-bindings) {
  position: absolute;
  top: 20px;
  right: 20px;
  background: rgba(255, 255, 255, 0.95);
  padding: 12px;
  border-radius: 12px;
  box-shadow: 0 4px 15px rgba(0,0,0,0.15);
  z-index: 1000;
  display: flex;
  flex-direction: column;
  gap: 10px;
  backdrop-filter: blur(4px);
  border: 1px solid rgba(0,0,0,0.05);
  max-width: 250px;
  
  /* Por defecto ocultos para evitar que se vean en transiciones o pasos incorrectos */
  opacity: 0;
  pointer-events: none;
  visibility: hidden; 
  transition: opacity 0.3s ease, visibility 0.3s;
}

/* Solo mostrar los controles cuando el contenedor padre es visible (tiene opacity-100) */
.opacity-100 :deep(.vega-bindings) {
  opacity: 1;
  pointer-events: auto;
  visibility: visible;
}

:deep(.vega-bind) {
  display: flex;
  flex-direction: column;
  font-family: system-ui, -apple-system, sans-serif;
  font-size: 0.85rem;
  color: #333;
}

:deep(.vega-bind-name) {
  font-weight: 600;
  margin-bottom: 4px;
  color: #4a4a4a;
}

:deep(input[type="range"]) {
  width: 100%;
  accent-color: #333;
}
</style>