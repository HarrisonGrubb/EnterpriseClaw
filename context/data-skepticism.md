# Data Skepticism — How We Read VEG Data

> **Status:** Template. Harrison to fill cases 2 and 3 by 2026-06-04.

## Principle

**SQL-right ≠ data-right.** The source systems lie, drift, get repurposed, and get plugged into things they were never meant for. Always interrogate the upstream reality before trusting any finding.

## Case studies

### 1. Boston call conversion — the doorbell problem

Boston looked like it was failing to convert callers. Under the hood: the hospital's doorbell was wired into the phone system to alert staff when someone walked in. Inbound "calls" included every door event. The SQL was right. The data was not.

**Lesson:** When a metric looks anomalous for one location, ask what *physical thing* in that location changed before assuming a behavioral explanation.

### 2. [Case 2 — TBD]

*Harrison to fill in. Suggested structure: ~3-sentence story → one-line lesson.*

### 3. [Case 3 — TBD]

*Harrison to fill in.*

## Skepticism checklist (run before blessing any finding)

- [ ] What does the upstream source system actually capture?
- [ ] Could this field be capturing something different than its name implies?
- [ ] Has the definition or instrumentation changed over time?
- [ ] Is there a real-world business process change that would shift this?
- [ ] Who entered this data and what was their incentive?
- [ ] Does this anomaly cluster by location, time-of-day, source system, or operator?
