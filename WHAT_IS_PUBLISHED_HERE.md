# What Is Published Here

This repository is the **public Zomniverse project window**.

It is intentionally separate from the private Zomniverse application/source repository. This file defines the public publishing boundary so that human contributors, local agents, remote agents, Codex-style coding agents, and documentation agents can understand what belongs here before proposing or applying changes.

> **Important:** this repository is **review-first**, not an automatic mirror of the private Zomniverse repository.
>
> Material should be added here deliberately after it is suitable for public disclosure.

---

## 1. Purpose of this repository

Use this repository for material that is appropriate to expose publicly about Zomniverse, including:

- public project descriptions;
- research context and scientific scope;
- public technical overviews;
- public development milestones;
- publication records, citations and DOIs;
- selected method summaries after they are ready for disclosure;
- selected screenshots, diagrams and figures;
- reproducibility packages that are approved for release;
- public datasets or example data when licensing and research governance permit;
- selected source components only when there is an explicit decision to release them;
- documentation explaining publicly released Zomniverse capabilities.

This repository is a curated public record of the project rather than a copy of the private product repository.

---

## 2. What is currently published

At present the public repository contains a small reviewed set of materials:

```text
README.md
  Public Zomniverse project overview and research context.

WHAT_IS_PUBLISHED_HERE.md
  This publishing-boundary and agent-guidance document.

docs/TECHNOLOGY.md
  Public overview of languages, packages, scientific stack and AI models.
```

Agents should inspect the repository itself before assuming this list is exhaustive, because additional reviewed public material may be added over time.

---

## 3. What is expected to be published here over time

The repository may progressively gain:

```text
docs/
  public research overviews
  public method documentation
  public technical architecture at an approved level of abstraction
  development milestones
  reproducibility guidance

publications/
  publication index
  citations
  DOIs
  associated notes
  links to papers and public supplementary material

assets/
  selected screenshots
  diagrams
  figures
  public visual material

releases/
  approved reproducibility bundles
  example outputs
  selected public research packages
```

Publication remains progressive and review-led. A planned directory or document is not authorization to expose private source material.

---

## 4. What must remain private unless explicitly approved

Do **not** publish the following merely because it exists in the private Zomniverse repository:

- private application source code;
- unpublished algorithms or experimental methods;
- internal backend implementation details;
- deployment scripts or private infrastructure topology;
- credentials, secrets, tokens, keys or private endpoints;
- private filesystem paths or machine-specific operational details;
- unpublished research datasets;
- internal research workspaces;
- collaborator-restricted or publisher-restricted material;
- private prompts, orchestration logic or research agents;
- unreleased moderation, security or abuse-prevention internals;
- private logs, database records or user information;
- material whose copyright, licence or governance status is unclear.

When uncertain, leave the material out and request review.

---

## 5. Agent operating rule

Agents working in this repository should treat it as an independent public documentation/project repository.

Preferred workflow:

```text
read this file
    ↓
inspect existing public material
    ↓
prepare or edit only material inside the public boundary
    ↓
show/review the change
    ↓
commit or publish after approval
```

Do not assume that a change made in the private Zomniverse repository should automatically be reflected here.

Do not automatically copy files from the private repository into this repository.

Do not infer that an internal feature is public merely because its existence is known.

---

## 6. Relationship to the private Zomniverse repository

The repositories have different responsibilities:

```text
PRIVATE ZOMNIVERSE REPOSITORY
  application source
  private research implementation
  internal architecture
  active experimental work
  private operations

PUBLIC ZOMNIVERSE-PROJECT REPOSITORY
  reviewed project description
  public scientific/technical documentation
  publication-linked material
  approved figures/assets
  approved reproducibility material
```

The public repository should be understandable and useful on its own without reproducing private implementation details.

---

## 7. Review-first publication policy

Automatic publication from the private repository is not the desired default for Zomniverse.

Reasons include:

- scientific work may be unpublished;
- implementation details may contain private architecture;
- research material may require validation before public release;
- collaborator, publisher, licence or governance restrictions may apply;
- public wording may need to differ from internal engineering documentation;
- a private implementation change does not automatically constitute a public scientific update.

Therefore the default policy is:

> **private development first → review → deliberate public publication**

rather than:

> **private change → automatic public mirror**

---

## 8. How agents should propose new public material

When adding something new, agents should make the intended public status obvious.

Useful categories include:

- **Public now** — suitable for immediate publication after ordinary review.
- **Draft for public release** — content may be developed here but still requires owner/research review before being treated as authoritative.
- **Publication-dependent** — should only be added after a named paper, DOI, preprint or other disclosure milestone.
- **Not for this repository** — belongs only in the private Zomniverse repository.

Where scientific claims are involved, link them to the relevant publication, public validation material or reproducibility evidence when available.

---

## 9. What agents may safely update without private-repository synchronization

Subject to normal review, agents can work directly in this repository on:

- wording and structure of existing public documentation;
- public project summaries;
- public technology descriptions;
- public milestone pages;
- publication/citation indexes;
- links to released papers and DOIs;
- public screenshots and captions;
- public diagrams;
- released reproducibility documentation;
- navigation and cross-links between public documents;
- this publishing-boundary file itself.

These changes do not require this repository to be a mirror of the private source repository.

---

## 10. Source of truth

For **public Zomniverse disclosure**, this repository is the source of truth for what has actually been released publicly.

For **private application behavior and implementation**, the private Zomniverse repository remains the source of truth.

If the two differ, do not automatically synchronize them. Determine first whether the difference is intentional public/private separation.

---

## 11. Current publishing principle

Zomniverse follows a **progressive, publication-led, review-first disclosure model**.

The guiding rule for humans and agents is simple:

> **Publish what is ready to be public, not everything that exists privately.**
