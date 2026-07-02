# Data & Delivery Team (DDT) — Active Delivery Context

**Last Updated:** 2026-07-02
**Source:** Jira board `DDT` (Data & Delivery Team), board 727 — [link](https://vegolution.atlassian.net/jira/software/projects/DDT/boards/727). Snapshot of 95 issues via JQL `project = DDT`, pulled 2026-07-02.
**Scope:** This file = *committed / in-flight execution*. Strategic candidate ideas live in `data-roadmap-context.md` (Jira VDDR). The two are linked (several DDT epics reference VDDR ideas).

> ⚠️ **Freshness note:** The DDT board is brand-new (created 2026-06-08, ~3.5 weeks old at snapshot). Statuses, assignees, and ticket counts churn weekly — treat specifics below as a point-in-time snapshot, not durable truth. Durable signal = the *workstreams, systems, people, and conventions*, not individual ticket states.

---

## People & Roles

**Board users (assignees/reporters):**

| Person | Role (inferred) | Signal |
|---|---|---|
| **Harrison Grubb** | Director of Solutions & Insights / DPM lead | Reporter on 34 issues; owns marketing insourcing, vendor/ETL selection, auto-doc production, SCL spot-checks |
| **Bill Perkins** | Data Product Management (DPM) | Largest assignee (36 issues); owns HCI DPM tasks, Estimates (EST) discovery/logic, Schema Overhaul epic |
| **Daniel Torres ("Danny")** | Data Engineering (DE) | 24 issues; owns HCI ingestion/data-vault build, EST data foundation, call-data, Fivetran connectors |
| **Brian Lomasky** | Data Engineering / infra & ops | 6 issues; Snowflake↔Google connection, GrowthFactor onboarding, Operational & Maintenance epic |
| **Ivan Gonzalez** | QA (currently on leave) | 1 issue; QA recommendations being covered by others in his absence |

**Stakeholders / SMEs referenced inside tickets (not board users):**

| Person | Relevance |
|---|---|
| **Maureen** | Sign-off authority on HCI must-have metrics |
| **Tori A.** | Estimate/product-mapping findings; validates EST metrics; also a DogByte admin user (see DDT-66) |
| **Mike C.** | Owns payment & "effective-status" heuristics for Estimates; triages Operational epic; ClinOps intros |
| **Song Chen** | DogByte dev-team liaison (raises app bugs; UDF column changes) |
| **Elena B.** | rDVM stakeholder (Multi rDVM) |
| **Mo** | Guide for the estimate-creation process (DPM discovery) |
| **John Goulah** | DogByte / infra (set up Snorkel POC servers) |
| **Jeremy / Tyler** | ClinOps contacts for estimate use-case input (via Mike) |

> **Role tag convention on tickets:** `[DPM]` = Data Product Management, `[DE]` = Data Engineering, `[PM]` = Project Management. These prefixes indicate which function owns the task within a workstream.

---

## Active Workstreams (Epics)

Ordered roughly by activity/priority signal (all tickets are Priority=Medium, so priority is inferred from status/staffing, not the field).

### 1. HCI — Home Care Instructions / Discharge (flagship) — *In Progress*
- **What:** VEG is replacing its unstructured patient-discharge process with a guided **Home Care Instructions (HCI)** flow inside **DogByte**, producing customer-facing PDFs at checkout. Adds a parallel **Direct Transfer** flow for referred cases.
- **Timeline:** Pilot **July–Sept 2026 across ~20 sites**; **VEG-wide go-live Oct 2026.**
- **Source data:** `dogbyte_replicate_fivetran.raw_public` — **14 HCI tables rooted at `visit_hci`**. Legacy backward-compat views in `veg_analysis.reporting` (`v_instructomatic_discharge_kpis`, `v_dogbyte_refer_out_pilot2`).
- **Metrics taxonomy:** `M-01…M-07` and `INT-01…INT-05` (P3 items M-07/INT-04/INT-05 deferred). Maureen signs off must-haves. M-06 ("records closed") needs **Vetrec** methodology alignment.
- 🚩 **Hard blocker:** all 14 tables exist with correct schema but **contain no data until the DogByte build goes live (~July 2026).** Schema gaps: `VISIT_HCI` missing `finalized_at`/`finalized_by` (blocks M-02/M-04; using `hci_updated_at` as proxy). Share-event table (`IOM_HISTORY` or new) unconfirmed (blocks M-02/03/04). DogByte status codes (deceased/no-show/PTS) unconfirmed. **Real risk to the pilot timeline.**
- Related roadmap idea: VDDR-23.

### 2. EST — Estimate Process Insights — *To Do (newly created 2026-07-01)*
- **What:** Data foundation + doctor-level coaching layer. Thesis: *"Estimates are the earliest observable signal of clinical behavior"* — what a doctor offered vs. what the client accepted, upstream of the invoice. Feeds the **MD "baseball-card" coaching workflow in Tableau.**
- **Data window:** January 2025 forward. Metrics only on **ClinOps/Medical-endorsed definitions** (buy-in gates the build).
- **DE build (parallel, "no-regrets"):** simplified estimate **header** table; **unified item** table (union of **7 item tables**, bridged to master product + invoice); visit-level **payment** table using **Mike's heuristic (estimate → visit → first payment after approval)**; daily refresh.
- **First shippable:** inpatient acceptance metric → Tableau v1 → validate with Tori/MD → pilot with select MDs → broad rollout.
- 🚩 Key definitions unconfirmed: "effective decline," doctor attribution (initial provider vs. creator; nurse-created), inclusion/exclusion (STAT/PTS, euthanasia, wildlife), Plan B vs. follow-up. DDT-95 literally carries a "??" on Mike's effective-status logic.
- Related roadmap idea: VDDR-9.

### 3. SCL — Simplified Call Logging — *To Do*
- **What:** DE work for Simplified Call Logging + unified call conversion funnel; integrate the existing **LLM call-tagging** system into the funnel.
- **Backend (feature `VI-590`):** **no new tables** — only new columns on `call_log` and `visit`:
  - `visit.dialpad_session_id` (visits created from a Dialpad call)
  - `call_log.dismissed_at` / `dismissed_by` (soft delete), `transferred_at`, `patient_id`, `transferred_note`, `transferred_to` (`'visit'` or `'patient_profile'`)
  - **`visit.call_session_id` to be removed** (always NULL, obsolete).
- Ties to Harrison's active **Dialpad2 call conversion funnel** workstream.

### 4. Multi rDVM 1.0 — *To Do (all unassigned)*
- **What:** Expand the rDVM (referring/family vet) model in DogByte to support **up to 3 rDVMs per visit**, one designated Family Vet/GP, standardized specialty enumeration. Postgres → Fivetran → dbt (staging → BV → reporting).
- Stakeholders: Harrison G. / Elena B. + Bill Perkins. Target delivery TBD (aligns with DogByte release).
- 🚩 Entire PM/DE task chain (DDT-49–53) unassigned. Related roadmap idea: VDDR-24 (and 2.0 = VDDR-48).

### 5. Auto-Documentation — *In Progress*
- Automated doc generation with human-in-the-loop review; dbt **tags** decide whether a "skill" needs to fire on a code change; automated feed to **Confluence**; system into production.
- Ties to Harrison's **skill-file canonicalization in `dv-dbt`** workstream and the Team → Enterprise Claude migration.

### 6. Marketing Insourcing — *To Do (unassigned epic)*
- Marketing wants to **insource paid search and paid social**: ingest raw → make analysis-ready → blend with visit/sales & search data → replacement dashboard.
- Owned (tasks) by Harrison. Related roadmap idea: VDDR-45.

### 7. Schema Overhaul + Data Documentation & Catalog — *To Do (mostly unassigned)*
- Deploy new warehouse schema, complete docs, **repoint Tableau reports & restructure Tableau folders.**
- Related roadmap ideas: VDDR-54 (schema), VDDR-43 (catalog).

### 8. Enhanced Call Data — *To Do*
- Call Quality + Usage dashboard for MOs and RVPs — explicitly *"without being too big brothery."* LLM model comparison in flight (Llama 3 vs Llama 4 spot check, DDT-60).

### 9. Snowflake Vendor Integrations — *To Do (unassigned epic)*
- Vendor data into Snowflake. In-flight items: **GrowthFactor** onboarding (own DB, exposed versioned tables via their Fivetran; HG still to load master competitor list), **Snorkel** POC (S&I sync from two Postgres RDS → `snorkel_db`, Snowflake LLM model), **Greenhouse** Harvest V3 API OAuth 2.0 re-auth, Snowflake↔Google direct connection, ADM email transition, ETL vendor selection.
- Related roadmap idea: VDDR-31.

### 10. Operational & Maintenance — *In Progress (ongoing bucket)*
- Central tracking for ad-hoc/interrupt/maintenance work. Triaged/assigned via **Brian / Harrison / Mike**; each item needs a rough time estimate.
- **Convention:** project interrupts → filed under the project Epic + `unplanned` label; standalone maintenance → this epic. (Label barely adopted so far — see flags.)

---

## Systems, Schemas & Key Objects

**Stack / vendors:** DogByte (core clinical app / EHR), Snowflake, dbt, Fivetran, Tableau (incumbent BI), Metaplane (data-quality alerting), Vetrec (records vendor), GrowthFactor (competitor / site-viability), Snorkel (external LLM POC team), Greenhouse (ATS / recruiting), Dialpad (calls), Confluence, Jira Polaris/VDDR.

**Schemas / databases referenced:**
| Object | Purpose |
|---|---|
| `dogbyte_replicate_fivetran.raw_public` | Primary HCI source (14 tables rooted at `visit_hci`) |
| `veg_analysis.reporting` | Legacy HCI views (`v_instructomatic_discharge_kpis`, `v_dogbyte_refer_out_pilot2`) |
| `DOGBYTE_REPORTING_DB.REPORTING.DOCTOR_PRODUCTION` | Doctor production report |
| `snorkel_db` | Snowflake destination for the Snorkel POC |

> **Note:** these schema names differ from the `VEG_DWH` / `information_mart` / `bv` / `reporting` schemas in `CLAUDE.md`. The DDT board references source-replica and reporting-app databases (`dogbyte_replicate_fivetran`, `veg_analysis`, `DOGBYTE_REPORTING_DB`) — likely raw/source layers upstream of `VEG_DWH`. Worth confirming the full layering map. ⚠️ *possible inconsistency to reconcile.*

**dbt data-vault layering convention:** `raw stage → prime → sat → sat_latest`. `BV` = Business Vault. `dhw` = data warehouse.

**Tables/columns of note:** `call_log` + `visit` (SCL new columns above); `VISIT_HCI` (missing `finalized_at`/`finalized_by`); `IOM_HISTORY` (candidate HCI share-event table); estimate simplified-header + unified-item (union of 7) + visit-level-payment tables.

---

## Domain Terminology

| Term | Meaning |
|---|---|
| **HCI** | Home Care Instructions — new structured discharge flow in DogByte |
| **EST** | Estimate Process insights workstream |
| **SCL** | Simplified Call Logging workstream |
| **rDVM / RDVM** | Referring DVM (family/general-practice vet who refers a case to VEG) |
| **DPM / DE / PM** | Data Product Mgmt / Data Engineering / Project Mgmt (ticket role tags) |
| **Baseball card** | Per-MD Tableau coaching view (doctor-level performance card) |
| **Direct Transfer** | HCI sub-flow for referred cases |
| **Effective decline / Plan B** | Estimate-logic concepts (client declined; downgrade vs. escalation) |
| **VI-590** | DogByte backend feature enabling Simplified Call Logging |
| **MO / RVP / RVP** | Medical Officer / Regional VP (dashboard audiences) |
| **DoD** | Definition of Done |

---

## Cross-System Relationships

- **DogByte is the system of record**; data flows **DogByte (Postgres) → Fivetran → Snowflake (raw replica) → dbt (raw stage → prime → sat → sat_latest → BV → reporting) → Tableau.**
- **DDT (execution) ↔ VDDR (roadmap):** DDT epics DDT-22, DDT-36, DDT-40, DDT-78 explicitly link to VDDR ideas 43, 31, 45, 9. Roadmap ideas graduate into DDT epics when committed.
- **DPM ↔ DE split:** DPM (Bill, Harrison) defines business logic, requirements, metrics, stakeholder buy-in; DE (Danny, Brian) builds ingestion, data-vault, dashboards. Tickets are tagged accordingly and often paired (`[DPM]` discovery + `[DE]` build in parallel).
- **ClinOps/Medical gate metric definitions** for clinical workstreams (HCI: Maureen; EST: ClinOps endorsement required before build).

---

## Flags — Stale / Incomplete / Contradictory

**Parent/child status contradictions (grooming needed):**
- DDT-5 **Done** but its subtasks DDT-12/13/14 still **To Do** — orphaned subtasks or parent closed early.
- DDT-8 (HCI goals/timelines) **To Do** but its subtasks DDT-9/10/15 are **In Progress** — parent lagging children.
- Auto-doc: DDT-23/25 **Done** ("docs are good," "system discerns when to update") while build tickets DDT-24/26/34/35 still **In Progress** and DDT-27/28 **To Do** — outcome/acceptance tickets closed ahead of the mechanics.

**Incompleteness (governance gaps for a young board):**
- **17 of 95 issues (18%) unassigned**, concentrated in whole workstreams — all Multi rDVM tasks (DDT-49–53), most Schema Overhaul (DDT-30–33), and **three epics** (DDT-22, DDT-36, DDT-40).
- **43 of 95 (45%) have no description** — including In-Progress tickets and **both Ready-For-QA tickets (DDT-59, DDT-60)**, so QA has nothing written to test against.
- **Components unused; labels effectively unused** (`unplanned` on 1 of 95 despite DDT-54 defining it as the interrupt convention). **All priority = Medium** — no prioritization signal in the field.

**Open data-quality / logic items surfaced inside tickets:**
- HCI (DDT-16) is In Progress but **hard-blocked on data + unresolved schema gaps** — risk to the July–Sept pilot.
- GrowthFactor (DDT-38): outstanding HG action to load the master competitor list into Snowflake.
- EST payment/effective-status definitions (DDT-93/95) lean on "Mike's heuristic/logic" with a literal "??" — unconfirmed and they gate the metric build.
- DogByte "Doctor Production" report (DDT-66, Done): non-doctors (incl. Bill P., Tori A., Mike C., Brian L.) attributed to revenue — root-caused as a **DogByte application bug, not a data bug**; raised to DogByte dev via Song Chen.

**Not genuinely stale:** oldest `updated` = 2026-06-09; whole board < 4 weeks old. Closest to "abandoned skeleton" = DDT-30–33 and DDT-42–47 (created mid-June, untouched, empty, unassigned) — worth a triage pass.
