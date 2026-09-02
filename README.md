# IMGT-KG-web

Interface web d'exploration et de visualisation des graphes de connaissances en immunogénétique **IMGT/mAb-KG** et **IMGT-KG**. Développée dans le cadre de mon stage de M2 Bioinformatique à l'IMGT (Institut de Génétique Humaine, CNRS, Montpellier), elle vise à rendre les graphes de connaissances liés aux anticorps monoclonaux thérapeutiques accessibles aux chercheurs, y compris ceux non familiers avec le web sémantique et les requêtes SPARQL.

Contrairement aux interfaces existantes d'IMGT (une par graphe, technologies différentes), cette interface unifie l'accès aux deux ressources dans une expérience unique, avec une visualisation de graphe interactive et une lecture tabulaire synchronisée des données.

## Fonctionnalités Clés

* **Recherche centrée sur une entité** : exploration d'un anticorps monoclonal (mAb) et de toutes ses relations (cible, structure, produits pharmaceutiques, mécanisme d'action).
* **Visualisation de graphe interactive** : affichage des entités et relations sous forme de graphe navigable (zoom, déplacement, sélection de nœuds) via `vis-network`.
* **Tableau RDF synchronisé** : liste détaillée des triplets (sujet, prédicat, objet) associée au graphe, mise à jour en temps réel.
* **Filtrage dynamique** : sélection des types d'entités à afficher (mAb, Target, Construct, Product, MOA) par cases à cocher, sans écrire de requête SPARQL.
* **Génération de requêtes SPARQL** : traduction automatique de la recherche utilisateur en requêtes interrogeant l'endpoint IMGT/mAb-KG.
* **Architecture modulaire** : composants Vue.js indépendants et réutilisables, pensés pour une extension future à IMGT-KG.

## Architecture

Le projet est construit avec **Vue.js 3** (Composition API, `<script setup lang="ts">`) et **TypeScript**, pour une interface typée, modulaire et évolutive.

**Stack technique**
* `Vue 3` + `Vue CLI` — framework et outillage front-end
* `TypeScript` — typage statique (triplets RDF, types d'entités, nœuds de graphe)
* `vis-network` — bibliothèque de visualisation de graphes interactifs
* `axios` — requêtes HTTP vers l'endpoint SPARQL IMGT/mAb-KG

**Composants principaux**
* `App.vue` : navigation et agencement général de l'interface
* `SidebarNav.vue` : navigation latérale entre les graphes IMGT/mAb-KG et IMGT-KG
* `SidebarFilters.vue` : filtres dynamiques par type d'entité
* `GraphWorkspace.vue` : composant central — recherche, affichage des résultats et du tableau de données
* `GraphDisplay.vue` : rendu du graphe interactif via `vis-network`
* `HomeCover.vue` : écran d'accueil affiché en l'absence de résultats
* `Fonctions.ts` : fonctions utilitaires centralisant les appels SPARQL, le parsing et le typage des données

**Pipeline de traitement**

```
Saisie utilisateur
      │
      ▼
Génération de la requête SPARQL
      │
      ▼
Envoi à l'endpoint IMGT/mAb-KG
      │
      ▼
Récupération des résultats (CSV)
      │
      ▼
Parsing & typage des triplets
      │
      ▼
Filtrage dynamique par type d'entité
      │
      ▼
Affichage : tableau RDF + graphe interactif
```

## Démonstration Vidéo


[Visionner la vidéo de démonstration](./video/demo_video.mp4demo_video.mp4)

## Ressources

* Rapport de stage : *Développement d'une interface web unifiée pour l'exploitation des graphes de connaissances IMGT-KG en immunogénétique* (M2 Bioinformatique, Université de Montpellier, 2025)
* [IMGT/mAb-KG](https://imgt.org/mAb-KG/)
* [IMGT-KG](https://www.imgt.org/imgt-kg/)

## Utilisation

### Prérequis

* Node.js et npm

### Installation

```
npm install
```

### Lancement en développement

```
npm run serve
```

### Build de production

```
npm run build
```
