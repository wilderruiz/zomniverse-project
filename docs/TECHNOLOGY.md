# Zomniverse technology overview

This is a public, employer-facing summary of the technologies used in Zomniverse. It describes responsibilities and capabilities without exposing private architecture, service topology, endpoints, infrastructure, prompts, unpublished methods, or research data.

**Reviewed:** 9 October 2026

## Technology summary

| Area | Technologies | Used for |
| --- | --- | --- |
| Browser application | JavaScript / ES modules, selected TypeScript | Interactive research workflows, validation, data handling, contextual assistance, and regression tests |
| Presentation | CSS, HTML, PHP-rendered pages | Responsive scientific workspaces, accessible controls, page composition, and print/export presentation |
| Application services | Node.js, Express | Controlled orchestration, lifecycle operations, governed data access, and integrations |
| Scientific computing | R, Bioconductor, Plumber | Deterministic RNA-seq processing, normalization, quality control, and bioinformatics computation |
| Validation and research utilities | Python, FastAPI | Bounded validation, evidence preparation, and Python-native processing |
| Persistence | MariaDB / SQL | Relational application state, ownership, lifecycle, and governed metadata |
| Local AI | Mistral-7B, llama.cpp-compatible serving | Natural-language assistance, evidence synthesis, explanation, and coding support |
| Visualisation and documents | Plotly.js, Mermaid, Markdown, document-export tooling | Scientific figures, diagrams, evidence presentation, and reviewable reports |
| Reproducibility | Git, renv, Conda, Bioconductor environments | Version control and reproducible language/package environments |
| Testing | `node:test`, pytest, R tests, browser/manual checks | Unit, contract, regression, integration, lifecycle, accessibility, and responsive testing |

## JavaScript and Node.js

JavaScript is the principal application language. Browser ES modules implement research interactions and validation feedback; Node.js provides private application services and controlled integrations.

Selected packages in active use include:

- **Express** for server-side application services;
- **axios** and **node-fetch** for controlled HTTP/data retrieval;
- **mysql2** for MariaDB access;
- **multer** for bounded upload handling;
- **bcrypt**, **cookie-parser**, and **cors** for web application support;
- **dotenv-flow** for environment-based configuration;
- **PapaParse** for tabular parsing;
- **mathjs** for bounded mathematical processing;
- **rss-parser** for feed handling;
- **sharp** for image processing;
- **esbuild** and **TypeScript** for selected typed tooling and builds;
- Node's built-in **`node:test`** and **`node:assert`** for contract and regression suites.

Package names demonstrate the technical stack; they do not describe private routes, data models, or service boundaries.

## Frontend and scientific presentation

Selected browser-side technologies include:

- **Plotly.js** for interactive scientific visualisation;
- **Mermaid** for diagrams;
- **Marked** for Markdown rendering;
- **PapaParse** for browser-side tabular parsing;
- **jQuery** for established DOM and request functionality;
- **html2pdf.js** for document export;
- **mathjs** for appropriate browser-side calculations;
- modern JavaScript modules and responsive CSS.

The interface layer includes responsive and fullscreen workspaces, keyboard-operable controls, visible focus, confirmation flows, contextual result panels, lazy presentation of long collections, and theme-aware scientific views. These technologies support interaction and communication; they do not establish scientific validity by themselves.

## R and Bioconductor

R is used for deterministic scientific computation. The controlled environment includes packages such as:

- **edgeR**;
- **limma / voom**;
- **DESeq2**;
- **GSVA**;
- **msigdbr**;
- **AnnotationDbi** and **org.Hs.eg.db**;
- **data.table**, **dplyr**, and **readxl**;
- **jsonlite** and **Plumber**;
- **clusterProfiler**, **enrichplot**, **ggplot2**, **EnhancedVolcano**, and **ComplexHeatmap** in research-analysis contexts.

The exact set varies by workflow. R code owns quantitative transformations where deterministic bioinformatics authority is required; language-model output does not replace those calculations.

## Python

Python and **FastAPI** provide bounded validation, evidence preparation, and Python-native processing. **pytest** supports Python verification, and Jupyter/JupyterLab can be used in research environments.

Python complements rather than replaces the R and JavaScript portions of the project. Some Python boundaries are prepared for staged expansion, so public descriptions distinguish implemented processing from future work.

## AI and local acceleration

Zomniverse supports a local **Mistral-7B** model through a llama.cpp-compatible runtime. Its roles include natural-language assistance, evidence-oriented synthesis, explanation, and code-oriented support.

Engineering around AI includes provider readiness checks, task-specific response governance, structured result presentation, bounded context and output handling, and failure paths that preserve retrieved evidence or computed results. Development can use local GPU acceleration when available; safe non-GPU or environment-appropriate fallbacks remain part of resilience planning.

Generated text is assistive output. It is not a substitute for deterministic analysis, primary literature, validation logic, or researcher judgement. Private prompts, routing policy, agent implementation, and orchestration remain unpublished.

## Data, provenance, and reproducibility

The project uses **MariaDB**, **SQL**, and the Node.js **mysql2** client for controlled relational persistence. Public documentation does not expose schemas, credentials, storage locations, or private data relationships.

Reproducibility mechanisms include:

- Git-based restore points and version history;
- **renv**, Conda, and Bioconductor environment control;
- stable artifact identity and checksums where required;
- explicit candidate/review/commit workflow stages;
- provenance-aware datasets and evidence records;
- release/status documentation and regression tests.

## Validation and observability

Verification spans JavaScript, Python, and R. The suite includes unit, contract, cross-layer, lifecycle, responsive-layout, accessibility, state-management, and scientific-workflow tests. Health/readiness checks and bounded operational diagnostics help distinguish provider or connectivity failures from scientific outcomes.

Observability is intentionally constrained: diagnostics should help explain system state without exposing private data, secrets, prompts, or internal implementation to the public interface.

## Development tooling

The wider development environment includes:

- Git and GitHub;
- Node.js and npm;
- Python and pytest;
- R, renv, and Bioconductor;
- Conda;
- TypeScript and esbuild;
- Jupyter/JupyterLab;
- Linux/WSL-oriented development and test workflows.

Bounded agent-development workspaces keep subsystem context, evidence, tests, and current-state notes close to the relevant code without copying production source or expanding every task to the whole repository.

## Disclosure boundary

The private source repository contains substantially more implementation detail than is appropriate for this public project. Internal component structure, deployment topology, routes, service addresses, database schema, security implementation, model configuration, prompts, unpublished methods, private datasets, and collaborator material are intentionally omitted.

See the [engineering overview](ENGINEERING_OVERVIEW.md) for capability-level design and [publication policy](../WHAT_IS_PUBLISHED_HERE.md) for the governing disclosure rules.

## Short technical profile

**JavaScript · TypeScript · Node.js · Express · PHP · Python · FastAPI · R · Bioconductor · Plumber · MariaDB · SQL · Plotly.js · edgeR · limma/voom · DESeq2 · GSVA · Git · automated testing · local Mistral-7B / llama.cpp tooling**
