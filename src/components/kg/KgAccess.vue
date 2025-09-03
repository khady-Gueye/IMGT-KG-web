<template>
  <section class="kgaccess-page">
  <section class="zone-main">
    
    <!-- Titre -->
    <v-row>
      <v-col>
        <v-card class="card-title mx-auto w-100" title="">
          <div class="title text-h5 text-center p-20">IMGT-KG Access</div>
        </v-card>
      </v-col>
    </v-row>

    <!-- Intro -->
    <v-row>
      <v-col>
        <v-card>
          <p class="mb-4">
            This page provides access to <strong>IMGT-KG</strong> data. The interface is powered by
            <v-chip class="ma-0 v-chip-link" color="primary" label>
              <a href="https://yasgui.triply.cc/">YASGUI</a>
            </v-chip>. you can to access to
            <v-chip class="ma-0 v-chip-link" color="primary" label>
              <a href="/fuseki/#/dataset/ImgtKg/query">IMGT-KG SPARQL Endpoint Server directly</a>
            </v-chip>can be accessed, which uses
            <v-chip class="ma-0 v-chip-link" color="primary" label>
              <a href="https://jena.apache.org/documentation/fuseki2/">Apache Jena Fuseki</a>
            </v-chip> as SPARQL server and
            <v-chip class="ma-0 v-chip-link" color="primary" label>
              <a href="https://jena.apache.org/documentation/tdb2/">Apache Jena TDB</a>
            </v-chip> as triplestore. It provides acces to the
            <strong>IMGT-KG</strong> data. Some examples are provided below.
          </p>
        </v-card>
      </v-col>
    </v-row>

    <!-- Exemples SPARQL -->
    <v-row>
      <v-col>
        <v-card class="mx-auto">
          <div class="title text-h5 text-center p-20">SPARQL Query examples</div>
          <v-list>
            <v-list-item
              v-for="(item, i) in items"
              :key="i"
              :value="item"
              active-color="primary"
              rounded="shaped"
            >
              <template #prepend>
                <!-- <svg style="width:24px;height:24px" viewBox="0 0 24 24">
    <path :d="mdiDatabaseSearchOutline" />
  </svg> -->
                <v-icon icon="mdi-database-search-outline" />
              </template>
              <v-list-item-title
                v-text="item.text"
                @click="readFiles(item.req)"
                class="cursor-pointer"
              />
            </v-list-item>
          </v-list>
        </v-card>
      </v-col>
    </v-row>

    <!-- YASGUI -->
    <v-row>
      <v-col>
        <v-card>
          <div id="yasgui" />
        </v-card>
      </v-col>
    </v-row>
  </section>
</section>
</template>

<script setup lang="ts">
// ⚠️ Ne pas vider le localStorage au top-level (HMR déclenchements multiples)
// localStorage.clear();

import { onMounted, ref } from 'vue'
//import { mdiDatabaseSearchOutline } from '@mdi/js'

type QueryItem = { text: string; req: string }

const items = ref<QueryItem[]>([
  { text: 'Find information on the genes/alleles functionality and why they are not functional in the case of a pseudogene, for example.', req: '/Queries/Query_scenario_1.rq' },
  { text: 'Explore crystal structures and the associated external links inluding epitote on IEDB, PUBMED article, PDB link and Jmol visualisation.', req: '/Queries/Query_scenario_2.rq' },
  { text: 'Find the COVID-19 spike protein with the associated chains, genes, alleles and genomics reference sequences.', req: '/Queries/Query_scenario_3.rq' },
  { text: 'Select an Allele which is partial and for which there exists a complete literature sequence.', req: '/Queries/Query1.rq' },
  { text: 'Find why a gene is pseudogene based on its functionality qualifier.', req: '/Queries/Query2.rq' },
  { text: 'Select genes which have not a given feature: L-PART-1, for example.', req: '/Queries/Query3.rq' },
  { text: 'Select groups, genes, alleles and their CDR3-IMGT length.', req: '/Queries/Query4.rq' },
  { text: 'Select pseudogenes without L-PART-1.', req: '/Queries/Query5.rq' },
  { text: 'Select chains and their alleles with allele properties.', req: '/Queries/Query6.rq' },
  { text: 'More through way to describe a chain and its association with an allele.', req: '/Queries/Query7.rq' },
  { text: 'Unification of nucleotide sequences (genemics) and amino acide sequences (proteins).', req: '/Queries/Query8.rq' },
  { text: 'Selection of structures with their bibliographic reference [...] and the external link to IEDB and Pubmed.', req: '/Queries/Query9.rq' },
  { text: 'Retrieve crystal structures and their related annotations including external links (jmol visualisation, imgt_display, pdb ... ).', req: '/Queries/Query10.rq' },
  { text: 'Description of a particular chain (chain-1QLFA).', req: '/Queries/Query11.rq' },
])

