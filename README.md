# Interactive Web Interface for IMGT Knowledge Graph Exploration

## Overview

This project was developed during my Master 2 internship in Bioinformatics at **IMGT®, the international ImMunoGeneTics information system®**, in Montpellier, France.

The aim of the internship was to develop a new web interface to facilitate the exploration of IMGT knowledge graphs, in particular **IMGT-KG** and **IMGT/mAb-KG**.

These resources use Semantic Web technologies to represent immunogenetics knowledge as interconnected entities and relationships that can be queried using RDF and SPARQL.

The initial objective was to provide a common approach for exploring both IMGT-KG and IMGT/mAb-KG through a single interface. Within the duration of the internship, development mainly focused on **IMGT/mAb-KG**, the knowledge graph dedicated to therapeutic monoclonal antibodies.

I developed an interface that allows users to search for a monoclonal antibody, query the knowledge graph and explore the retrieved information through two complementary views: an **interactive graph** and an **RDF triple table**.

The application was developed using **Vue.js 3 and TypeScript**, with **SPARQL** for knowledge graph queries and **vis.js / vis-network** for interactive graph visualization.

---

## At a Glance

- **Context:** Master 2 Bioinformatics internship at IMGT
- **Domain:** immunogenetics and therapeutic monoclonal antibodies
- **Main resource:** IMGT/mAb-KG
- **Data model:** RDF knowledge graph
- **Query language:** SPARQL
- **Frontend:** Vue.js 3 + TypeScript
- **Visualization:** vis.js / vis-network
- **Main outputs:** interactive knowledge graph and RDF triple table
- **Main features:** mAb search, graph exploration and dynamic filtering

---

## Context

IMGT is an information system dedicated to immunogenetics and immunoinformatics. It provides databases and resources covering immunoglobulins, T cell receptors, monoclonal antibodies and other immune-related information.

To facilitate the integration and exploration of these data, IMGT has developed resources based on Semantic Web technologies.

### IMGT-KG

**IMGT-KG** is a knowledge graph dedicated to immunogenetics. It connects information from different IMGT resources and relies on IMGT-ONTOLOGY to provide a semantic representation of the data.

### IMGT/mAb-KG

**IMGT/mAb-KG** focuses on therapeutic monoclonal antibodies.

Its data model connects monoclonal antibodies with different types of information, including:

- molecular targets;
- therapeutic indications;
- mechanisms of action;
- molecular constructs;
- pharmaceutical products;
- regulatory information.

These data are represented as interconnected entities and relationships in a knowledge graph.

---

## Problem

At the beginning of the project, IMGT-KG and IMGT/mAb-KG were accessible through interfaces developed with different technologies.

The IMGT-KG interface was based on Vue.js and provided access to SPARQL queries, whereas the IMGT/mAb-KG interface was developed with Streamlit and provided a more visualization-oriented approach.

This created several challenges for knowledge graph exploration:

- users may need to move between different interfaces;
- the two resources provide different user experiences;
- writing SPARQL queries can be difficult for users unfamiliar with Semantic Web technologies;
- interactive exploration of complex graph relationships remained limited.

The project therefore aimed to develop a more intuitive interface for querying and visually exploring these data.

---

## Project Objectives

The interface was designed to:

- search for a monoclonal antibody;
- generate and execute the required SPARQL queries;
- retrieve related information from IMGT/mAb-KG;
- transform query results into data structures that can be used by the application;
- represent biological entities and relationships as an interactive graph;
- display the corresponding RDF triples in a table;
- dynamically filter the information displayed.

The application architecture was also designed with future extension to IMGT-KG in mind.

---

# My Contribution

During the internship, I mainly worked on the design and development of the new web interface.

My contributions included:

- understanding the IMGT/mAb-KG data model;
- exploring relationships between entities in the knowledge graph;
- designing SPARQL queries for the application's use cases;
- developing the frontend with Vue.js 3 and TypeScript;
- retrieving and processing SPARQL query results;
- transforming query results into RDF triples usable by the interface;
- building an interactive graph with vis.js / vis-network;
- developing an RDF table as a complementary representation of the data;
- implementing dynamic filters;
- synchronizing the information displayed in the graph and RDF table.

An important part of the project was making the knowledge graph easier to explore without requiring users to manually write complex SPARQL queries.

---

# Technologies

| Technology | Use |
|---|---|
| **Vue.js 3** | Web interface development |
| **TypeScript** | Application logic and data processing |
| **SPARQL** | Knowledge graph querying |
| **RDF** | Triple-based representation of knowledge |
| **IMGT-ONTOLOGY** | Semantic model used by IMGT resources |
| **vis.js / vis-network** | Interactive graph visualization |
| **HTML / CSS** | Interface structure and styling |

---

# Application Workflow

The main workflow of the application can be summarized as follows:

```text
User
 |
 v
mAb identifier
 |
 v
SPARQL query generation
 |
 v
IMGT/mAb-KG endpoint
 |
 v
Query results
 |
 v
RDF triple processing
 |
 +-------------------------+
 |                         |
 v                         v
Interactive graph       RDF table
 |                         |
 +------------+------------+
              |
              v
       Dynamic filters
```

For the main exploration workflow, the user therefore does not need to manually construct the underlying SPARQL query.

---

# SPARQL Query Centered on a Monoclonal Antibody

An important part of the development was designing a general query able to retrieve different types of information around a selected monoclonal antibody.

Starting from a mAb identifier, the query explores several relationships in IMGT/mAb-KG.

Depending on the available information, it can retrieve data related to:

- the monoclonal antibody itself;
- its target or targets;
- its molecular construct;
- associated products;
- other entities connected to the selected mAb.

The query results are represented as:

```text
subject | relation | object
```

