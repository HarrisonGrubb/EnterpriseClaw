# Data Product Roadmap (VDDR) — Strategic Idea Backlog Context

**Last Updated:** 2026-07-02
**Source:** Jira Product Discovery (Polaris) project `VDDR` — [roadmap link](https://vegolution.atlassian.net/jira/polaris/projects/VDDR/ideas/view/13574309). Snapshot of 49 ideas via JQL `project = VDDR`, pulled 2026-07-02.
**Scope:** This file = *candidate ideas / future bets* (discovery). Committed, in-flight execution lives in `data-team-delivery-context.md` (Jira DDT). Ideas graduate into DDT epics when committed.

> ⚠️ **Big caveat — this is a raw backlog, not a triaged roadmap.** All 49 ideas were bulk-created by Harrison Grubb on **2026-06-08**, all sit in stage **"Parking lot,"** all are Priority=Medium, and **none has advanced a stage.** Only 3 of 49 have descriptions; the other 46 are title-only. There are **no Polaris prioritization fields** (no RICE/impact/effort/reach/confidence). **Ideas cannot currently be ranked from the data** — the only differentiating axis is rough ownership. Treat this as a brain-dump of *intent*, useful for knowing what's on the horizon, not as a committed sequence.

---

## What This Tells the Agent

The VDDR board is the **universe of things the data team might build.** Its value as context is:
1. **Directional intent** — the themes VEG's data function cares about (RDVM, pricing, clinical metrics, labor, marketing, platform).
2. **The 1.0 → 2.0 pattern** — several capabilities are explicitly staged (Multi RDVM 1.0/2.0, Multi Customer 1.0/2.0).
3. **The bridge to execution** — when an idea here shows up as a DDT epic, it has been committed.

---

## Ownership Split (the only usable signal)

| Owner | Count | Rough theme |
|---|---|---|
| **Bill Perkins** | 17 | Clinical / pricing / ops data |
| **Harrison Grubb** | 13 | Calls / marketing / RDVM / infra |
| **Unassigned** | 19 | Not yet triaged |

Reporter = Harrison Grubb on all 49. Ownership reads as a workload split, **not** a priority ranking.

---

## Ideas by Theme

*(All stage = Parking lot. Owner in brackets. Titles verbatim. Only VDDR-1/23/56 have descriptions.)*

### Clinical / Medical data & QA (largest cluster, ~9)
- **VDDR-23 — Discharge - HCI** [Bill] — 🎯 *most concrete idea, has a deadline.* New structured discharge flow (HCI) in DogByte replacing the unstructured process; build measurement infra for adoption/completion/clinical outcomes across pilot sites; **VEG-wide go-live October 2026.** → **now committed as DDT epic DDT-16.**
- VDDR-7 — Clinical Data Refresh (leveraging master product and product class) [Bill]
- VDDR-11 — Clinical metrics: Structured metrics and treatment sheet activity [Bill]
- VDDR-12 — Clinical metrics: Disease-based metrics (unstructured data) [Unassigned]
- VDDR-13 — Full MD Audit Tool development (MIT) [Unassigned]
- VDDR-14 — Case templated search tool [Bill]
- VDDR-21 — "Performed by" reporting for ANT [Bill]
- VDDR-22 — Teleconsulting Dashboard [Bill]
- VDDR-26 — Nerd/Nurse Provider in Visits [Bill]
- VDDR-37 — APVN Reporting [Bill]

### Calls / RDVM / Referral funnel (biggest theme by count, ~9)
- VDDR-8 — Enhanced Call Data [Harrison] → committed as DDT epic DDT-56
- VDDR-28 — Simplified Call Logging [Harrison] → committed as DDT epic DDT-73 (SCL)
- VDDR-30 — Email Logging [Bill]
- VDDR-24 — Multi RDVM 1.0 [Harrison] → committed as DDT epic DDT-48
- VDDR-48 — Multi RDVM 2.0 [Bill]
- VDDR-34 — RDVM Call Forwarding Dashboard [Harrison]
- VDDR-35 — Reworking RDVM master for call forwarding [Unassigned]
- VDDR-36 — Finalize and push RDVM Constellation RDVM Master [Harrison]
- VDDR-52 — Enhanced Refer Out LLM Scoring [Harrison]

### Customer / Marketing
- VDDR-25 — Multi Customer 1.0 [Harrison]
- VDDR-49 — Multi Customer 2.0 [Bill]
- VDDR-19 — Enhanced Marketing Analytics [Harrison]
- VDDR-45 — Insource Marketing Data Feeds [Harrison] → committed as DDT epic DDT-40
- VDDR-53 — Braze Expanded Use Case [Harrison]

### Pricing / Finance
- VDDR-9 — Estimate Process (& Doing the value thing) insights [Bill] → committed as DDT epic DDT-78 (EST)
- VDDR-29 — Pricing Analysis and Inquiry [Bill]
- VDDR-46 — Historical and Location Specific Pricing Normalization [Bill]
- VDDR-47 — Serial Pricing [Bill] — *opaque, title-only*
- VDDR-27 — Blackline [Unassigned] — financial close/recon integration
- VDDR-41 — MOI - Rework sales sourcing [Bill]

### People / Talent / Labor
- VDDR-18 — Enhanced People Analytics / Dashboarding [Unassigned]
- VDDR-50 — Talent Funnel Dashboard [Unassigned]
- VDDR-16 — Cultivate data integration and dashboard [Unassigned]
- VDDR-17 — Legion: Data integration for centralized analysis [Unassigned]
- VDDR-57 — Legion: Schedule vs worked to understand call out patterns and scheduling patterns [Unassigned]
- VDDR-58 — Legion Forecast Model Support [Unassigned]

### Operations / CX
- VDDR-10 — In-hospital operational workflows and CX Metrics [Bill]
- VDDR-15 — Constellation - Additional use cases [Unassigned]

### Data Platform / Engineering / Governance
- VDDR-20 — Data Governance Framework & Master Data Roadmap [Unassigned]
- VDDR-43 — Data Documentation & Catalog [Unassigned] → committed as DDT epic DDT-22
- VDDR-54 — Schema Overhaul [Unassigned] → committed as DDT epic DDT-29
- VDDR-31 — Snowflake Vendor Integrations [Harrison] → committed as DDT epic DDT-36
- VDDR-33 — Zapier to Retool Transition [Harrison] — ties to active Zapier→Retool workstream
- VDDR-42 — Rework fact visit combined (`fact_visit_combined`) [Harrison]
- VDDR-51 — Rework Fact Shift (`fact_shift`) [Unassigned]
- VDDR-39 — Reengineering the KPI Flyby [Unassigned]

### AI / Tooling / Visualization
- VDDR-55 — AI Democratizing Data [Unassigned] — ties to the Gen AI leverage priority
- VDDR-56 — Modern data viz and analysis tool search [Unassigned] — *"Understand the job to be done across the field, VQ etc for data analysis and viz tooling perspective to potentially replace tableau"* — evaluate a **Tableau replacement.**

### Not a real idea
- VDDR-1 — "What are archived ideas?" [Unassigned, no priority] — 🚩 **JPD default sample/help card, not a real idea. Should be archived/deleted.**

---

## Domain Terminology & Systems (roadmap-specific)

**Systems / products:** Constellation (RDVM master-data product), Legion (labor scheduling/forecasting), Cultivate (people/engagement), Braze (marketing messaging/CRM), Blackline (financial close/recon), Zapier, Retool, Snowflake, DogByte (clinical/EMR), Tableau (incumbent BI — candidate for replacement), Dialpad (calls, implied).

**Acronyms:** RDVM (referring DVM), HCI (structured discharge flow), MIT / "MD Audit Tool," ANT, APVN, MOI, VQ, DVM. "Nerd/Nurse Provider," "the value thing" (estimate process).

**dbt / warehouse models named:** `fact_visit_combined` (VDDR-42), `fact_shift` (VDDR-51), "KPI Flyby" (VDDR-39), "RDVM Master" / "master product & product class."

---

## Flags — Stale / Incomplete / Contradictory

1. **VDDR-1 is boilerplate** — the JPD "What are archived ideas?" sample card. Skews counts; should be removed.
2. **46 of 49 ideas are title-only** — no descriptions, acceptance criteria, or outcomes. Only VDDR-1/23/56 have bodies.
3. **Zero prioritization metadata** — no impact/effort/RICE fields exist, all Priority=Medium, all one stage. **The board cannot sequence work as-is.** If this is meant to be a real roadmap, JPD custom fields (impact/effort or RICE) need to be added and populated. *(Flag for Harrison — this is the biggest gap.)*
4. **Everything stuck in "Parking lot"** with identical 2026-06-08 bulk-create timestamps — a one-time brain-dump not groomed since.
5. **Overlap / merge candidates to reconcile:**
   - RDVM master: VDDR-35 vs VDDR-36 vs VDDR-24/48 — four overlapping RDVM-master efforts with unclear boundaries.
   - Call/comms logging: VDDR-8 (Enhanced Call Data) / VDDR-28 (Simplified Call Logging) / VDDR-30 (Email Logging).
6. **Opaque without Harrison's context:** VDDR-47 "Serial Pricing," VDDR-9 "Doing the value thing."
7. **Only one dated anchor:** VDDR-23 (HCI) — VEG-wide go-live **October 2026** — likely the most time-sensitive item despite carrying the same Medium/Parking-lot metadata as everything else. Already committed to execution (DDT-16).
8. **Typos in source titles** (corrected here): VDDR-20 "Framewok"→Framework, VDDR-57 "patters"→patterns.
