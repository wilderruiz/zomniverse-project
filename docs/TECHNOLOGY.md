# Zomniverse technology overview

This document gives a **public, employer-facing view of the technologies used in Zomniverse** without exposing the private system architecture, unpublished methods, internal service topology, prompts, routes, infrastructure details, or private research data.

**Snapshot:** 20 September 2026  
**Private source branch measured:** `master`

---

## Technology summary

| Area | Technologies | Used for |
| --- | --- | --- |
| Primary application language | JavaScript / ES modules | Interactive research workflows, validation logic, data handling, user-facing scientific tooling and regression tests |
| Typed frontend/tooling | TypeScript | Typed input-assistance and supporting UI/tooling code |
| Styling | CSS | Responsive research interfaces, scientific workspaces, print/export presentation |
| Server-side web | PHP | Server-side page composition and application configuration |
| Scientific computing | R / Bioconductor | RNA-seq processing, normalization, differential-expression workflows, gene-set analysis and scientific computation |
| Python | Python / FastAPI | Validation, evidence preparation, research utilities and Python-native processing |
| Service/runtime | Node.js / Express | Server-side application services, controlled data processing and integrations |
| Data | MariaDB / SQL | Relational persistence, controlled research metadata and cache-like stores |
| Local AI | Mistral-7B + llama-server | Natural-language assistance and explanation inside AI-assisted research tooling |
| Reproducibility | renv, Conda, Bioconductor | Reproducible scientific environments and package/version control |
| Testing | Node `node:test`, pytest, R tests | Unit, contract and regression testing across languages |

The descriptions above are intentionally **capability-level**, not architectural.

---

## Approximate language footprint

The percentages below are an approximate **tracked first-party source footprint by file size**, calculated from the current private repository.

To avoid a misleading result, obvious third-party bundles and generated/static code assets were excluded from this calculation, including vendored JavaScript bundles and large generated dictionaries. These percentages are **not lines-of-code counts and are not a measure of engineering importance or complexity**.

| Language / source type | Approx. share |
| --- | ---: |
| JavaScript / ES modules | **68.4%** |
| CSS | **18.9%** |
| PHP | **5.5%** |
| Python | **3.1%** |
| R | **1.9%** |
| TypeScript | **1.1%** |
| Shell + PowerShell | **0.6%** |
| HTML | **0.4%** |
| SQL | **0.2%** |

Percentages are rounded.

The repository is therefore primarily a **JavaScript-driven research software project**, with dedicated R, Python, PHP, SQL and TypeScript components supporting scientific computation, validation, persistence, typed tooling and server-side functionality.

---

## JavaScript / Node.js ecosystem

Selected packages currently used in the private project include:

- **Express** — server-side HTTP/application functionality
- **axios** and **node-fetch** — HTTP/data retrieval
- **mysql2** — MariaDB/MySQL access
- **multer** — controlled file-upload handling
- **bcrypt** — password hashing
- **cookie-parser** and **cors** — web request/session support
- **dotenv-flow** — environment configuration
- **PapaParse** — CSV parsing
- **mathjs** — mathematical expression handling
- **rss-parser** — feed parsing
- **sharp** — image processing
- **esbuild** — JavaScript/TypeScript build tooling
- **nodemon** — development runtime reloads
- **TypeScript** — typed source for selected interactive tooling

Zomniverse also uses Node's built-in **`node:test`** and **`node:assert`** APIs extensively for regression and contract testing.

---

## Frontend and scientific presentation

Selected browser-side technologies include:

- **Plotly.js** — interactive scientific visualisation
- **Mermaid** — diagrams and research-document figures
- **Marked** — Markdown rendering
- **PapaParse** — browser-side CSV parsing
- **jQuery** — existing DOM/AJAX functionality
- **html2pdf.js** — document/PDF export support
- **mathjs** — browser-side mathematical evaluation where appropriate
- modern **JavaScript ES modules**
- responsive **CSS**

These packages support presentation and interaction. They do not define the scientific validity of an analysis.

---

## R and Bioconductor

R is used for deterministic scientific and bioinformatics computation.

Selected packages and environments in active use include:

- **edgeR**
- **limma / voom**
- **DESeq2**
- **GSVA**
- **msigdbr**
- **AnnotationDbi**
- **org.Hs.eg.db**
- **data.table**
- **dplyr**
- **readxl**
- **jsonlite**
- **Plumber**
- **reticulate**

Research analysis work also uses packages such as:

- **clusterProfiler**
- **enrichplot**
- **ggplot2**
- **EnhancedVolcano**
- **ComplexHeatmap**

The exact package set can differ between research workflows. Scientific environments are controlled with **renv**, **Conda** and **Bioconductor** rather than being treated as an unversioned global installation.

---

## Python

Python is used where Python-native validation, evidence handling, research utilities or bounded processing are appropriate.

Current public-safe technologies include:

- **FastAPI**
- **pytest**
- Python data-processing and validation code
- Jupyter/JupyterLab in research environments

Python complements the R and JavaScript portions of the project rather than replacing them.

---

## AI / language-model tooling

The currently implemented local language-model option is:

### Mistral-7B

- **Model class:** Mistral-7B
- **Execution:** local inference
- **Serving technology:** `llama-server` / llama.cpp-compatible runtime
- **Role:** natural-language assistance, explanation and conversational support
- **Scientific authority:** none by itself

Zomniverse treats generated model text as **assistive output**. It is not considered a substitute for deterministic analysis, primary scientific literature, validation logic or researcher judgement.

The private project contains governance and validation work around AI-assisted research interactions, but unpublished orchestration, prompts and internal implementation details are intentionally not described in this public repository.

---

## Data and persistence

The project uses:

- **MariaDB**
- **SQL**
- the Node.js **mysql2** client

These technologies support relational persistence and controlled research/application data. Internal schemas, table structures, credentials, storage locations and service boundaries remain private.

---

## Development and reproducibility tooling

The wider development environment includes:

- **Git / GitHub**
- **Node.js / npm**
- **Python**
- **R**
- **Conda**
- **renv**
- **Bioconductor**
- **TypeScript**
- **esbuild**
- **Jupyter / JupyterLab**
- Linux/WSL-based development workflows

The project uses automated tests and explicit validation contracts across JavaScript, Python and R.

---

## Why the source repository remains private

This public technology overview is intended to demonstrate the project's engineering and scientific breadth while keeping the following private until appropriate:

- source architecture and internal component boundaries;
- deployment topology and infrastructure;
- private routes, endpoints and storage layout;
- credentials or operational configuration;
- unpublished algorithms and scientific methods;
- private datasets and collaborator material;
- AI prompts and orchestration logic;
- research components still under validation or publication embargo.

More implementation detail may be released progressively alongside peer-reviewed publications and public reproducibility material.

---

## Short technical profile

**JavaScript / TypeScript · Node.js · Express · PHP · Python · FastAPI · R · Bioconductor · Plumber · MariaDB · SQL · Plotly · edgeR · limma/voom · DESeq2 · GSVA · Git · automated testing · local Mistral-7B / llama.cpp tooling**

