# IMGT-KG-web

Web interface for exploring and visualizing the **IMGT/mAb-KG** and **IMGT-KG** immunogenetics knowledge graphs. Built during my M2 Bioinformatics internship at IMGT (Institut de Génétique Humaine, CNRS, Montpellier), it aims to make knowledge graphs related to therapeutic monoclonal antibodies accessible to researchers, including those unfamiliar with the semantic web and SPARQL queries.

Unlike IMGT's existing interfaces (one per graph, built with different technologies), this interface unifies access to both resources in a single experience, combining an interactive graph visualization with a synchronized tabular view of the data.

## Key Features

* **Entity-centered search**: explore a monoclonal antibody (mAb) and all its relations (target, structure, pharmaceutical products, mechanism of action).
* **Interactive graph visualization**: entities and relations displayed as a navigable graph (zoom, pan, node selection) via `vis-network`.
* **Synchronized RDF table**: detailed list of triples (subject, predicate, object) linked to the graph, updated in real time.
* **Dynamic filtering**: select which entity types to display (mAb, Target, Construct, Product, MOA) via checkboxes, with no need to write SPARQL.
* **SPARQL query generation**: automatic translation of the user's search into queries against the IMGT/mAb-KG endpoint.
* **Modular architecture**: independent, reusable Vue.js components, designed to allow future extension to IMGT-KG.

## Architecture

The project is built with **Vue.js 3** (Composition API, `<script setup lang="ts">`) and **TypeScript**, for a typed, modular and scalable interface.

**Tech stack**
* `Vue 3` + `Vue CLI` — front-end framework and tooling
* `TypeScript` — static typing (RDF triples, entity types, graph nodes)
* `vis-network` — interactive graph visualization library
* `axios` — HTTP requests to the IMGT/mAb-KG SPARQL endpoint

**Main components**
* `App.vue`: navigation and overall layout of the interface
* `SidebarNav.vue`: side navigation between the IMGT/mAb-KG and IMGT-KG graphs
* `SidebarFilters.vue`: dynamic filters by entity type
* `GraphWorkspace.vue`: central component — search, results display and data table
* `GraphDisplay.vue`: renders the interactive graph via `vis-network`
* `HomeCover.vue`: home screen shown when there are no results
* `Fonctions.ts`: utility functions centralizing SPARQL calls, parsing and data typing

**Processing pipeline**

```
User input
      │
      ▼
SPARQL query generation
      │
      ▼
Request sent to the IMGT/mAb-KG endpoint
      │
      ▼
Results retrieval (CSV)
      │
      ▼
Triple parsing & typing
      │
      ▼
Dynamic filtering by entity type
      │
      ▼
Display: RDF table + interactive graph
```

## Video Demo

[Watch the demo video](video/demo_video.mp4)

## Resources

* Internship report: *Development of a unified web interface for exploiting the IMGT-KG knowledge graphs in immunogenetics* (M2 Bioinformatics, University of Montpellier, 2025)
* [IMGT/mAb-KG](https://imgt.org/mAb-KG/)
* [IMGT-KG](https://www.imgt.org/imgt-kg/)

## Usage

### Requirements

* Node.js and npm

### Installation

```
npm install
```

### Run in development mode

```
npm run serve
```

### Production build

```
npm run build
```
