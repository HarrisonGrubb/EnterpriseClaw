# Morning Meeting Prep Brief

## When to Use
Fires automatically once per weekday at 8:00 AM ET via scheduled trigger.
Can also be run manually with: "run my morning meeting prep" or "prep today's meetings."

BEFORE doing any work, check PTO. If today has an all-day Google Calendar
"Out of Office" event on the user's primary calendar, STOP — do not generate
a brief, do not write anything. Exit silently.

## Storage Model (GitHub, not Google Drive)
Briefs are stored as Markdown files in a GitHub repo. This is deliberate: the
GitHub connector supports true in-place overwrite (create_or_update_file with
the current blob SHA) and delete, which the Google Drive connector does NOT
(Drive exposes only create/copy/read — no update, no delete). GitHub also gives
version history for free via git.

- Repo: HarrisonGrubb/EnterpriseClaw
- Branch: meeting-briefs  (dedicated branch so daily commits don't clutter main)
- Current-day file (OVERWRITTEN in place each run): briefs/today.md
- History file (APPENDED in place each run):          briefs/history.md

## Required Connections
- Google Calendar: read — list today's events, attendees, descriptions,
  attachments; detect all-day Out of Office (PTO) events.
- Gmail: read — search recent threads with meeting attendees / topic.
- Google Drive: read ONLY — read/search recent docs for context. No writes.
- Atlassian/Jira: read — search tickets related to attendees or meeting topic.
- GitHub: write — read + overwrite briefs/today.md and append briefs/history.md
  on the meeting-briefs branch. Write is scoped to EXACTLY those two paths
  (see Stop Conditions). Tools: get_file_contents (to fetch current content +
  SHA), create_or_update_file (overwrite/append with that SHA), and — only if
  ever needed — delete_file. No Snowflake.

## Required Inputs
- Today's date (ET) and the user's primary calendar.
- Timezone: America/New_York.
- Lookback window for Gmail + Drive: 21 days.
- User is internal at @veg.com or @veg.vet (VEG changed domains last year;
  both are valid). Only cross-reference attendees whose email domain is
  @veg.com or @veg.vet.
- On first run, if the meeting-briefs branch or the briefs/ files don't exist,
  create them (create_or_update_file creates a file when none exists — omit SHA).

## Step-by-Step Workflow
1. Resolve "today" in America/New_York. Determine the weekday.
2. PTO CHECK: Query Google Calendar for today's events on the primary
   calendar. If any event is an all-day "Out of Office" / PTO type covering
   the full day, STOP immediately — generate nothing, write nothing, exit
   with a one-line note: "PTO detected for [date] — skipping morning brief."
3. HISTORY APPEND: briefs/today.md still holds the PREVIOUS run's brief at this
   point (it is not overwritten until step 7). Fetch it with get_file_contents
   (capture its blob SHA for step 7). If it exists and has content:
     a. Derive the brief's date from its H1 heading (e.g., "Morning Meeting
        Prep — Thursday, July 03, 2026"); fall back to the file's last commit
        date if the heading can't be parsed.
     b. Fetch briefs/history.md with get_file_contents (capture its SHA).
     c. Build new history content = existing history + "\n\n---\n\n# Brief —
        [that date]\n\n" + the prior today.md content (append at the BOTTOM).
     d. Write briefs/history.md via create_or_update_file, passing history.md's
        current SHA. This is the ONLY modification of the history file. Commit
        message: "History: append [that date] brief".
   If briefs/today.md is missing or empty (first run, or gap after PTO): skip
   the append silently.
4. Pull today's calendar events. For each event, KEEP it only if ALL are true:
     - It has at least 1 attendee other than the user.
     - It is NOT marked/titled as a 1:1.
     - It is NOT the user's morning standup.
     - It is not an all-day / Out of Office / focus-time block.
   Discard everything else.
5. For each qualifying meeting, in chronological order:
     a. Extract: title, start–end time (ET), full attendee list, location/video
        link, description/agenda, and any attached docs.
     b. Identify internal attendees (@veg.com or @veg.vet only) for
        cross-referencing.
     c. GMAIL: Search threads from the last 21 days involving those internal
        attendee addresses AND/OR matching the meeting title's key terms.
        Capture subject, date, participants, and a 1-line gist of the latest
        message. Cap at the 5 most recent/relevant threads per meeting.
     d. DRIVE: Search files modified/shared in the last 21 days that are
        attached to the invite, owned/shared by internal attendees, or match
        the meeting topic. Capture file name, type, owner, last-modified date,
        and link. Cap at 5 per meeting. (Read-only.)
     e. JIRA: Search issues matching the meeting topic or assigned to/reported
        by internal attendees, updated in the last 21 days. Capture key,
        summary, status, assignee, link. Cap at 5 per meeting.
6. Assemble the brief as Markdown per the Output Format below.
7. Overwrite briefs/today.md IN PLACE via create_or_update_file, passing the
   blob SHA captured in step 3 (so it updates rather than errors). Commit
   message: "Brief: [today's date]". Keep the SAME path — never date-stamp the
   filename, never create a second today file. The stable link is:
   github.com/HarrisonGrubb/EnterpriseClaw/blob/meeting-briefs/briefs/today.md
8. Return a short confirmation in chat: number of meetings briefed + the
   (stable) link to briefs/today.md. If run manually, also render the brief inline.

## Output Format
Target file: briefs/today.md (Markdown, overwritten in place each run).

# Morning Meeting Prep — [Weekday, Month DD, YYYY]
_[N] meetings briefed · generated [time] ET_

## [HH:MM–HH:MM] — [Meeting Title]
**Attendees:** [names/emails; mark external attendees with ⚑ but do not
  cross-reference them]
**Link/Location:** [video link or location]
**Agenda (from invite):** [description, or "None provided"]

**Recent email threads (21d):**
- [date] — [subject] — [1-line gist] — [link]
  (or "No recent threads found.")

**Relevant docs (21d):**
- [name] ([type], updated [date], owner [name]) — [link]
  (or "No recent docs found.")

**Open Jira tickets:**
- [KEY] [summary] — [status], [assignee] — [link]
  (or "No related tickets found.")

**Suggested talking points / questions:**
- [2–4 derived from the context above]

**Flags:** [e.g., external attendees present, no agenda, back-to-back conflict,
  document not shared with all attendees — or "None."]

---
(repeat per meeting)

## Error Handling & Stop Conditions
- If a connection fails or auth is expired (Calendar, Gmail, Drive, Jira,
  GitHub): do NOT partial-write a misleading brief. For a single READ source
  failure (Gmail/Drive/Jira), continue and mark that section "⚠ [source]
  unavailable — check connection." If Calendar fails, STOP (no calendar = no
  brief). If GitHub fails, STOP (cannot store the brief) and notify the user.
- If PTO all-day event is detected: STOP, write nothing, report skip.
- If no qualifying meetings today: still run the history-append step, then
  write a today.md that says "No qualifying meetings today." — never fail silently.
- If a section returns no data: print the explicit "No … found." line, never
  omit the section.
- If a create_or_update_file call fails with a SHA conflict (409): re-fetch the
  file with get_file_contents to get the current SHA, then retry once.
- If the history-append step (step 3) fails: do NOT overwrite today.md yet
  (its content is the only copy of the prior brief until it is safely in
  history.md). Report the error and stop, so no history is lost.
- NEVER delete a calendar event.
- NEVER invite anyone to, or modify, any calendar event.
- NEVER send or draft an email; this skill only reads Gmail.
- NEVER create, comment on, or transition a Jira ticket; read-only.
- NEVER write, modify, or delete any repo file other than the two designated
  paths: briefs/today.md and briefs/history.md on the meeting-briefs branch.
  Never touch main, never open a PR, never create dated copies.
- NEVER pull People/Talent, CFO-financial, or customer-identifying data into
  a brief even if it surfaces in search; flag its existence, don't reproduce it.
  (Briefs are committed to the repo — treat that as the reason to be strict.)
