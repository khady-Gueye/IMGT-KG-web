
<template>
  <div class="graph-wrapper">
    <div class="network-container" ref="networkContainer"></div>
  </div>
</template>

<script setup lang="ts">
/* eslint-disable */
import { ref, watch, onMounted, onBeforeUnmount, nextTick } from 'vue'
import { initVisNetwork, type Triple } from '@/utils/Fonctions'

type GraphApi = {
  zoomIn: () => void
  zoomOut: () => void
  fit: () => void
  resetView: () => void
  exportPng: () => Promise<string>
  getData: () => { nodes: any[]; edges: any[] }
}

const emit = defineEmits<{
  (e: 'node-click', iri: string): void
  (e: 'register-api', api: GraphApi): void
}>()

const props = defineProps<{
  triples: Triple[]
  showRelations: boolean
}>()

const networkContainer = ref<HTMLElement | null>(null)
let network: any = null

// Vue initiale pour resetView()
const initialView = {
  pos: { x: 0, y: 0 },
  scale: 1
}

// ————— helpers —————
function applyRelationLabels(show: boolean) {
  if (!network) return
  const edges = network.body?.data?.edges
  if (!edges) return

  const updates = edges.get().map((e: any) => {
    // on s’assure d’avoir mémorisé le libellé d’origine
    const original = e.originalLabel ?? e.label ?? ''
    return {
      id: e.id,
      originalLabel: original,
      label: show ? original : ''
    }
  })
  edges.update(updates)
}

function buildApi(): GraphApi {
  return {
    zoomIn: () => {
      if (!network) return
      const s = network.getScale?.() ?? 1
      network.moveTo?.({ scale: Math.min(s * 1.2, 4) })
    },
    zoomOut: () => {
      if (!network) return
      const s = network.getScale?.() ?? 1
      network.moveTo?.({ scale: Math.max(s / 1.2, 0.05) })
    },
    fit: () => {
      network?.fit?.({ animation: { duration: 300, easingFunction: 'easeInOutQuad' } })
    },
    resetView: () => {
      if (!network) return
      network.moveTo?.({
        position: { ...initialView.pos },
        scale: initialView.scale,
        animation: { duration: 300, easingFunction: 'easeInOutQuad' }
      })
    },
    exportPng: async () => {
      await nextTick()
      // vis-network expose un canvas interne
      const canvasEl =
        network?.canvas?.frame?.canvas ??
        (networkContainer.value?.querySelector('canvas') as HTMLCanvasElement | null)

      if (!canvasEl) return ''
      try {
        return canvasEl.toDataURL('image/png')
      } catch {
        // fallback (rare)
        return ''
      }
    },
    getData: () => {
      const nodes = network?.body?.data?.nodes?.get?.() ?? []
      const edges = network?.body?.data?.edges?.get?.() ?? []
      return { nodes, edges }
    }
  }
}

// ————— init/destroy —————
async function initializeNetwork() {
  if (!networkContainer.value) return
  await nextTick()

  // détruire l’instance précédente
  try { network?.destroy?.() } catch {}

  const { network: net } = await initVisNetwork(
    networkContainer.value,
    props.triples,
    (clickedNodeId: string) => emit('node-click', clickedNodeId),
    props.showRelations ?? false
  )
  network = net

  // mémorise label d’origine sur chaque arête
  const edges = network.body?.data?.edges
  if (edges) {
    const all = edges.get()
    edges.update(
      all.map((e: any) => ({
        id: e.id,
        originalLabel: e.originalLabel ?? e.label ?? ''
      }))
    )
  }

  // applique l’état showRelations courant
  applyRelationLabels(props.showRelations)

  // vue initiale pour reset
  try {
    initialView.pos = network.getViewPosition?.() ?? { x: 0, y: 0 }
    initialView.scale = network.getScale?.() ?? 1
  } catch {}

  // expose l’API au parent
  emit('register-api', buildApi())
}

onMounted(initializeNetwork)

onBeforeUnmount(() => {
  try { network?.destroy?.() } catch {}
  network = null
})

// réinit quand les données changent
watch(
  () => props.triples,
  () => initializeNetwork(),
  { deep: true }
)

// mise à jour live de l’affichage des relations
watch(
  () => props.showRelations,
  (val) => applyRelationLabels(val)
)
</script>

<style scoped>
.graph-wrapper {
  display: flex;
  width: 100%;
  height: 80vh;
}

.network-container {
  flex: 1;
  border: 1px solid #ddd;
  background-color: #fff;
}
</style>








