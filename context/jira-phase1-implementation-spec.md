# Jira Phase 1 — Implementation Spec (Kanban + Hybrid Estimation)

**Last Updated:** 2026-07-02
**Source:** Decisions confirmed by Harrison 2026-07-02 — **Kanban** board mode; **Hybrid** estimation (epic T-shirt sizing for quarterly roadmap capacity + flow metrics for active-board delivery). Builds on `jira-maturity-playbook.md`.
**Audience:** Jira admin (spec-and-hand-off). Markdown format by default — *confirm preferred handoff format (Jira epic + subtasks? Confluence? this doc?) before implementation.*
**Goal:** Set the minimal substrate so status history is clean from now on, with the least ongoing human input.

> ⏱ **Time-sensitive:** the required-field validator (§3.1) is the one item that should ship first. Every week it's off is a week of un-forecastable transition history.

---

## 1. DDT Board — Field Schema (active delivery)

Because delivery uses **flow metrics, there is no story-point field.** Effort is measured, not estimated. The only sizing field is epic-level (for the hybrid roadmap tie-in).

| Field | Type | Applies to | Required when | Notes |
|---|---|---|---|---|
| **Description** | Text | All | Leaving To Do | LLM-drafted, human-reviewed; backfill the 43 blanks |
| **Assignee** | User | All | Leaving To Do | — |
| **Component** | Single-select | All | Leaving To Do | Fixed list, §4 |
| **Epic Size** | Single-select (S/M/L/XL) | **Epics only** | On epic creation | Feeds quarterly capacity, §5. Not on tasks/subtasks |
| **Blocked Reason** | Single-select | All | Entering Blocked | Fixed list, §4 — the key retro enabler |

**No fields added:** story points, original/remaining estimate, sprint. Keep it lean — flow metrics don't need them.

**Ticket-size discipline (not a field, a rule):** for throughput forecasting to be fair, keep tasks/subtasks in a comparable band. **Split anything expected to exceed ~1 week.** This keeps ticket-count a valid proxy for effort.

---

## 2. DDT Board — Kanban Configuration

**Columns (status → column):**
`Backlog` (To Do) → `In Progress` → `Blocked` → `Ready for QA` → `Done`

