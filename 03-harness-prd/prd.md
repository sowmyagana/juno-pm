# AI PRD · Juno

> Module 3 · Harness / AI PRD. The AI product requirements doc specifying all six harness surfaces, built with the **M3 · AI PRD Builder** (archetype and starting settings from the **M3 · Harness Architecture Decider**). Paste the tool's markdown over this file.

## Problem & user

_The user problem and who has it._

_____

## The harness

| Surface | Specification |
|---|---|
| 01 Context | _what Juno gets to see_ |
| 02 Tools | _the verb list_ |
| 03 Loop | _turn ceiling and escalation_ |
| 04 Memory | _what persists, at what scope_ |
| 05 Permissions | _read … · draft … · write … · send …_ |
| 06 Verification | _the check before output ships_ |

## 01 Context · Data Requirements
Context: where Juno looks each Tuesday

Rule: Juno must check all four streams before naming the open customer issues. An issue is not confirmed from one stream alone.

Source	What Juno looks for	Window
Tickets	Open customer issues, with severity, client and age	All open items
Engineering questions	Active questions from the team about burning issues	Unanswered or open this week
Leadership asks	Urgent reports requested for the upcoming Tuesday meeting. These get priority.	Open asks due by Tuesday
Meeting notes	Customer meetings where an issue was discussed	Last 1-2 weeks

Reference sources

Customer product documents: the documents drafted for each customer, including any customizations. Juno checks them to see whether an issue comes from a customization or is a general product problem.
Roadmap: a key input. Juno uses it to mark each issue covered, partial or gap. It is read-only, and Juno proposes no edits.

If a source is missing or stale: Juno says so, prints the data timestamp, and lists the gap under open questions. It does not assume anything about that source.
REQUIRED SOURCES

Streams (all four must be checked before naming open customer issues)
1. Tickets: the production support ticketing system [SYSTEM NAME]. Fields needed: ticket ID, title, severity, client, age, link.
2. Engineering questions: Slack [CHANNEL NAMES], email [MAILBOX OR DISTRIBUTION LIST], and meeting action items [WHERE THEY ARE KEPT]. Only active questions on burning issues.
3. Leadership asks: the Friday leadership dashboard [DASHBOARD NAME] plus any Slack or email request for the upcoming Tuesday meeting. These get priority.
4. Meeting notes: customer meeting notes and transcripts from the last 1-2 weeks [WHERE THEY ARE STORED].

Reference sources
5. Customer product documents: one document per customer, including customizations [LOCATION AND NAMING PATTERN]. Used to tell a customization-specific issue from a general product problem.
6. Roadmap: the 2027 roadmap drafted by the Core Product Team [DOCUMENT NAME AND LOCATION]. Read-only. Juno proposes no edits.

DELIBERATE EXCLUSIONS (what is left out, and why)
- Closed tickets, unless reopened. Reason: they are not open issues.
- Patient-level data (PHI/PII), stripped before Juno sees it. Reason: privacy, and it adds nothing to prioritization.
- Private direct messages. Reason: not a shared team source.
- Customer contact details. Reason: not needed to rank issues.
- Meetings older than 2 weeks (proposed). Reason: stale signals dilute the current picture. An open issue with no newer signal is flagged instead of silently dropped.
- Prospect meetings that do not discuss an existing customer issue (proposed). Reason: they are not open customer issues.
- Leadership asks not tied to the upcoming Tuesday meeting (proposed). Reason: they are shown, but not prioritized.
- ARR in the channel view (proposed). Reason: the post is visible to the whole channel. Show it only to the Senior PM.

SLA OF TRUTH (how fast a change in a source becomes a change in the answer)
- With live connectors, Juno refreshes sources every 15 minutes.
- The Tuesday post is a snapshot taken at generation time (proposed: about 12:15 PM). A change made before the snapshot appears in the post. A change made after it appears only when the Senior PM clicks Verify sources, Re-rank or Promote, which re-fetch the affected items.
- Every post prints its data timestamp.
- A new P0 or possible data-integrity error that arrives after the snapshot (proposed): Juno sends the Senior PM a separate alert rather than waiting for next Tuesday.
- Roadmap and customer documents are read at generation. Juno prints their last-updated date if the source provides one.

WHEN A SOURCE IS UNREACHABLE
Rule: retry twice per source, then apply the behavior below (proposed). Juno never implies a live refresh happened when it did not.
- Tickets: FAIL LOUDLY. Post a short "review delayed" notice naming the cause, and alert the Senior PM. Reason: tickets are the core source and the main route for P0s.
- Leadership asks: DEGRADE WITH A BANNER, plus a direct message to the Senior PM to check manually. Reason: a missed urgent ask is costly.
- Engineering questions and meeting notes: DEGRADE. Post marked "partial review", naming the missing stream and the data timestamp. Issues that rest on the missing stream are labeled "not checked against [stream]".
- Roadmap: DEGRADE. Show the roadmap link as "Unknown", never as "gap", and list it under open questions.
- Customer product documents: DEGRADE. List "customization not checked" under open questions.
- Escalation items (possible PHI exposure, clinical data-integrity error, regulatory non-compliance) are always shown, labeled "unverified" if a source was down.
- If the 12:45 PM cutoff is hit, a notice replaces the post.

- LATE ALERT (separate from the Tuesday post)

Trigger (proposed): after the Tuesday snapshot, a new or changed item that meets any escalation rule: P0, possible PHI exposure, clinical data-integrity error, or regulatory non-compliance. One signal is enough.

