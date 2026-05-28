# Context Curation Plan v1 — EnterpriseClaw Strategic Advisor

**Owner:** Harrison (Director of Solutions and Insights, VEG)
**Date:** 2026-05-28
**Scope:** Knowledge to load into the Strategic Advisor agent over the next ~8 weeks

---

## 1. Domain Inventory

| # | Domain / Work Area | Type | Current state |
|---|---|---|---|
| 1 | Data product specification (spec → handoff lifecycle) | Process | Partial — currently being built |
| 2 | Data quality & testing (dbt taxonomies, shift-left QA) | Process | Partial — skill exists, weak on business-logic validation |
| 3 | Stakeholder analytics support | Process | Tribal |
| 4 | Active workstreams (RDVM, Dialpad2, GrowthFactor, AI label QA, dv-dbt canonicalization) | Project | Partial — only Dialpad has docs (Google Drive + Confluence) |
| 5 | Tooling & AI infrastructure (skill files, MCP, Team → Enterprise migration) | Project + Reference | Partial |
| 6 | DPM function build-out | Project + Policy | Gap — not yet started |
| 7 | VEG cultural reality (anti-quantitative, short attention, hospital empathy) | Reference | Tribal (Harrison) — captured as one-liner in CLAUDE.md, not standalone |
| 8 | Data skepticism principle (Boston doorbell pattern) | Reference | Tribal — top capture priority |
| 9 | Vet industry & emergency sector landscape | Reference | Gap — mostly private companies, hard to source |
| 10 | Internal collaborator graph (dv-dbt contributors) | Relationship | Discoverable from repo activity |

---

## 2. Knowledge Matrix (top 6)

| Domain | Documented | Tribal | Gap |
|---|---|---|---|
| Spec lifecycle | None — being built | Harrison + DE manager | Whole lifecycle: intake → template → acceptance criteria → handoff |
| Data quality & testing | `dv-dbt` skill (strong on factual, weak on join, **bad on business logic**) | Harrison + DE engineers | Business logic validation patterns |
| Active workstreams | Dialpad only (Drive + Confluence) | Harrison + 1 engineer/workstream | Strategic "why" + success criteria per workstream |
| DPM build-out | Nothing | Harrison | Charter, operating model, values, intake, success metrics |
| VEG cultural reality | None | Harrison | Captured as one-liner in CLAUDE.md — no standalone doc needed |
| Data skepticism | None | Harrison + Boston investigators | Short playbook with case studies |

**Cross-cutting (treat as in-scope):**
- Stakeholder communication styles → captured per-draft, not upfront
- VEG approval process for new tools → deferred until AI policy lands

---

## 3. Prioritized Capture Plan

| Pri | Knowledge area | Why it matters for the advisor | Effort | Method | Owner | Timeline |
|---|---|---|---|---|---|---|
| 1 | Business logic validation patterns | Reshapes how I help design tests, spec acceptance criteria, review outputs | Medium-High | Dedicated session + mine `dv-dbt` + DE engineer review | Harrison + DE engineer | 3–4 wks |
| 2 | Data skepticism playbook | Stops me blessing SQL-right-but-data-wrong findings | Low-Medium | Quick write-up | Harrison | 1–2 wks ← **QUICK WIN** |
| 3 | Spec lifecycle | I should help shape this in real time, not retrofit it | High | Document-as-you-go | Harrison + DE manager | 4–8 wks |
| 4 | VEG AI initiative landscape | Can't help prioritize AI initiatives without baseline of what's tried/running | Medium | Quick write-up + Jira mining | Harrison | 2–3 wks |
| 5 | DPM operating model artifacts | Can't push back on operating-model decisions without a baseline | Medium-High | Document-as-you-go + 1–2 focused sessions | Harrison | 6–8 wks |

---

## 4. Quick Win — Next 7 Days

**Deliverable:** `context/data-skepticism.md` — operating principle + 3 VEG case studies + skepticism checklist
**Owner:** Harrison (no external dependencies)
**Deadline:** 2026-06-04
**Effort estimate:** ~1 hour

---

## 5. Gaps I Can't Fill Alone

These require other people's input, external research, or org events to resolve:

- **Business logic validation patterns (#1 priority)** — needs a DE engineer's brain alongside Harrison's
- **Spec lifecycle (#3 priority)** — needs the DE manager's input and real intake cases to capture as-they-happen
- **VEG AI tools policy** — TBD organization-wide; can't capture until org decides
- **Compliance posture beyond HIPAA** (SOC2, state-level pet medical records) — needs legal/compliance input
- **Vet industry & emergency sector landscape** — mostly private companies; needs research time + maybe a vendor briefing
- **dv-dbt collaborator graph** — discoverable from repo activity; flag for capture as I observe over sessions
- **Preferred spec handoff format** (Jira / Notion / Markdown) — pending team decision

---

## Process notes for the advisor

- **Pattern detection → propose → Harrison validates.** When the same theme appears across multiple sessions, surface it at session-wrap as a candidate update to CLAUDE.md / context files. Never silently update.
- **Document-as-you-go** items get captured in session logs first, then promoted to `context/` once stable.
- **Definition of done for any capture:** Harrison has reviewed, understands the framing, and weighed in on what's missing.