- **Add a `Blocked` status** (not just a flag) so time-in-blocked is measurable from status history. Transition into it requires Blocked Reason (§3.4).
- **WIP limits** (you're at ~6/person; target 2–3):
  | Column | Limit (~3 engineers) |
  |---|---|
  | In Progress | 8 |
  | Ready for QA | 4 |
  - Blocked is uncapped (blocked work shouldn't count against pull capacity) but should trigger a nudge (§3.5).
- **Sub-filter:** board should show Task + Subtask + Epic; ensure the board query is `project = DDT` with swimlanes by Epic (makes epic-level flow visible for forecasting).

---

## 3. Automation for Jira — Rules

Jira mechanics note: **hard gates = workflow validators** (block the transition); **nudges/side-effects = automation rules**. Spec'd accordingly.

### 3.1 Required-field gate *(workflow validator — ship first)*
- **Where:** validator on the `To Do → In Progress` transition.
- **Condition:** `description` is not empty AND `assignee` is set AND `component` is set.
- **Effect:** blocks the transition with a message: *"Add description, assignee, and component before starting."*
- **Why validator not automation:** a validator prevents the dirty transition; an automation can only revert it after the fact (leaves a messy history — the exact thing we're protecting).

### 3.2 Unplanned / interrupt routing *(automation)*
- **Trigger:** issue created.
- **Condition:** issue has no parent epic AND issue type = Task.
- **Action:** add label `unplanned`; if still unlinked after creation, set Component = `Ops-Maintenance` and link to the Operational & Maintenance epic (DDT-54).
- **Note:** true "interrupt" detection is heuristic; this rule catches standalone maintenance. Mid-project interrupts still get `unplanned` applied manually per the DDT-54 convention.

### 3.3 Idea ↔ Epic delivery link *(automation)*
- **Trigger:** DDT epic created or updated.
- **Condition:** summary/description contains a `VDDR-###` key.
- **Action:** create an issue link (`implements` / `delivers`) to that VDDR idea.
- **Caveat:** cross-project (software ↔ JPD) linking may need the JPD **delivery-link** type rather than a standard issue link; if the automation can't create it, fall back to JPD's native "Delivery" field on the idea. Verify in your instance.

### 3.4 Blocked-reason capture *(workflow validator)*
- **Where:** validator on any transition **into** `Blocked`.
- **Condition:** `Blocked Reason` is set.
- **Effect:** blocks with *"Pick a blocked reason."* This is what turns blockers from anecdote into countable data.

### 3.5 Stale-in-progress nudge *(automation)*
- **Trigger:** scheduled, daily.
- **Condition:** status = In Progress AND not updated in 14 days.
- **Action:** comment @assignee: *"Still active? Move to Blocked (with reason) or update."* No auto-move — just a nudge.

---

## 4. Controlled Value Lists

**Component / Theme** (shared vocabulary across DDT components and VDDR themes so they reconcile):
`HCI` · `Estimates` · `Call/RDVM` · `Marketing` · `Pricing/Finance` · `People/Labor` · `Platform/Schema` · `Vendor-Integrations` · `Ops-Maintenance`

**Blocked Reason** (keep short — long lists don't get used):
`Upstream data dependency` · `Awaiting stakeholder decision` · `Awaiting source-system/app change` · `Blocked by another ticket` · `Capacity/resourcing` · `External vendor`

**Epic Size → eng-weeks** (rough starting map; recalibrate after ~1 quarter of actuals):
| Size | Eng-weeks |
|---|---|
| S | 0.5 |
| M | 1 |
| L | 2–3 |
| XL | 4–6 |

---

## 5. VDDR — JPD Setup (roadmap / quarterly capacity)

This is the **high-level planning layer** — the "something more high level for next quarter" you asked for. It's deliberately coarse: theme budgets + T-shirt sizes, not per-idea estimates.

### 5.1 JPD fields to add
| Field | Type | Purpose |
|---|---|---|
| **Impact** | Single-select 1–5 (or High/Med/Low) | Strategic value |
| **Effort (T-shirt)** | Single-select S/M/L/XL | Maps to eng-weeks, §4 |
| **Theme** | Single-select | Same list as Component, §4 |
| **Delivery** | JPD delivery link | Ties idea → DDT epic, shows delivery progress |

### 5.2 Idea funnel (replace the single "Parking lot" stage)
`Parking lot` → `Exploring` → `Validated` → `Now` → `Next` → `Later`
- Only `Now` ideas should have a linked DDT epic.
- Archive **VDDR-1** (JPD sample card) immediately.

### 5.3 Quarterly capacity ritual (the top-down loop — ~30 min/quarter)
1. **Compute committable capacity** (one number):
   `~3 eng × 12 wks × 0.7 overhead − 20% interrupt reserve ≈ 20 eng-weeks` *(confirm the 0.7 and 20%)*.
2. **Set theme budget** (five numbers): allocate the 20 eng-weeks across themes by strategic weight.
3. **Size ideas** (LLM-assisted): batch idea titles+descriptions → S/M/L/XL; review outliers only.
4. **Fit to budget:** fill each theme's eng-weeks with sized ideas → the ones that fit move to `Now`, rest to `Next`/`Later`.

Steps 3–4 are automatable via a Claude skill (reads VDDR, proposes sizes + a Now/Next/Later cut against the theme budget; you approve). Human input = the five theme numbers + outlier review.

---

## 6. Sequencing

| Order | Item | Effort | Blocks |
|---|---|---|---|
| 1 | §3.1 required-field validator | 30 min | Clean forecasting history |
| 2 | §1 fields + §2 Kanban/WIP/Blocked status | Half day | Everything below |
| 3 | §3.4 blocked-reason validator + §4 lists | 1 hr | Retro data |
| 4 | §5.1–5.2 JPD fields + funnel; archive VDDR-1 | 1–2 hrs | Quarterly planning |
| 5 | §3.2, §3.3, §3.5 automations | 2–3 hrs | Nice-to-have |
| 6 | Backfill 43 blank descriptions (LLM draft → review) | ongoing | — |

---

## 7. Open Items to Confirm

- Capacity assumptions: overhead factor (0.7?) and interrupt reserve (20%?).
- Preferred handoff format for this spec (Jira epic+subtasks vs. Confluence vs. this markdown).
- Whether your JPD instance supports cross-project delivery links (affects §3.3).
- Impact field granularity: 1–5 numeric vs. High/Med/Low.