These RDF triples are then processed to build both the graph and the table displayed in the interface.

---

# Developed Interface

The interface provides two complementary representations of the retrieved knowledge.

## Interactive Graph

The graph represents:

- **entities** as nodes;
- **relationships** as edges.

This representation makes it possible to quickly see how a monoclonal antibody is connected to its targets, constructs, products and other biological or clinical information.

Users can interact with the visualization to explore these relationships more easily.

---

## RDF Table

The same information is also displayed as an RDF table:

```text
Subject | Predicate | Object
```

This provides direct access to the triples underlying the graphical representation.

The interactive graph therefore provides an intuitive view of the relationships, while the table gives a more explicit view of the RDF data.

---

# Dynamic Filters

Dynamic filters were implemented to help users focus on specific categories of information.

The filters update both the graph and the RDF table.

## mAb Filter

The user can focus the visualization on information directly associated with the `mAb` level.

This makes it easier to inspect the characteristics of the selected monoclonal antibody and its associated entities.

## Target Filter

The `Target` filter focuses the visualization on molecular targets and the antibodies connected to them.

## Combining Filters

Filters can also be combined.

For example, an `mAb + Target` view makes it possible to focus on the relationship between a selected monoclonal antibody and its molecular target while also identifying other antibodies connected to the same target.

---

# Example: Exploring mAb_781

**mAb_781** was used as one of the examples for testing and illustrating the interface.

Starting from this identifier, the SPARQL query retrieves several types of information associated with the antibody.

The resulting graph can include information related to:

- its identification as a monoclonal antibody;
- its INN information;
- its molecular target;
- its construct;
- therapeutic indications;
- associated pharmaceutical products.

In this example, one of the main targets retrieved is **LAG3**.

Using the `Target` view makes it possible to explore this target and identify other monoclonal antibodies connected to it.

This example illustrates one of the main advantages of graph-based exploration: the user can start from an antibody, move to its target and then discover other entities connected through the same biological relationship.

---

# Visualization Choice

Interactive graph visualization was an important part of the project.

The visualization library needed to support:

- interactive graph exploration;
- dynamic manipulation of nodes and relationships;
- integration with the Vue.js application;
- updates based on query results and user-selected filters.

**vis.js / vis-network** was used to implement the interactive graph visualization.

---

# Development Approach

The project was developed progressively using an iterative approach.

The main steps were:

1. understanding the biological context and IMGT resources;
2. studying Semantic Web concepts, RDF, ontologies and SPARQL;
3. exploring the IMGT-KG and IMGT/mAb-KG data models;
4. reviewing the existing interfaces;
5. identifying relevant user scenarios;
6. designing the SPARQL queries;
7. developing the interface with Vue.js and TypeScript;
8. integrating interactive graph visualization;
9. adding the RDF table;
10. implementing filters and improving interaction with the data.

This approach allowed the different components of the application to be developed and tested progressively.

---

# Technical Challenges

Several technical challenges were encountered during development.

One of the main challenges was transforming SPARQL query results into a structure suitable for interactive visualization.

Knowledge graph results need to be processed so that entities and relationships can be correctly identified and represented in the interface.

Another challenge was maintaining a readable visualization when the query returned many interconnected entities.

Finally, the filters, graph and RDF table needed to remain synchronized so that the different representations displayed consistent information when the user changed the selected filters.

---

# Results

By the end of the internship, a functional interface for exploring **IMGT/mAb-KG** had been developed.

The application supports:

- monoclonal antibody search;
- automatic SPARQL querying;
- retrieval and processing of associated knowledge;
- interactive visualization of relationships;
- RDF triple display;
- filtering by different categories of entities.

Development during the internship focused on IMGT/mAb-KG. However, the components and architecture were designed to facilitate future extension to IMGT-KG.

---

# Limitations and Future Work

The initial objective of the internship was broader than the implementation completed within the available development time.

The current implementation mainly focuses on **IMGT/mAb-KG**. Full integration of IMGT-KG into the new interface therefore remains an important future extension.

Other possible improvements include:

- extending the interface to IMGT-KG;
- supporting more flexible SPARQL exploration;
- improving interactive documentation of entities and relationships;
- improving navigation through large or complex graphs;
- allowing selected views or queries to be shared;
- continuing to improve the overall user experience.

These developments could progressively lead toward a common interface for exploring different IMGT knowledge graphs.

---

# What I Learned

This internship allowed me to work at the intersection of **bioinformatics, biomedical data and web development**.

I strengthened my skills in:

- knowledge graphs;
- RDF and linked data;
- SPARQL;
- biomedical ontologies;
- Vue.js and TypeScript;
- interactive data visualization;
- interface design for complex biological data.

The project also helped me understand the importance of usability and visualization when bioinformatics resources are intended for users who may not be specialists in Semantic Web technologies.

---

# References

The main scientific resources behind this project include:

1. IMGT®, the international ImMunoGeneTics information system®.
2. IMGT-KG, a knowledge graph for integrating and exploring immunogenetics data.
3. IMGT/mAb-KG, a knowledge graph dedicated to therapeutic monoclonal antibodies.
4. RDF and SPARQL Semantic Web standards.

> Full bibliographic references can be added here based on the publications cited in the internship report.

---

# Running the Application

## Requirements

The project requires Node.js and npm.

## Project setup

Install the project dependencies:

```bash
npm install
```

## Development

Compile and run the application with hot reload:

```bash
npm run serve
```

## Production build

Compile and minify the application for production:

```bash
npm run build
```

## Linting

Lint and fix files:

```bash
npm run lint
```

## Author

**Khadidiatou Sall Gueye**

Bioinformatics — Immunogenetics — Knowledge Graphs — SPARQL — Vue.js — Data Visualization