// Refs Yasgui
const yasgui = ref<any>(null)
const yasqe  = ref<any>(null)
const yasr   = ref<any>(null)

// UI state
const loading = ref(false)
const error   = ref<string | null>(null)

// Charge un script une seule fois
function loadScript (src: string): Promise<void> {
  return new Promise((resolve, reject) => {
    // ✅ Sélecteur correct pour vérifier si déjà injecté
    if (document.querySelector(`script[src="${src}"]`)) return resolve()
    const s = document.createElement('script')
    s.src = src
    s.async = true
    s.onload = () => resolve()
    s.onerror = () => reject(new Error(`Fail load: ${src}`))
    document.head.appendChild(s)
  })
}

// Charge un CSS une seule fois
function loadCss (href: string): void {
  // ✅ Sélecteur correct
  if (document.querySelector(`link[href="${href}"]`)) return
  const l = document.createElement('link')
  l.rel = 'stylesheet'
  l.href = href
  document.head.appendChild(l)
}

// Ajoute un LIMIT si absent (pour éviter des réponses gigantesques)
function ensureLimit(q: string, defaultLimit = 200) {
  const hasLimit = /\blimit\s+\d+\b/i.test(q)
  return hasLimit ? q : `${q.trim()}\nLIMIT ${defaultLimit}`
}

// Charge un fichier .rq et exécute la requête proprement
async function readFiles (file: string) {
  if (!yasqe.value) return
  error.value = null
  loading.value = true
  try {
    // Préfixes utiles (idempotent)
    yasqe.value.addPrefixes({
      'imgt': 'https://www.imgt.org/imgt-ontology#',
      'ncit': 'http://ncicb.nci.nih.gov/xml/owl/EVS/Thesaurus.owl#',
      'skos': 'http://www.w3.org/2004/02/skos/core#',
      'obo' : 'http://purl.obolibrary.org/obo/',
      'faldo': 'http://biohackathon.org/resource/faldo#',
      'owl': 'http://www.w3.org/2002/07/owl#',
      'rdfs': 'http://www.w3.org/2000/01/rdf-schema#'
    })

    const res  = await fetch(file)
    if (!res.ok) throw new Error(`Impossible de charger ${file}`)
    let text   = await res.text()
    text = ensureLimit(text, 200)

    // Met à jour l’éditeur
    yasqe.value.setValue(text)
    yasqe.value.collapsePrefixes(true)

    // Annule la requête précédente si nécessaire
    try { yasqe.value.abortQuery?.() } catch {console.warn('Abort failed')}

    // Exécute (POST + timeout via la config Yasgui)
    await yasqe.value.query()
  } catch (e: any) {
    error.value = e?.message ?? String(e)
  } finally {
    loading.value = false
  }
}