Where: direct message to the Senior PM (proposed). Not the channel, because it may involve PHI or client detail.

Content, kept short:
- One line: what changed and why it was escalated.
- Plain-terms line and Why it matters line, same style as the cards.
- Severity, client, and a link to the original source, only if the inputs provide one.
- Status: "Not yet verified by Senior PM".
- Buttons: Verify sources, Add to this week's review.

Rules:
- No patient-level data.
- No dates committed on Product's behalf.
- One alert per item. A follow-up signal on the same item updates the first alert instead of sending a new one.
- Tuesday only: an alert after the 12:45 PM cutoff but before the post still goes out, with a line saying the post was already generated.
- 
- **Required sources:** _name actual systems and documents, not "internal docs."_
- **Deliberate exclusions:** _what you left out, and why._
- **SLA of truth:** _how fast a change in a source becomes a change in the answer._
- **When a source is unreachable:** _fail loudly, or degrade how?_

## 02 Tools · System Capabilities

read tickets, Slack threads, meeting notes, roadmap excerpt, account context. Post, update in place, reply in thread, record a Senior PM decision.

Omissions:
No editing the roadmap, since your constraints forbid it.
No changing ticket severity in the ticketing system.
No contacting customers.
No committing dates.

- **The verb list:** _every function Juno may call, with its side-effect class (read / draft / write / send)._
- **Deliberate omissions:** _the tools you did not build, and why. Name at least one._

## 03 Loop · AI Costs & Latency
Turn ceiling: one full analysis run per Tuesday. Button actions such as Re-rank or Promote re-render from stored results and don't re-fetch.
Retry budget: 2 retries per connector, and 1 correction pass after verification.
Hard cutoff: 12:45 PM, which leaves room to post at 12:55 PM.
Escalation triggers (stop and notify the Senior PM):
A signal stream is missing.
Verification fails after the correction pass.
Possible PHI is detected.
A contradiction can't be resolved.
The cutoff is hit.
The notice replaces the post. It never sends a partial review without a label.

- **Turn ceiling:** _e.g. 5 turns per request._
- **Escalation trigger:** _what makes it stop and hand back to a human._
- **Latency and cost target:** _e.g. p95 under 8s, under $0.12 per task._

## 04 Memory · Data Requirements
Previous week's report and Senior PM overrides, promotions, flags, verified status and confirmed sprint focus.
Persists:
Last review's ranks.
Senior PM decisions.
Open questions and glossary additions.
Expires:
Raw ticket text and transcripts. Juno re-fetches them every week, and PHI is never stored.
An override lasts until a new signal arrives on that issue or about 4 weeks pass, whichever comes first.
Who may write: only Senior PM actions write decisions. A thread reply from anyone else is treated as a comment, not an override.

- **What persists, at what scope:** _turn, session, user, or team._
- **Expiry:** _TTL per fact._
- **Write rules:** _who outranks the model when a human disagrees._

## 05 Permissions · AI Risks & Mitigations

The Permissions Dial is set per action: Auto, Ask, or Never.

AUTO (Juno acts on its own)
- Read the four streams, group duplicates, and rank the issues. Reason: analysis only.
- Post the Tuesday review and the escalation banner. Reason: the post is a prompt for review and changes nothing downstream.

ASK (needs a Senior PM action first)
- Update the post after Re-rank or Promote. Reason: the button click is the approval.
- Send to Engineering. Reason: allowed only after the Senior PM confirms sprint focus.

NEVER AUTO (only the Senior PM can do this)
- Mark a card "Verified". Reason: only the Senior PM can verify Juno's analysis.
- Confirm sprint focus. Reason: it is a decision, not an analysis.

NEVER (out of scope for Juno)
- Change ticket severity or edit the roadmap. Reason: outside Juno's role.
- Commit a date or contact a customer. Reason: these are Product's commitments, not Juno's.
- Reproduce patient-level data. Reason: PHI rule.

| Side-effect class | Tier |
|---|---|
| read | _auto / confirm / blocked_ |
| draft | _auto / confirm / blocked_ |
| write | _auto / confirm / blocked_ |
| send | _auto / confirm / blocked_ |

**Justification:** _blast radius and recoverability for each non-obvious tier._

## 06 Verification · AI Testing & Measurement

Checks before posting:

Counts: signal and client counts come from the source rows, not from the model. Top 3 plus Other issues must equal total reviewed.
Evidence: every Fact traces to an input, and every link is one from the inputs.
Rules: severity follows your definitions, and confidence matches the signal count (3+ is High, 2 is Medium, 1 is Low). Escalated items never appear in Other issues.
Safety: a scan for patient-level data and for committed dates.
Language: acronyms are explained on first use.

Failure behavior:

Hard fail (PHI, an invented link or ID, a missing escalation): remove the element and re-check once. If it still fails, post a short "review delayed" notice with the reason.
Soft fail (style or plain-language): ship the post with a flag.
Escalated items: never dropped, even if a check fails. Post them labeled "unverified".

- **The check before output ships:** _what is checked, against what bar._
- **Failure behaviour:** _what happens when the check fails. Silently dropping the output is never the answer._

## Eval plan

_Stub. Module 5 fills this in: the human eval rubric in `05-evals/human-rubric.md`, with its pass bar._

## Out of scope

_Any capability not in the verb list, and any action tiered `blocked`. Both are decisions on the record, not omissions._
