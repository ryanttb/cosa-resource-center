# CoSA Resource Center & Data Visualization — Proposal (Working Draft)

**Project:** State and Territorial Archive Data Aggregation — Implementation Grant
**Submitted to:** Council of State Archivists (CoSA) — Joy Banks, Executive Director
**Prepared by:** Ryan Morrison-Westphal (senior software developer) & Jess Farrell (archivist)
**Phase(s) addressed:** Both phases — Phase 1 (Technical Design & Implementation) **and** Phase 2 (Technical Maintenance)
**Status:** Internal working draft — iterate freely. Not yet sent to CoSA.
**Last updated:** 2026-06-04

> **Drafting notes (delete before sending):**
>
> - Funding ceilings are fixed by the grant: **Phase 1 = $100,000**, **Phase 2 = $50,000**, **$150,000 total**.  
> Hard outer boundary: project end date **Nov 30, 2028** (no-cost extension possible).
> - This draft deliberately presents **multiple approaches**. We can collapse to one recommendation after discussing with CoSA.

---

## Table of Contents

1. [At a Glance](#1-at-a-glance)
2. [Motivations & Qualifications](#2-motivations--qualifications)
3. [Our Understanding of the Project](#3-our-understanding-of-the-project)
4. [Guiding Principles](#4-guiding-principles)
5. [Project Approaches (Options A / B / C)](#5-project-approaches-options-a--b--c)
6. [Recommended Architecture](#6-recommended-architecture)
7. [Modern Tooling Rationale (GitHub & AI)](#7-modern-tooling-rationale-github--ai)
8. [Project Plan & Workstreams](#8-project-plan--workstreams)
9. [Timeline](#9-timeline)
10. [Deliverables](#10-deliverables)
11. [Challenges & Risks](#11-challenges--risks)
12. [Requirements Traceability (RFP → Solution)](#12-requirements-traceability-rfp--solution)
13. [Sustainability & Handoff (Phase 2)](#13-sustainability--handoff-phase-2)
14. [Budget Framing](#14-budget-framing)
15. [Open Questions for CoSA](#15-open-questions-for-cosa)
16. [Appendix](#16-appendix)

---

## 1. At a Glance

CoSA needs two things that must outlive this grant:

1. A **Resource Center** — a community hub of linkable, richly-described resources (documents, A/V, datasets, code) with an intuitive, preservation-standards-based taxonomy, integrated with CoSA's GrowthZone/WordPress presence and instrumented for analytics.
2. A **Data Visualization capability** — a web-accessible, submission-friendly data repository that tracks longitudinal survey data, contextualizes provenance, normalizes disparate sources (CoSA, SAA, Census, IMLS), and produces interactive dashboards and standard exports.

The hard constraint behind both is **sustainability**: CoSA does not maintain development staff, and the prior "turnkey" solution proved expensive to feed and maintain. **Every design decision in this proposal is filtered through "can a non-developer keep this alive on a $50,000 Phase 2 budget?"**

Our team pairs a **senior developer** (architecture, automation, data engineering) with a **practicing archivist** (taxonomy, metadata, community needs, governance) — directly matching the RFP's call for a small team fluent in both technology and archives.

We propose a **Git-native, low-maintenance core** with **commercial visualization where it earns its keep**, optionally augmented by **AI-assisted ingestion and natural-language querying** to realize CoSA's "dream scenario" — all introduced incrementally so CoSA is never locked into something it can't sustain.

**We are bidding both phases.** Phase 1 designs and builds the Resource Center and data-visualization tools; Phase 2 hardens them, trains staff and volunteers, and folds routine maintenance into CoSA's existing capacity. Bidding both lets the *same* team carry the work from design through sustainable handoff — exactly the continuity the RFP encourages when it expects the selected parties "to work in tandem for some portion of the project term."

---

## 2. Motivations & Qualifications

### Why we're motivated

Ryan: My career has centered on public-interest data: at Harvard's Berkman Klein Center I built systems that made civic and legal data usable for policymakers, researchers, and the public — the Lumen DMCA-transparency database and the Internet Monitor among them. That work convinced me that member-serving organizations are better served by durable, open, low-cost tooling they control than by rented black boxes that cost money just to keep fed. This project pairs exactly that conviction with archival rigor — and I've seen the cost of getting it wrong: a community-contributed data-and-visualization platform I helped build (Curarium) never shipped, undone by scope creep, and I want to do that idea right this time. The longevity I want for CoSA is the kind I still see in my own open-source work — the jQuery Geo mapping library I wrote over a decade ago is still downloaded weekly in 2026, with no one paying to keep it alive.

> `TODO (Jess)`: 2–4 sentences from the archivist's perspective — stewardship, metadata/taxonomy, serving the state-archives community, making data actionable for advocacy.

### Team & roles


| Role                       | Person           | Primary responsibilities                                                                              |
| -------------------------- | ---------------- | ----------------------------------------------------------------------------------------------------- |
| Technical lead / developer | Ryan Morrison-Westphal | Architecture, data pipelines, automation, dashboards, hosting, security, handoff docs                 |
| Archivist / domain lead    | Jess Farrell | Taxonomy & metadata design, resource curation, community/stakeholder engagement, governance, training |
| Shared                     | Both             | Discovery, requirements, testing with members, reporting & presentations                              |


### Qualifications mapped to RFP requirements (Phase 1)

> Fill the "Evidence" column with concrete projects, roles, tools, and outcomes. Be specific (system names, scale, dates).


| RFP-required qualification                                                        | Who covers it | Evidence (`TODO`) |
| --------------------------------------------------------------------------------- | ------------- | ----------------- |
| Data visualization (Tableau Public, interactive dashboards)                       | Dev (+ Archivist)  | Amazon (2018–2025): built and iterated internal dashboards from cloud & device metrics that drove weekly Ops decisions, bug detection, and prioritization. Berkman Klein: custom visualization for Curarium (collection-metadata search/viz) and the Internet Monitor (multi-source quantitative indicators); authored the open-source jQuery Geo map/spatial-data viz library; built government web-mapping apps at Applied Geographics. Strong custom viz incl. mapping — no Tableau by name, but ready to adopt it (the proposal also supports in-site rendering). |
| Project management of complex projects (nonprofit/academic preferred)             | Both          | Technical/lead developer on complex, multi-year, multi-stakeholder builds at Harvard's Berkman Klein Center (2012–2018; academic/nonprofit research) and Amazon (2018–2025), partnering closely with dedicated PMs and researchers; at Applied Geographics designed and led junior developers on government grant projects, incl. the federally funded National Broadband Map. *(Archivist: add archivist project-coordination evidence.)* |
| Web development & design, SEO, UX                                                 | Dev           | 15+ years full-stack web. At Applied Geographics (2005–2012) personally designed every site and led junior devs task-by-task, delivering government web/mapping apps on MS SQL Server, ASP.NET, and C#.NET (ArcGIS/GDAL) — e.g., a FEMA resource-tracking site, the federal National Broadband Map, a Taunton, MA wetlands-permitting site with automated location-based PDF generation, and public assessor-lookup apps for many municipalities. Berkman Klein (2012–2018) and React/TypeScript work since. SEO was secondary on prior targeted gov/researcher audiences; general working knowledge, ready to deepen it for CoSA. |
| CMS/AMS familiarity; website platform migration                                   | Both          | Deployed and maintained WordPress (CoSA's current stack), Drupal, and WikiMedia instances, plus the Refinery (Ruby) CMS for the Internet Monitor. Hands-on multi-platform CMS operation; GrowthZone specifics to confirm in discovery. *(Archivist: add AMS/community-platform evidence.)* |
| Data management — cleaning, linking datasets, data maps                           | Dev           | Berkman Klein's Lumen Database: high-volume, heavily automated ingestion + SOLR search over DMCA-notice data supplied by Google, Meta, and the U.S. government. Internet Monitor: compiled and curated quantitative data from many sources. At Applied Geographics, built the federal National Broadband Map to normalize provider coverage/pricing data nationwide and converted many municipal assessor databases into public web-mapping apps. Relational (MS SQL Server) & NoSQL throughout. |
| GitHub / similar for hosting & managing resources                                 | Dev           | Active open-source maintainer (GitHub: ryanttb). Authored the open-source jQuery Geo library (2008–2012), still downloaded weekly on NPM in 2026. All Berkman Klein projects — Lumen, Emily Dickinson Archive, Curarium, Internet Monitor — are public GitHub repositories. |
| Strong written/oral communication; clear documentation                            | Both          | Authored extensively documented open-source software (jQuery Geo); ran government customer needs-assessment sessions at Applied Geographics (stakeholder-facing oral communication); translated researcher requirements into deployed systems at Berkman Klein; at Amazon, regularly presented technical designs to senior management for review and approval in formal design-review sessions. *(Archivist: add writing/presentation evidence.)* |
| Stakeholder engagement (board, retirees, volunteers, sponsors, partners)          | Archivist     |                   |
| Focus groups & surveys                                                            | Archivist     |                   |
| Familiarity with archives / government / libraries / museums / records management | Archivist     |                   |
| Eligible to work in the U.S.                                                      | Both          | Yes — both team members are U.S. work-eligible. |
| Able to travel to an in-person gathering                                          | Both          | Yes — both able to travel. |


### Qualifications mapped to RFP requirements (Phase 2)

> We are bidding Phase 2 as well, so we map its required qualifications here too. Fill the "Evidence" column with concrete projects, roles, tools, and outcomes. Be specific (system names, scale, dates).


| RFP-required qualification (Phase 2)                                              | Who covers it | Evidence (`TODO`) |
| --------------------------------------------------------------------------------- | ------------- | ----------------- |
| Strong written/oral communication; clear, concise user documentation             | Both          | Authored extensively documented open-source software (jQuery Geo); ran government customer needs-assessment sessions at Applied Geographics; translated researcher requirements into deployed systems at Berkman Klein; at Amazon, regularly presented technical designs to senior management in formal design-review sessions. *(Archivist: add writing/documentation evidence.)* |
| Developing training materials & instructing diverse audiences                     | Both          | Dev: authored user-facing docs and API guides for open-source software (jQuery Geo) and maintainer runbooks; conducted hands-on customer training/needs sessions at Applied Geographics. *(Archivist: add training-material development & instruction evidence — core archivist/community-facing strength.)* |
| Website maintenance, content management & data visualization tools                | Dev (+ Archivist)  | Deployed and maintained WordPress (CoSA's current stack), Drupal, WikiMedia, and the Refinery (Ruby) CMS over multi-year periods; built and iterated interactive dashboards from cloud/device metrics at Amazon (2018–2025) and custom visualization at Berkman Klein (Curarium, Internet Monitor). Tableau adoption ready (see Phase 1). |
| Cloud-based systems, data management & version control                            | Dev           | Active open-source maintainer on GitHub (ryanttb); version control central to 15+ years of delivery. High-volume, heavily automated ingestion + SOLR search on Berkman Klein's Lumen Database; cloud/device-metric pipelines and dashboards at Amazon; relational (MS SQL Server) & NoSQL throughout. |
| Collecting & integrating user feedback to improve tools                           | Both          | Dev: iterated internal dashboards at Amazon directly from Ops feedback; ran needs-assessment sessions to shape government web apps at Applied Geographics. *(Archivist: add community user-feedback / Calls-to-Members evidence.)* |
| Strong project management; works independently and as part of a team              | Both          | Technical/lead developer on complex, multi-year, multi-stakeholder builds at Harvard's Berkman Klein Center (2012–2018) and Amazon (2018–2025), partnering with dedicated PMs; led junior developers on government grant projects at Applied Geographics, incl. the federally funded National Broadband Map. *(Archivist: add archivist project-coordination evidence.)* |
| Community building & engagement (esp. nonprofit/archival)                         | Archivist     |                   |
| CMS/AMS familiarity; optimizing websites to new platforms                         | Both          | Deployed and maintained WordPress (CoSA's current stack), Drupal, and WikiMedia instances, plus the Refinery (Ruby) CMS for the Internet Monitor; hands-on multi-platform CMS operation; GrowthZone specifics to confirm in discovery. *(Archivist: add AMS/community-platform evidence.)* |
| Stakeholder engagement (board, retirees, volunteers, sponsors, partners)          | Archivist     |                   |
| Familiarity with archives / government / libraries / museums / records management | Archivist     |                   |
| Eligible to work in the U.S.                                                      | Both          | Yes — both team members are U.S. work-eligible. |

---

## 3. Our Understanding of the Project

This grew out of the Mellon-funded planning work, where **AVP** delivered a recommendations report (June 2024) and Tableau Public proofs of concept. Key context we're building on:

- **AMS is already chosen and live:** CoSA migrated to **GrowthZone** (Dec 2024), which also hosts a **WordPress** site. The Resource Center and data tools must *integrate with*, not replace, this stack.
- **AVP's six recommendations** were: (1) adopt an AMS, (2) bring ARM survey data into the AMS, (3) build an interactive member dashboard, (4) migrate resources to a new repository (they floated DSpace), (5) make CoSA data downloadable, and (6) streamline survey/data collection. The RFP explicitly says proposals are **not bound** to AVP's specifics — they're a starting point.
- **A mock-up Resource Center already exists in a GitHub repo** from the prior consultants — evidence that a Git-based approach is viable and already partially familiar to CoSA.
- **The data is messy and partly historical:** surveys dating to the 1990s across Airtable, HigherLogic, Informatics, Sogolytics, SurveyMonkey, and paper. AVP found that (a) members often *can't* do their own data wrangling, (b) cleaned + contextualized data is essential, and (c) visualizations can both reveal and hide data gaps.
- **Representative user needs / FAQs** (from your notes and AVP's use cases):
  - "Do you have a policy on X?" / "Are there guidelines on topic Y?" → Resource Center discovery.
  - "About how much did state Z spend on projects related to A?" → cross-dataset query + visualization.
  - Advocacy/budget comparisons against similar states → the interactive dashboard.
- **The sustainability problem is the real problem.** The previous turnkey product reportedly cost developer time just to add data. CoSA has **no standing dev staff**. So "powerful" is necessary but not sufficient — it must be **operable by archivists and volunteers**.

### What the AVP proof of concept tells us (and how it shapes our plan)

AVP's *Proof of Concept Findings* (May 2024) walked two real use cases end-to-end in Tableau Public. It's the most concrete evidence we have of where the **labor and traps** actually live — and it maps almost one-to-one onto why our automation-first, normalize-once approach is the right call.

**Use Case 1 — "Resources by State."** An archivist looks for states with a similar budget/staffing and a *higher* DPCMM score that have already written and shared digital-preservation policies. AVP combined a CoSA-supplied Resource Center spreadsheet (records tagged to the DPC Access Framework and to states) with the master dataset, then built a choropleth + a linked resource table in a cross-filtering dashboard.

**Use Case 2 — "Budget per capita / budget as % of expenditures."** A state archivist argues for a higher budget by comparing per-capita archives budget and archives budget as a share of total state expenditures against similar states. AVP pulled 2020 population and 2022 expenditure data from the Census Bureau and built bar charts + choropleths.

The **data-massaging effort** AVP documented, by skill level, is the headline:


| Step                                                                                                                                 | What it took                                                                                                                                   | AVP skill rating   | Our take                                                                                                                                                                                          |
| ------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------- | ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Build the **master dataset** (states/territories × region, program, agency, budget, FTE, DPCMM, population, expenditures)            | Manual copy/paste from ARM survey + internal Airtable + DPCMM/region spreadsheets into one Google Sheet; manual value-checking                 | Intermediate       | This canonical reference table is the backbone of *every* cross-dataset question. We make it a **versioned, automatically-assembled deliverable**, not a hand-built one-off.                      |
| ARM survey had **duplicate responses** per state and gaps                                                                            | AVP had to ask CoSA for a hand-cleaned, de-duplicated version                                                                                  | Intermediate       | De-dup + "one row per state per year" resolution becomes a **repeatable pipeline step**, not a favor requested from staff each time.                                                              |
| **Use Case 1 multi-valued fields** (`FrameworkElements`, `State`) had to be split into separate related tables keyed by `ResourceID` | "Complex cross-sheet formulas"                                                                                                                 | **Expert**         | This is the single hardest manual step — and it **disappears** when resources are modeled as structured records from the start. We never "un-spread" data; we capture it relationally up front.   |
| **Census population/expenditure** ingest                                                                                             | Find exact Census terms for territories, transpose columns→rows, multiply "thousands" ×1000, manually paste territory rows                     | Basic–Intermediate | Deterministic transforms → a documented, **re-runnable ingest script**. AVP themselves suggested CoSA pre-normalize and host these — exactly our "normalized external datasets as data products." |
| **Missing-data encoding** in maps                                                                                                    | Gray/white "hacky workaround," manual re-adjustment each year; gaps ambiguous (unreported vs. not-collected-by-source)                         | Intermediate       | We treat **data-availability status** as an explicit field (reported / not-reported / not-collected) so gaps are honest and don't need re-hacking per year.                                       |
| **Geography** problems                                                                                                               | Resources with no state land on "Null Island" (0,0); small states/DC/territories hard to see on a geographic map; filtering on null misbehaves | Intermediate       | A **tile-grid / cartogram** map gives every jurisdiction equal visual weight and a real home for "no geography." See [§6.2](#62-mapping--visualization-tile-grid-maps-with-tableau).              |


**Net effect on the proposal:** no change of direction, but three things get *sharper*:

1. The **"master dataset" becomes a named, first-class deliverable** — a maintained, versioned reference table that powers the dream-scenario "query across datasets" capability.
2. The normalization workstream gets **specific, demonstrable wins** (automated de-dup, structured-from-the-start resources that skip the Expert-level reshape, scripted Census ingest) — strong evidence we'll *reduce* the labor that made the prior turnkey tool expensive.
3. **Missing-data honesty and jurisdiction-fair mapping** move from a vague principle to concrete design commitments.

CoSA (via Joy) is **comfortable with flexibility and iteration**: pivots based on testing and feedback are welcome and can be built into the plan. We treat this as a green light for a **discovery-first, incremental** delivery model rather than a fixed big-bang spec.

---

## 4. Guiding Principles

These principles drive the trade-offs in every option below.

1. **Sustainability over sophistication.** Prefer the simplest thing a non-developer can maintain. Anything fancy must degrade gracefully and have a documented "plain" fallback.
2. **Own the durable layer; rent the convenient layer.** Content, metadata, and data-of-record should live in open, exportable formats CoSA controls (text/CSV/JSON in Git). Use commercial tools (e.g., PowerBI/Tableau) for the parts where buying beats building — but never as the *only* copy of the truth.
3. **No data dead-ends.** Everything can be exported; nothing is trapped in a vendor.
4. **Contribution should be easy and reviewable.** Submissions flow through low-friction front doors (forms) but land in a reviewable, auditable pipeline.
5. **Context travels with data.** Provenance, collection method, data dictionaries, and "known gaps" are first-class, per AVP's findings.
6. **Iterate with real members.** Build, test with a small member cohort, refine — repeatedly.
7. **Document for the next person, not the last.** Handoff/maintenance docs are a deliverable, not an afterthought.

---

## 5. Project Approaches (Options A / B / C)

We present three coherent approaches. They are **not mutually exclusive**; C is a deliberate blend and our tentative recommendation, but we want CoSA's input before committing.

### Option A — Git-native / static-first

**Idea:** The Resource Center is a statically-generated website built from Markdown + structured data files (YAML/CSV/JSON) in a GitHub repository. Each resource is a file → an individual, linkable page with customizable front-matter metadata and taxonomy tags. Data lives as versioned CSV/JSON. Visualizations are embedded from free tools (Tableau Public / Looker Studio) or rendered client-side. Contributions come via web forms and GitHub Pull Requests; GitHub Actions run validation, build, and deploy.


| Pros                                                  | Cons                                                           |
| ----------------------------------------------------- | -------------------------------------------------------------- |
| Near-zero hosting cost; extremely durable             | GitHub literacy required for some maintainer workflows         |
| Full version history & audit trail for free           | Heavier "dynamic query" features need extra tooling            |
| No vendor lock-in; everything is plain files          | Static model less natural for frequently-changing tabular data |
| Plays directly to "migrate Resource Center to GitHub" | Real-time/interactive analytics not native                     |
| Cheap to keep alive in Phase 2                        |                                                                |


### Option B — Commercial platform-centric

**Idea:** Lean on the existing **GrowthZone/WordPress** stack (or a hosted asset repository like **DSpace**, per AVP) for the Resource Center, and a **commercial data platform** (PowerBI, Tableau, or Airtable-as-source-of-truth) for the data repository and visualization. Closest to the literal RFP language ("commercial platform with data visualization and analytics tools available (e.g. PowerBI)").


| Pros                               | Cons                                            |
| ---------------------------------- | ----------------------------------------------- |
| Matches RFP wording most literally | Recurring license costs eat into a finite grant |
| Familiar GUIs for non-developers   | Vendor lock-in; export/portability risk         |
| Less custom code to maintain       | The prior "turnkey" pain lived here             |
| Vendor support contracts available | Customization often hits platform ceilings      |


### Option C — Hybrid (tentative recommendation)

**Idea:** Combine A's **durable, version-controlled core** with B's **commercial visualization where it genuinely helps**, then layer **AI-assisted ingestion and natural-language query** on top — introduced last and optionally, so nothing critical depends on it.

- **Resource Center:** Git-native static site (Option A), integrated into CoSA's WordPress/GrowthZone via embeds/links and a shared taxonomy. Submissions via forms → PR-based review.
- **Data repository:** A lightweight, managed datastore as the *working* source of truth (e.g., a hosted Postgres/Supabase, or Airtable if CoSA prefers GUI editing), with **scheduled exports back to versioned CSV/JSON in Git** so the durable copy is never lost. Supports submissions, longitudinal tracking, and provenance/notes.
- **Visualization:** Free interactive dashboards (Tableau Public / Looker Studio) for the headline member-advocacy use cases, plus standard one-click exports (charts, tables, datasets). Custom lightweight charts only where embedding falls short.
- **AI layer (incremental, optional):** Assisted ingestion (PDF/spreadsheet → structured, human-reviewed data), and a natural-language assistant that answers the FAQ-style questions over the Resource Center + datasets with citations.


| Pros                                                            | Cons                                                     |
| --------------------------------------------------------------- | -------------------------------------------------------- |
| Durable truth in Git + convenient editing/viz on top            | More moving parts than A or B alone                      |
| Realizes the "dream scenario" without betting the project on it | Requires clear boundaries to stay maintainable           |
| Cost-controlled (free/cheap core; paid only where it pays)      | AI features need guardrails (accuracy, cost, governance) |
| Graceful degradation: AI/viz can fail without data loss         |                                                          |


**Why C, tentatively:** It directly answers the sustainability problem (durable, exportable, low-cost core), satisfies the RFP's commercial-viz expectation, builds on the existing GitHub mock-up and GrowthZone investment, and leaves a clear, *optional* path to the AI-powered dream scenario — which we can scope to fit whatever budget and appetite remain.

---

## 6. Recommended Architecture

*(For Option C — illustrative, not a commitment. We expect to refine after discovery.)*

```
                +-------------------------------------------+
                |        Contributors & CoSA Members        |
                |  (archivists, staff, volunteers, funders) |
                +----------------+--------------------------+
                                 |
            Web forms / GitHub  |   Browse / search / query / view dashboards
            (low-friction in)   v   (out)
        +----------------------------------------------------------+
        |                  RESOURCE CENTER (Git-native)            |
        |  Markdown + front-matter metadata, taxonomy, static site |
        |  Integrated with WordPress/GrowthZone via embeds/links   |
        +-------------------+----------------------+---------------+
                            |                      |
            GitHub Actions  |                      |  Analytics (GA / privacy-friendly)
            (validate/build/|                      |
             deploy)        v                      v
        +----------------------------+   +---------------------------+
        |  DATA REPOSITORY (working) |   |   VISUALIZATION LAYER     |
        |  + MASTER DATASET (canon., |-->|  dashboards + maps        |
        |  versioned); submissions,  |   |  (tile-grid via Tableau   |
        |  longitudinal, provenance, |   |  or in-site); standard    |
        |  data-availability status  |   |  charts/tables/exports    |
        +-------------+--------------+   +---------------------------+
                      |  scheduled export
                      v
        +----------------------------+
        |  DURABLE COPY (versioned   |
        |  CSV/JSON in Git)          |   <-- nothing is ever trapped
        +----------------------------+

        +----------------------------------------------------------+
        |     AI LAYER (optional, incremental, human-in-loop)      |
        |  Ingestion assist (PDF/CSV -> structured, reviewed)      |
        |  NL query/assistant over resources + data, with cites   |
        +----------------------------------------------------------+
```

**Key design choices**

- **Single shared taxonomy** (designed by Archivist against digital-preservation standards) used by both the Resource Center and the data repository, so discovery is consistent.
- **Provenance & "known gaps" as required fields**, addressing AVP's warning that visualizations can mislead by hiding missing data.
- **Standard exports are first-class deliverables**, per the RFP ("Standard exports for specific products are essential").
- **Normalization workflows** to map CoSA/SAA/Census/IMLS data into a common schema for comparison (e.g., the state/territory master dataset AVP already prototyped).

### 6.1 The master dataset & normalization (lessons from the POC)

The AVP proof of concept showed that almost every interesting question routes through one canonical table. We make that explicit:

- **Master reference dataset** — one authoritative, versioned row per jurisdiction (50 states + DC + 5 territories), carrying region, program type, agency placement, budget, FTE, DPCMM score, population, and expenditures. It is **assembled by a documented, re-runnable pipeline** from ARM/DPCMM survey exports + internal Airtable + external sources — not hand-stitched in a spreadsheet.
- **De-duplication & longitudinal keys** — ARM responses are resolved to one row per jurisdiction *per survey year*, so "compare to similar states" and "track over time" both work without manual cleanup.
- **Structured-from-the-start resources** — Resource Center records store multi-valued fields (framework elements, related states) as first-class structured data. The POC's *Expert-level* "split multi-valued fields into related tables" step simply doesn't exist for us; exports to Tableau/PowerBI/CSV are generated, not reshaped by hand.
- **Scripted external ingest** — Census population/expenditure pulls become parameterized scripts (correct territory identifiers, unit scaling, column→row shaping) that re-run cleanly each survey cycle, and the normalized results are published as **downloadable data products** in the Resource Center (exactly what AVP recommended).
- **Explicit data-availability status** — every metric carries a status (reported / not reported / not collected by source) so gaps are represented honestly in tables, exports, and maps — directly answering AVP's warning that filters and choropleths can hide or misattribute missing data.

### 6.2 Mapping & visualization: tile-grid maps with Tableau

**Most of the choropleth pain AVP hit is cartographic, not tool-specific** — so the biggest win is a map *style* choice. Our recommendation: **tile-grid maps as the default for jurisdiction-level geography, with Tableau (or a lightweight in-site library) for the tabular charts.** Mapbox was considered and set aside as overkill (see the end of this section).

**The core problem with geographic choropleths here.** CoSA's data is "one value per jurisdiction" for 56 jurisdictions, several of which are tiny or non-contiguous (DC, PR, VI, Guam, American Samoa, CNMI). On a literal U.S. map these are nearly invisible — and AVP's "Null Island," "hard to see small states," and "hacky gray-for-missing" notes are all symptoms of forcing categorical jurisdiction data onto real geography.

**The fix: tile-grid / hex-cartogram maps.** Give every jurisdiction an equal-size cell laid out in a roughly geographic grid. This solves small-state visibility, gives territories and DC a real home, makes "no data" an honest distinct cell instead of a color hack, and reads cleanly at thumbnail size for advocacy one-pagers. This single change addresses the majority of the POC's mapping complaints.

This is an established public-sector practice, not a novel bet. The **CDC's own data-visualization guidance** documents hex-tile maps for exactly the reasons we hit here — equal visibility for the smallest states (what CDC calls the "Alaska effect"), gray "special classes" for `NA`/missing values (our explicit data-availability status, replacing AVP's hand-tuned gray hack), support for simple yes/no policy maps (the "Do you have a policy on X?" use case), and optional per-cell links as a navigation aid to resource pages. See CDC's example and best-practices write-up: [CDC COVE — Hex Map](https://www.cdc.gov/cove/data-visualization-types/hex-map.html).

**Tableau can build tile-grid maps directly.** It doesn't offer them as a built-in map layer, but the well-established technique treats a tile-grid as a **scatter plot**: load a small spatial-crosswalk CSV (fields like `Jurisdiction, Row_X, Column_Y`), plot the coordinates, switch the mark type to a square (or hex shape), and color-code by the metric. Community templates exist (e.g., the "Tableau Hex Tile Map" generator). Practical implications for us:

- We maintain **one tiny, versioned crosswalk file** (jurisdiction → grid row/column) in the repo. It's reusable across every map *and* every tool — Tableau, PowerBI, or lightweight in-site code all consume the same CSV.
- **Missing data** is rendered as an explicit empty/greyed cell we control, not the brittle color workaround AVP had to re-tune each year.
- Members already living in Tableau can reuse the crosswalk, so we meet them where they are while keeping the underlying data portable.

**Tool fit:**


| Need                                                                         | Recommended                                                                      | Notes                                                                   |
| ---------------------------------------------------------------------------- | -------------------------------------------------------------------------------- | ----------------------------------------------------------------------- |
| Jurisdiction maps (resources by state, budget per capita, % of expenditures) | **Tile-grid map** — in Tableau (scatter + crosswalk) or lightweight in-site code | Same crosswalk CSV drives both; honest, controllable missing-data cells |
| Tabular/statistical charts (bars, comparisons, distributions, filters)       | **Tableau** (or PowerBI/Looker)                                                  | Matches RFP/AVP precedent; member-familiar dashboards                   |
| In-site, private/tiered, version-controlled charts & maps                    | **Lightweight libs** (Observable Plot / Plotly / D3 / SVG)                       | Zero per-load cost; data can stay member-only                           |


**Caveat retained:** **Tableau *Public* requires data to be public.** For member-only/tiered datasets we'd use a Tableau license that keeps data private, or render in-site; either way Tableau stays a first-class **export target**, not the system of record.

**Mapbox — considered, not recommended.** We evaluated Mapbox GL JS. It's excellent for *true geospatial* work (zoomable basemaps, point/location layers, coordinate-level density/heat), but CoSA's data is one value per jurisdiction — where Mapbox adds an API token, usage-based pricing, and an external dependency for no real benefit over a tile-grid. We'd only revisit it if a future use case needs genuine map interactivity (e.g., plotting individual facility locations). *(This mapping recommendation comes from hands-on spatial-data experience: government web-mapping applications built at Applied Geographics with ArcGIS/GDAL, and authoring the open-source jQuery Geo mapping library.)*

---

## 7. Modern Tooling Rationale (GitHub & AI)

You floated GitHub and AI agents — here's how we'd justify them to CoSA *without* overpromising.

### GitHub (high confidence, low risk)

- **Migration target the RFP already names** ("migration of CoSA's Resource Center to GitHub or similar"), and a mock-up already lives there.
- **Free version history, audit trail, and rollback** — directly supports Phase 2's "version control" requirement.
- **Submissions & triggers:** web/issue forms and Pull Requests give a reviewable contribution pipeline; **GitHub Actions** automate validation, build, deploy, link-checking, and scheduled data exports — i.e., the system feeds itself instead of requiring paid developer time to "add data."
- **Access control:** roles/branch protections let CoSA gate who can publish vs. propose.
- **Cost:** effectively free for a nonprofit's needs (and GitHub offers nonprofit/education programs).

### AI agents (promising, but staged and guard-railed)

Framed as **assistive, optional, human-in-the-loop** — never an unsupervised authority:

- **Ingestion assist:** turn PDFs/spreadsheets/legacy survey files into draft structured records for a human to approve — attacks the exact pain point ("developer time just to add data").
- **Natural-language query:** answer FAQ-style questions ("Do you have a policy on X?", "Guidelines on Y?", "How much did state Z spend on A?") over the Resource Center + datasets, returning **answers with citations/links** back to source resources and data.
- **Guardrails we'd commit to:** every answer cites sources; AI never writes to the data-of-record without human review; usage costs are capped/monitored; the system fully functions (search, browse, dashboards) even if the AI layer is turned off.
- **Why staged:** accuracy, cost, and governance need validation with real CoSA data before anyone relies on it. We'd pilot it narrowly, measure, then expand only if it earns trust.

> Net: GitHub is core to the recommended build; AI is a high-value **opt-in** capability scoped to remaining budget and CoSA's comfort.

---

## 8. Project Plan & Workstreams

Organized as parallel workstreams delivered incrementally (discovery → build → test-with-members → refine).

1. **Discovery & Governance**
  - Stakeholder interviews/focus groups (Archivist-led); confirm priority users, use cases, and FAQs.
  - Inventory existing resources, datasets, and the prior GitHub mock-up.
  - Define data-governance basics: who curates, who approves, access tiers (public vs. member).
2. **Taxonomy & Metadata**
  - Design a preservation-standards-based, community-intuitive taxonomy and a metadata schema (incl. contributor/usage tracking).
  - Validate with a small member cohort.
3. **Resource Center Build**
  - Implement Git-native site, individual linkable resource pages, search/faceted browse, analytics.
  - Integrate with WordPress/GrowthZone (embeds, links, consistent navigation/branding).
  - Stand up the submission → review → publish pipeline.
4. **Data Repository & Normalization**
  - Stand up the working datastore (submissions, longitudinal tracking, provenance/notes).
  - Build the **master reference dataset** pipeline (jurisdictions × region/program/agency/budget/FTE/DPCMM/population/expenditures), incl. ARM de-duplication and per-year longitudinal keys.
  - Build normalization workflows for CoSA + external sources (SAA, Census, IMLS) into a shared schema; **scripted, re-runnable Census ingest**.
  - Model multi-valued resource fields as structured-from-the-start (no manual reshaping); attach **data-availability status** to every metric.
  - Establish scheduled export to versioned, downloadable datasets (with data dictionaries + context).
  - Reproduce AVP's two use cases ("Resources by State," "Budget per capita / % of expenditures") as an early end-to-end validation of the pipeline.
5. **Visualization & Exports**
  - Build the headline interactive dashboard(s) for advocacy/comparison use cases.
  - Use **tile-grid maps** for jurisdiction metrics (built in Tableau via scatter + a versioned crosswalk CSV, or as lightweight in-site code); honest missing-data cells.
  - Implement standard, repeatable exports (charts, tables, datasets), including Tableau/PowerBI-friendly outputs.
6. **AI Layer (optional/incremental)**
  - Pilot ingestion assist and NL query on a bounded dataset; measure accuracy/cost; expand if validated.
7. **Testing, Feedback & Iteration**
  - Recurring member testing; incorporate feedback; pivot as needed (explicitly welcomed by CoSA).
8. **Documentation, Training & Reporting**
  - Maintainer/runbook docs, user guides, training sessions; interim reports/presentations; final report with maintenance recommendations.

---

## 9. Timeline

**Ballpark only.** Phase 1 is a 24-month engagement (2026–2027); a delayed start (reposting) may shift these. Hard outer bound: **Nov 30, 2028**. Quarters below are relative to project start (T0).

### Phase 1 — Design & Implementation (~24 months, $100,000 ceiling)


| Period | Focus                   | Representative milestones                                                                              |
| ------ | ----------------------- | ------------------------------------------------------------------------------------------------------ |
| Q1     | Discovery & foundations | Stakeholder interviews; inventory; governance v0; taxonomy draft; repo + CI scaffolding                |
| Q2     | Resource Center MVP     | Linkable resource pages, metadata schema, search/browse, submission pipeline; first member test        |
| Q3     | Data repository MVP     | Working datastore, provenance model, first normalized dataset; export-to-Git pipeline                  |
| Q4     | Visualization v1        | First interactive dashboard + standard exports; **interim report/presentation**                        |
| Q5     | Integration & breadth   | WordPress/GrowthZone integration; more datasets normalized; analytics; refine taxonomy from feedback   |
| Q6     | AI pilot (optional)     | Ingestion-assist + NL-query pilot on bounded data; evaluate accuracy/cost/governance                   |
| Q7     | Hardening & docs        | Performance, accessibility, security; maintainer runbooks; user guides; training drafts                |
| Q8     | Handoff readiness       | Member testing round; **final Phase 1 report/presentation**; maintenance recommendations; Phase 2 plan |


### Phase 2 — Maintenance & Sustainability (~24 months, $50,000 ceiling; can overlap)

- Content-update system & schedule; SEO confirmation; data map for AMS elements.
- User-feedback system; finalize resource/taxonomy/architecture migration.
- Tool updates, version-control plan, user documentation, outreach & training.
- Integrate the reimagined **ARM survey** (~2026) and future data into Phase 1 tools.
- Assessment via existing mechanisms (e.g., annual Calls to Members); communication/training plan.
- **Goal:** fold routine maintenance into existing CoSA staff/volunteer roles by grant end.

> **We are bidding both phases.** The RFP expects selected parties to **work in tandem** for part of the term; carrying both phases with one team gives CoSA continuity from design through sustainable handoff. Phase 2 (~2027–2028) overlaps the back half of Phase 1 so maintenance practices are built in, not bolted on.

---

## 10. Deliverables

**Software & systems**

- Resource Center website (Git-native), integrated with CoSA's WordPress/GrowthZone, with:
  - individual linkable resource pages across formats (docs, A/V, datasets, code),
  - customizable metadata + contributor/usage tracking,
  - a preservation-standards-based, intuitive taxonomy,
  - search/faceted browse and usage analytics.
- Data repository (web-accessible) supporting submissions, longitudinal tracking, and provenance/context.
- **Master reference dataset** (canonical, versioned, pipeline-assembled) + **normalized external data products** (e.g., Census population/expenditures) published for download.
- Normalization workflows turning disparate sources (CoSA, SAA, Census, IMLS, etc.) into a comparable, standardized schema, with explicit data-availability status.
- Interactive dashboard(s) and **jurisdiction-fair tile-grid maps** (Tableau or in-site, driven by a shared crosswalk CSV), plus **standard, repeatable exports** (charts, tables, datasets, Tableau/PowerBI-friendly).
- Submission/review/publish + automated build/deploy/validation pipeline (GitHub + Actions).
- *(Optional)* AI ingestion-assist and natural-language query with citations.

**Documentation**

- Architecture & data map; metadata/taxonomy specification; data dictionaries with provenance/known-gaps; maintainer runbooks; user guides.

**Engagement & reporting**

- Interim reports and presentations (as needed).
- Training sessions for CoSA staff/volunteers and the community.
- Final report and presentation with **maintenance/sustainability recommendations**.

---

## 11. Challenges & Risks


| #   | Challenge / Risk                                                                                                                    | Likelihood | Impact   | Mitigation                                                                                                                                                                |
| --- | ----------------------------------------------------------------------------------------------------------------------------------- | ---------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | **Sustainability** — CoSA has no dev staff; prior turnkey tool was costly to feed                                                   | High       | High     | Durable plain-file core; automation does the "adding data"; runbooks; train staff/volunteers; design Phase 2 handoff from day one                                         |
| 2   | **Messy/legacy data** (1990s→present, many tools/formats; ARM duplicate responses; gaps)                                            | High       | Med      | Incremental normalization; automated de-dup + master-dataset pipeline; data dictionaries; provenance + data-availability status as required fields                        |
| 3   | **Misleading visualizations** hiding/misattributing data gaps (AVP warning)                                                         | Med        | High     | Explicit data-availability status; tile-grid maps with honest "no data" cells; distinguish "unreported" vs. "not collected by source"; archivist review before publishing |
| 3b  | **Tableau Public requires public data** — conflicts with member-only/tiered access; hand-built workbooks brittle to re-style yearly | Med        | Med      | Don't make Tableau Public the system of record; render maps as versioned code; offer Tableau/PowerBI as *export* targets, not the source of truth                         |
| 4   | **GitHub literacy** for non-developer maintainers                                                                                   | Med        | Med      | Form-based contribution (no Git needed for most); clear guides; optional light GUI layer                                                                                  |
| 5   | **AI accuracy / hallucination / cost**                                                                                              | Med        | Med–High | Citations required; human-in-the-loop; cost caps; system works fully with AI disabled                                                                                     |
| 6   | **Vendor lock-in / recurring license cost** vs. finite grant                                                                        | Med        | Med      | Own the durable layer in open formats; rent only where it pays; budget licenses explicitly                                                                                |
| 7   | **Integration friction** with WordPress/GrowthZone                                                                                  | Med        | Med      | Early integration spike; embeds/links + shared taxonomy; confirm GrowthZone API/options in discovery                                                                      |
| 8   | **Scope creep** from an open-ended "dream scenario"                                                                                 | Med        | Med      | Phased/optional AI; member-tested priorities; change log; explicit MVP boundaries                                                                                         |
| 9   | **Member engagement/participation** (declining survey response per AVP)                                                             | Med        | Med      | Low-friction submission; show value back via dashboards; Archivist-led community engagement                                                                                    |
| 10  | **Two-person team / bus factor**                                                                                                    | Med        | Med      | Document everything; standard tools; cross-training; clear handoff to CoSA                                                                                                |
| 11  | **Timeline slip** from delayed/reposted start                                                                                       | Med        | Med      | Incremental delivery so value lands early; re-baseline with Joy at kickoff                                                                                                |
| 12  | **Accessibility & privacy** (public sector, tiered data access)                                                                     | Med        | Med–High | WCAG-minded build; access tiers (public vs. member); anonymization options per AVP rec. 6                                                                                 |


---

## 12. Requirements Traceability (RFP → Solution)


| RFP requirement (Phase 1)                                                          | How we address it                                                                |
| ---------------------------------------------------------------------------------- | -------------------------------------------------------------------------------- |
| Individual, linkable pages for varied resource formats                             | Git-native page-per-resource; supports docs/A/V/datasets/code via links + embeds |
| Customizable metadata; contributor/usage tracking                                  | Front-matter metadata schema; contributor + analytics tracking                   |
| Preservation-standards taxonomy, intuitive to community                            | Archivist-designed taxonomy validated with members                                    |
| Integrate with CoSA website & GrowthZone Community                                 | Embeds/links, shared taxonomy/branding; integration spike in discovery           |
| Usage analytics                                                                    | Privacy-friendly web analytics on site + dashboards                              |
| Online data repository, multiple formats                                           | Working datastore + versioned exports                                            |
| Commercial platform w/ viz & analytics (e.g., PowerBI)                             | Tableau Public/Looker (free tier) or PowerBI for dashboards; CoSA's choice       |
| Accept submissions; longitudinal; provenance/context                               | Submission pipeline; longitudinal tracking; provenance/notes as required fields  |
| Recommend efficient collection/export (GrowthZone Forms, Sogolytics, SurveyMonkey) | Collection-strategy recommendations in discovery + reporting                     |
| Standardized workflows across CoSA/SAA/Census/IMLS                                 | Normalization pipelines to a shared schema                                       |
| Export to datasets/graphs/visualizations; standard exports essential               | First-class, repeatable export deliverables                                      |
| Interim & final reports/presentations; maintenance recs                            | Built into reporting cadence + final deliverable                                 |


---

## 13. Sustainability & Handoff (Phase 2)

The core promise: **CoSA can keep this running without us, and without surprise developer bills.**

- **Operability by non-developers:** routine updates happen through forms and reviewable proposals; automation handles validation/build/deploy.
- **Open, exportable everything:** content and data-of-record stay in plain text/CSV/JSON under version control.
- **Runbooks + training:** documented "how to do X" for the common maintenance tasks; live training sessions; recorded walkthroughs.
- **Designed-in handoff:** Phase 2 work (content schedule, feedback loop, ARM-survey integration, version-control plan, outreach/training) maps onto existing CoSA staff/volunteer capacity by grant end.
- **Graceful degradation:** if a paid/AI component is dropped later, search, browse, datasets, and dashboards still work.

---

## 14. Budget Framing

> `TODO`: This is structure only — fill with your rates and effort estimates. Keep totals within ceilings.

- **Phase 1 ceiling:** $100,000. **Phase 2 ceiling:** $50,000. **Combined:** $150,000.
- Suggested cost categories to estimate:
  - Labor (dev + archivist), by workstream/quarter.
  - Tooling/licenses (most core tooling is free; budget any PowerBI/Tableau/hosting/datastore costs explicitly and conservatively).
  - AI usage (if pursued) — capped, monitored, modest.
  - Travel to the in-person gathering (RFP says funding is available — confirm whether it's inside or outside the ceiling).
  - Contingency for re-baselining after the delayed start.
- **Framing message to CoSA:** we intentionally minimize recurring costs so the finite grant buys *building and durability*, not perpetual rent.

---

## 15. Open Questions for CoSA

To turn this draft into a tight, single-approach proposal:

1. What's CoSA's appetite for **GitHub-based workflows** for staff/volunteers vs. preferring a GUI?
2. Preferred **visualization/dashboard surface** — Tableau (note: Tableau *Public* requires public data; a paid license keeps data private), PowerBI, Looker, or in-site code — and any existing licenses? (We plan **tile-grid maps**, buildable in Tableau or in-site; Mapbox was considered but isn't recommended.)
3. What does **GrowthZone** expose (APIs, embedding, community spaces) for integration?
4. How interested is CoSA in the **AI layer**, and what's the comfort level on accuracy/governance/cost?
5. Which **datasets and FAQs** are highest priority for the first dashboard/release?
6. **Access tiers:** what must be public vs. member-only vs. restricted?
7. Confirm the **submission deadline / whether the window is still open**, and the actual project start date for timeline baselining.
8. Is **travel funding** inside or outside the $100K ceiling?
9. Can we get access to the **prior GitHub mock-up** and AVP's POC datasets/Tableau workbooks?

---

## 16. Appendix

### A. Glossary

- **AMS** — Association Management System (CoSA uses **GrowthZone**).
- **ARM Survey** — Archives and Records Management survey (a major CoSA dataset; reimagined iteration expected ~2026).
- **DPCMM** — Digital Preservation Capability Maturity Model (survey/scoring).
- **AVP** — The consultancy that produced the 2024 planning recommendations and Tableau Public POCs.
- **IMLS** — Institute of Museum and Library Services, an independent U.S. federal agency that acts as the primary source of federal funding, policy development, and research for the nation's libraries, museums, and related cultural organizations — including archives.
- **POC** — Proof of Concept.
- **RAG** — Retrieval-Augmented Generation (AI answers grounded in cited source documents/data).
- **SAA** — Society of American Archivists, the oldest and largest national professional association for archivists in North America.
- **Master dataset** — the canonical, versioned reference table (one row per jurisdiction, optionally per year) that joins CoSA and external metrics for cross-dataset analysis.
- **Choropleth** — a map that shades geographic areas by a data value.
- **Tile-grid / cartogram map** — a map that gives each jurisdiction an equal-size cell in a roughly geographic layout, so small states, DC, and territories are equally visible.
- **Spatial crosswalk** — a small lookup file mapping each jurisdiction to a grid position (row/column), used to render tile-grid maps in Tableau (as a scatter plot) or in-site libraries.

### B. Source documents

- `proposals/rfp.pdf` — CoSA Call for Proposals (Phase 1 & Phase 2 scopes, qualifications, funding, timeline).
- `proposals/recommendations.pdf` — AVP "State and Territorial Archive Data Aggregation Planning Project — Recommendations Report" (June 20, 2024).
- `proposals/poc-findings.pdf` — AVP "Proof of Concept Findings" (May 7, 2024): two end-to-end Tableau Public use cases with step-by-step data-prep effort and skill ratings.

### C. External references

- [CDC COVE — Hex Map](https://www.cdc.gov/cove/data-visualization-types/hex-map.html) — U.S. CDC data-visualization standards page describing hex-tile maps and best practices (small-state visibility / the "Alaska effect," gray "special classes" for missing/`NA` values, yes/no policy maps, and per-cell links as a navigation aid). Reference example for the tile-grid mapping approach in [§6.2](#62-mapping--visualization-tile-grid-maps-with-tableau).

### D. AVP's six recommendations (for reference)

1. Adopt an AMS (→ GrowthZone, done Dec 2024).
2. Bring ARM survey data into the AMS.
3. Develop an interactive dashboard for members.
4. Migrate resources to a new repository (DSpace floated).
5. Make CoSA data accessible for download.
6. Streamline survey & data collection methods.

> We are **not bound** to these specifics (the RFP says so), but our approach builds on and complements them.

