# Jira Maturity & Time-Allocation Playbook

**Last Updated:** 2026-07-02
**Source:** Analysis of DDT board (95 issues) + VDDR roadmap (49 ideas), plus the operating context that **both boards are ~2–3 weeks old** (created 2026-06-08).
**Purpose:** A staged path from the current raw stage to a robust, low-human-input operating model for the DPM + data-engineering team. Companion to `data-team-delivery-context.md` and `data-roadmap-context.md`.

---

## Guiding Principles

1. **Don't over-engineer a 2-week-old board.** Most current "gaps" are newness, not neglect. Add process only where it pays for itself now.
2. **Set the substrate while the board is small.** 95 tickets is groomable by hand; 500 isn't. Conventions are cheapest to set today.
3. **Measure, don't guess — but you can't measure yet.** Flow forecasting needs 6–8+ weeks of clean history. Bridge with light estimation now; switch to measured throughput later.
4. **Minimize human input.** Every step below is designed so a human sets a policy once and automation/LLM does the repetitive work. Human role = approve and react, not assemble.
5. **The clock is the asset.** Forecasting quality in Q2 depends on transition history you start accruing *this week*. Sloppy transitions now = a lost quarter of forecasting data.

---

## The Maturity Ladder

| Phase | Timeline | What you do | What you get | Human input |
|---|---|---|---|---|
| **0 — Raw** *(you are here)* | Weeks 0–2 | Capture work as tickets | A backlog exists | — |
| **1 — Substrate** | Weeks 2–6 | Set minimal conventions + enforce via automation; start clean transitions | Consistent metadata; clean status history begins accruing | One-time setup |
| **2 — Bridge** | Weeks 6–14 (~1 quarter) | Reference-class epic sizing; theme budgets; accumulate flow data | A committable roadmap + rough delivery dates | ~30 min/quarter |
| **3 — Robust** | Quarter 2+ | Throughput/Monte-Carlo forecasting; auto-drafted retro | Confidence-interval delivery forecasts; actionable retros | React to findings only |

Do not skip phases. Phase 3 forecasting is impossible without the clean history that Phase 1 starts collecting.

---

## Phase 1 — Substrate (Weeks 2–6)

**Goal:** stop the metadata problem from getting worse while the board is still small, and begin accruing clean status history. Minimal field set — resist adding more.

### Minimal fields to add now
| Field | Applies to | Why | Input cost |
|---|---|---|---|
| **Description** (required to leave To Do) | All | QA/DE can't act on blank tickets (45% are blank today) | LLM-drafted, human-reviewed |
| **Assignee** (required to leave To Do) | All | 18% unassigned incl. 3 epics | Trivial |
| **T-shirt size** (S/M/L/XL) | Epics only | Bridge estimation for Phase 2 | ~1 min/epic |
| **Component** | All | Slice by domain/system (currently 0 used) | Pick from a fixed list |
| **"Blocked" status + one-word reason** | All | The single highest-value field for retros | Set on transition |

### Workflow hygiene
- **Enforce parent-before-child close** (or at least alert): fixes the DDT-5 / DDT-8 contradictions.
- **Stop writing outcome statements as tickets.** "Auto-doc creates good docs" is acceptance criteria, not a work item — move these to the epic's DoD. This is the root cause of the backwards status flow.
- **Set WIP limits** if running Kanban. 18 In Progress ÷ ~3 engineers ≈ 6 each is too high; target ~2–3 WIP per person.

### Automation to build (Automation for Jira — zero input after setup)
- Flag/block any ticket leaving To Do without description + assignee + estimate.
- Auto-apply `unplanned` label + parent epic on mid-cycle creation (revives the DDT-54 convention).
- Auto-create VDDR idea ↔ DDT epic delivery link when an epic references a VDDR key.

### Component starter list (fixed, so it's actually used)
`HCI` · `Estimates` · `Call/RDVM` · `Marketing` · `Pricing/Finance` · `People/Labor` · `Platform/Schema` · `Vendor-Integrations` · `Ops-Maintenance`

---

## Phase 2 — Bridge (Weeks 6–14)

**Goal:** produce a committable roadmap and rough delivery dates *while* accruing the flow data that Phase 3 needs. Two allocation horizons run in parallel.

### A. Top-down roadmap allocation (VDDR) — capacity budgeting

**Step 1 — Committable capacity (one number/quarter):**
```
~3 engineers × 12 weeks              = 36 raw eng-weeks
× 0.7 overhead (meetings/ops/switch) ≈ 25 eng-weeks
− 20% Operational/interrupt reserve  ≈ 20 committable eng-weeks
```
*(Overhead factor and reserve % are assumptions — Harrison to confirm.)*