onMounted(async () => {
  // Vide une seule fois si tu veux repartir propre
  try { localStorage.clear() } catch {console.warn('localStorage clear failed')}

  // Injecte YASGUI
  loadCss('https://unpkg.com/@triply/yasgui/build/yasgui.min.css')
  await loadScript('https://unpkg.com/@triply/yasgui/build/yasgui.min.js')

  const Y = (window as any).Yasgui
  if (!Y) {
    error.value = 'Yasgui non disponible'
    return
  }

  yasgui.value = new Y(document.getElementById('yasgui'), {
    // ✅ Envoie en POST pour éviter les URLs énormes
    requestConfig: {
      endpoint: 'https://www.imgt.org/fuseki/ImgtKg/sparql',
      method: 'POST',
      timeout: 30000,
      headers: {
        Accept: 'application/sparql-results+json'
      }
    },
    autofocus: false,
    copyEndpointOnNewTab: true,
    tabName: 'Query',
    yasr: {
      prefixes: {
        'imgt': 'https://www.imgt.org/imgt-ontology#',
        'ncit': 'http://ncicb.nci.nih.gov/xml/owl/EVS/Thesaurus.owl#',
        'obo' : 'http://purl.obolibrary.org/obo/',
        'faldo': 'http://biohackathon.org/resource/faldo#',
        'so'  : ' http://purl.obolibrary.org/obo/so#',
        'owl' : 'http://www.w3.org/2002/07/owl#',
        'skos': 'http://www.w3.org/2004/02/skos/core#',
        'pubmed': 'https://pubmed.ncbi.nlm.nih.gov/',
        'rdfs': 'http://www.w3.org/2000/01/rdf-schema#',
        'bibo': 'http://purl.org/ontology/bibo/'
      },
      maxLines: 30,
    },
  })

  yasqe.value = yasgui.value.getTab().yasqe
  yasr.value  = yasgui.value.getTab().yasr

  // Préfixes init
  yasqe.value.addPrefixes({
    'imgt': 'https://www.imgt.org/imgt-ontology#',
    'ncit': 'http://ncicb.nci.nih.gov/xml/owl/EVS/Thesaurus.owl#',
    'skos': 'http://www.w3.org/2004/02/skos/core#',
    'obo' : 'http://purl.obolibrary.org/obo/',
    'faldo': 'http://biohackathon.org/resource/faldo#',
    'owl' : 'http://www.w3.org/2002/07/owl#',
    'rdfs': 'http://www.w3.org/2000/01/rdf-schema#'
  })
})
</script>

<style scoped>
@import url('https://fonts.cdnfonts.com/css/source-sans-pro');

/* zone centrale de la page */
.zone-main {
  margin-top: 20px !important;
  width: 85%;
  margin: auto;
}

/* titre au-dessus des cartes */
.title {
  padding: 20px;
  font-weight: 500 !important;
  color: #444 !important;
}

/* texte de paragraphe */
.text-body-2 {
  text-align: justify;
}

/* liens et chips */
a {
  text-decoration: none;
  color: darkblue;
}
.v-chip-link a {
  font-weight: bold;
}
.v-chip-link a:hover {
  color: crimson !important;
}
.v-chip-link {
  height: 23px !important;
}

/* drawer (si présent dans cette page) */
.v-navigation-drawer a {
  color: white !important;
}

/* tableaux (natifs et Vuetify) */
table,
.v-table {
  width: 100%;
  border-spacing: 0;
  background-color: white;
  border-radius: 5px;
}
th {
  border-bottom: thin solid rgba(var(--v-border-color), var(--v-border-opacity));
  color: rgba(var(--v-theme-on-surface), var(--v-medium-emphasis-opacity));
  padding: 0 16px;
  transition: height cubic-bezier(.4, 0, .2, 1);
  font-weight: 500;
  user-select: none;
  text-align: start;
  height: calc(var(--v-table-header-height) + 0px);
  color: black;
}
td {
  border-bottom: thin solid rgba(var(--v-border-color), var(--v-border-opacity));
  height: calc(var(--v-table-row-height, 52px) + 0px);
  padding: 0 16px;
  transition: height cubic-bezier(.4, 0, .2, 1);
}
td,
th {
  border-bottom: solid 1px rgba(210, 210, 210, 0.8);
}

/* YASGUI : masquer l’autocomplete */
:deep(.yasgui .autocompleteWrapper) {
  display: none !important;
}
.kgaccess-page { background: #eee; }
</style>

