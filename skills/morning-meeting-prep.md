# Morning Meeting Prep Brief

## When to Use
Fires automatically once per weekday at 8:00 AM ET via scheduled trigger.
Can also be run manually with: "run my morning meeting prep" or "prep today's meetings."

BEFORE doing any work, check PTO. If today has an all-day Google Calendar
"Out of Office" event on the user's primary calendar, STOP — do not generate
a brief, do not touch any files. Exit silently.

## Required Connections
- Google Calendar: read — list today's events, attendees, descriptions,
  attachments; detect all-day Out of Office (PTO) events.
- Gmail: read — search recent threads with meeting attendees / topic.
- Google Drive: read + write — read recent docs for context; write today's
  brief file and the running history file. Write is scoped to EXACTLY two
  files inside the "Meeting Prep Briefings" folder (see Stop Conditions).
- Atlassian/Jira: read — search tickets related to attendees or meeting topic.
- (No Snowflake. No Confluence. No GitHub.)

## Required Inputs
- Today's date (ET) and the user's primary calendar.
- Timezone: America/New_York.
- Lookback window for Gmail + Drive: 21 days.
- Folder layout (create on first run if missing):
    Meeting Prep Briefings/
      Today/            → single file, overwritten each run:
                          "brief-YYYY-MM-DD.md"  (only today's file kept here)
      History/          → single running file, appended each run:
                          "briefing-history.md"
- User is internal at @veg.com or @veg.vet (VEG changed domains last year;
  both are valid). Only cross-reference attendees whose email domain is
  @veg.com or @veg.vet.

## Step-by-Step Workflow
1. Resolve "today" in America/New_York. Determine the weekday.
2. PTO CHECK: Query Google Calendar for today's events on the primary
   calendar. If any event is an all-day "Out of Office" / PTO type covering
   the full day, STOP immediately — generate nothing, write nothing, exit
   with a one-line note: "PTO detected for [date] — skipping morning brief."
3. HISTORY APPEND: Look in "Meeting Prep Briefings/Today/" for the most
   recent existing brief file (i.e., yesterday's). If one exists:
     a. Read its full contents.
     b. Read "History/briefing-history.md" (create it empty if missing).
     c. Append yesterday's content to the BOTTOM of the history file under a
        dated header: "\n\n---\n\n# Brief — [yesterday's date]\n\n" + content.
     d. Write the updated history file back (this is the ONLY modification of
        the history file).
     e. Delete yesterday's file from Today/ ONLY after the append succeeds, so
        Today/ holds just the current day. (If append fails, leave it and
        report the error — never lose the file.)
   If no prior file exists (first run, or gap after PTO): skip append silently.
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
        and link. Cap at 5 per meeting.
     e. JIRA: Search issues matching the meeting topic or assigned to/reported
        by internal attendees, updated in the last 21 days. Capture key,
        summary, status, assignee, link. Cap at 5 per meeting.
6. Assemble the brief in the Output Format below.
7. Write the assembled brief to "Meeting Prep Briefings/Today/brief-YYYY-MM-DD.md"
   (overwrite if it already exists for today).
8. Return a short confirmation in chat: number of meetings briefed + a link to
   today's file. If run manually, also render the brief inline.

## Output Format
File: brief-YYYY-MM-DD.md

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
- If a connection fails or auth is expired (Calendar, Gmail, Drive, Jira):
  do NOT partial-write a misleading brief. For a single-source failure,
  continue and mark that section "⚠ [source] unavailable — check connection."
  If Calendar itself fails, STOP (no calendar = no brief) and notify the user.
- If PTO all-day event is detected: STOP, write nothing, report skip.
- If no qualifying meetings today: still run the history-append step, then
  write a brief that says "No qualifying meetings today." — never fail silently.
- If a section returns no data: print the explicit "No … found." line, never
  omit the section.
- If the history-append step fails: keep yesterday's Today/ file in place,
  report the error, and continue to generate today's brief.
- NEVER delete a calendar event.
- NEVER invite anyone to, or modify, any calendar event.
- NEVER send or draft an email; this skill only reads Gmail.
- NEVER create, comment on, or transition a Jira ticket; read-only.
- NEVER modify, move, or delete any file other than the two designated files:
  Today/brief-YYYY-MM-DD.md and History/briefing-history.md.
- NEVER pull People/Talent, CFO-financial, or customer-identifying data into
  a brief even if it surfaces in search; flag its existence, don't reproduce it.