**Step 2 — Theme budget (five numbers/quarter):** allocate the ~20 eng-weeks across roadmap clusters by strategic weight, e.g. Clinical 30% / RDVM 25% / Pricing 20% / Platform 15% / Marketing 10%. This is the only real human judgment in the loop.

**Step 3 — LLM T-shirt sizing:** batch each idea's title+description → Claude → S/M/L/XL → eng-weeks. Human reviews outliers only (~30 min).

**Step 4 — Now/Next/Later falls out:** fit sized ideas into each theme's eng-week budget; what fits = Now, remainder = Next/Later. No per-idea prioritization meeting.

> **Prerequisite:** VDDR needs real JPD fields (impact/effort) and an idea funnel first — see Phase 1 substrate. Without them the sizing has nowhere to live.

### B. Bottom-up active-board allocation (DDT) — the no-history bridge

**Now → ~week 8 (no history): reference-class sizing.** Size each *epic* once (S/M/L/XL); map to eng-weeks from gut + your one real data point (HCI). Placeholder precision — buys a schedule while data accrues. **Do not estimate every subtask.**

**~Week 8+ (history exists): throughput forecasting.** Once you know "we close ~X tickets/week," forecast any epic as `remaining tickets ÷ throughput`. Zero per-ticket estimation. This fits the spec-and-hand-off model — the question is "when is epic Y done," not "points per ticket."

> **Consistency caveat:** throughput forecasting assumes roughly similar ticket sizes. Keep subtasks in a comparable size band (split anything >1 week) so ticket-count is a fair proxy for effort.

---

## Phase 3 — Robust (Quarter 2+)

**Goal:** confidence-interval forecasts and near-zero-input retros.

### Monte Carlo forecasting
Run historical throughput as a distribution → "epic Y ships Aug 12–Sep 3, 80% confidence." Use Jira's native flow charts, or a small script off the Jira API / a Snowflake export of the issue-change log.

### Auto-drafted quarterly retro
Once Phase 1 (clean transitions + blocked-reason) + Phase 2 (history) exist, the retro is largely generated:
- Pull cycle time, time-in-status, blocked-duration, throughput by theme via the Jira API.
- Claude drafts the narrative: bottleneck columns, most-blocked epics, blocker reasons ranked, throughput trend.
- Human input = react to findings, not assemble them.

**Retro value today vs. after Phase 1–2:**
| | Today (raw) | After Phase 1–2 |
|---|---|---|
| Blockers | Anecdotal ("HCI felt slow") | Quantified ("blocked 6 wks on upstream data; hit 3 of 5 epics") |
| Bottlenecks | Invisible | Time-in-status per column |
| Estimate accuracy | N/A (no estimates) | Forecast vs. actual |
| Effort to produce | Manual, low signal | Auto-drafted, high signal |

The single highest-leverage retro enabler is the **Blocked status + one-word reason** (Phase 1). That one field turns "it felt slow" into countable, sliceable blocker data.

---

## Minimal-Human-Input Automation Map

| Phase | Automated | Human does |
|---|---|---|
| 1 | Required-field enforcement; unplanned tagging; idea↔epic linking | Set policies once |
| 2 | LLM idea sizing; description backfill drafts | Set theme budget; review outliers |
| 3 | Throughput/Monte-Carlo forecast; retro draft | React to findings |

---

## Do This Week (checklist)

- [ ] Decide estimation destination: **flow metrics** (recommended, minimal input) vs. story points — it sets which fields to add.
- [ ] Add the minimal field set (description, assignee, epic T-shirt size, component, Blocked+reason).
- [ ] Turn on the required-field automation rule so transitions are clean *from now* (protects Q2 forecasting data).
- [ ] Convert outcome-statement tickets (e.g. DDT-23/25 style) into epic DoD, not work items.
- [ ] Set WIP limits (~2–3/person) if Kanban.
- [ ] Archive VDDR-1 (JPD sample card).
- [ ] Confirm the two capacity assumptions (overhead factor, interrupt reserve %).

---

## Open Decisions / Forks (for Harrison)

1. **Estimation model:** flow metrics (recommended — near-zero ongoing input, needs clean transitions) vs. story points (enables sprint commitment, ongoing estimation cost). Changes the Phase 1 field set.
2. **Kanban vs. Scrum:** current columns imply Kanban → needs WIP limits. If sprints are wanted, needs sprint config + points.
3. **Capacity assumptions:** confirm overhead factor (0.7?) and interrupt reserve (20%?).
4. **Where forecasting lives:** Jira-native charts vs. Snowflake export + custom (more control, ties to existing stack).
5. **Theme budget split:** the five percentages are a strategic call only Harrison can make.
