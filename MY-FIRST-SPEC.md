# MY-FIRST-SPEC.md — Recall
> Product Requirements Document (PRD) · v1.0  
> Author: Vy · Status: Draft · Last updated: May 2026

---

## 1. Problem

Freelancers juggling 3+ client projects attend 8–12 meetings per week across different clients.
Before each meeting, they waste 10–20 minutes manually searching through old email threads,
Notion notes, or paper to remember what was discussed last time.

If the meeting is with a less important client, they often skip prep entirely and show up unprepared.
This happens at least 2 out of every 3 meetings.

**The exact moment of pain:**  
5–10 minutes before a meeting, when the freelancer is already rushing, they realise they
have no clear memory of what was left unresolved with this client last time.

**What they do today:**
- Scroll back manually through Notion or email (10–20 min)
- Trust memory and show up unprepared
- Write action items on paper and forget to review them
- Set calendar reminders but have nothing useful to read when the reminder fires

**Emotional pain:**  
They feel embarrassed showing up unprepared to clients they are charging significant fees.

---

## 2. User

**Name:** Maya  
**Role:** Independent consultant / freelancer  
**Active projects:** 3 clients simultaneously  
**Meetings:** 8–12 per week across different clients  
**Current tools:** Notion (meeting notes) + Google Calendar + Gmail  
**Pain level:** 8/10

Maya is not bad at her job. She is just context-switching too fast across clients
for manual prep to be realistic every time.

---

## 3. Solution

**Recall** — a Mac menubar app that shows a 3-bullet pre-meeting briefing
5 minutes before each meeting, automatically.

**How it works:**
1. Connects to Google Calendar → detects upcoming meetings
2. Matches the meeting to a client → pulls past Notion notes for that client
3. Runs context freshness logic → filters resolved topics, surfaces open actions
4. Fires a menubar notification 5 minutes before the meeting
5. Maya clicks the icon → sees a 3-bullet briefing in a popup
6. She can thumbs-down any item to remove it from future briefings

**Architecture:**
- Local-first: all processing happens on the user's Mac, no data sent to servers
- OAuth tokens stored on device only
- No account required beyond connecting Notion + Google Calendar

---

## 4. Context Freshness Logic

This is what makes Recall useful rather than noisy.

**Signals a topic is RESOLVED (deprioritise):**
- Keywords: "launched", "shipped", "resolved", "closed", "done", "confirmed", "cancelled"
- Phrasing: "we decided to…", "no longer needed", "not moving forward"

**Signals a topic is STILL OPEN (surface it):**
- Keywords: "follow up", "pending", "waiting for", "action item", "next steps", "TBD"
- Unanswered questions (sentences ending in `?`)
- Items with no resolution in subsequent notes

**Recency weighting:**
| Age of note | Weight |
|---|---|
| Last 30 days | High priority |
| 31–90 days | Medium — show if unresolved |
| 90+ days | Only show if explicitly still open |

**User feedback:**
- Thumbs down on any bullet → removes it permanently from that client's briefings
- Over time this trains Recall to surface what actually matters to Maya

---

## 5. ODI Opportunity Scoring

Outcomes scored using the Outcome-Driven Innovation formula:  
**Opportunity = Importance + max(Importance − Satisfaction, 0)**  
Scores above 10 = underserved. Scores based on estimated user research (to be validated).

| Desired outcome | Importance | Satisfaction | Score | Priority |
|---|---|---|---|---|
| Know what was left unresolved last time | 9 | 2 | **16** | Build v1 |
| Surface key context without manual search | 9 | 2 | **16** | Build v1 |
| Know which context is still relevant | 8 | 1 | **15** | Build v1 |
| Get briefed automatically — no manual trigger | 8 | 2 | **14** | Build v1 |
| Aggregate notes across tools (Notion + Zoom) | 8 | 1 | **15** | v2 |
| Know what to follow up on after meeting | 7 | 3 | **11** | v2 |
| Verify briefing quality (thumbs feedback) | 6 | 4 | **8** | v1 nice-to-have |
| Post-meeting summary sent to attendees | 5 | 5 | **5** | Not v1 |

**Note:** These scores are estimated. Next step is to validate with 5–10 real freelancer interviews.

---

## 6. What We Are NOT Building (v1)

| Out of scope | Reason |
|---|---|
| Post-meeting follow-up drafts | Second product — validate pre-meeting first |
| Zoom / Teams / Gmail integration | Too much scope for v1 |
| Meeting recorder | Different product category |
| Full CRM | Not replacing Notion or HubSpot |
| Team / shared features | Solo freelancer only in v1 |
| Windows version | Mac menubar only in v1 |
| AI-generated notes | Reading existing notes only |

---

## 7. Data Sources (Phased)

| Version | Sources |
|---|---|
| v1 | Notion meeting notes only |
| v2 | + Zoom AI recap emails via Gmail |
| v3 | + Google Calendar attendee matching for auto client-tagging |

---

## 8. Competitive Landscape

| Tool | What it does | Gap vs Recall |
|---|---|---|
| Zoom AI Companion | Summarises THIS meeting (backward-looking) | No forward-looking brief before next meeting |
| Microsoft Teams Intelligent Recap | Same as Zoom — requires Premium plan | Locked to Teams ecosystem |
| Notion AI | Stores and summarises notes on demand | No proactive reminder, no cross-meeting context |
| Granola / Mem.ai | General AI note tools | Not pre-meeting focused, not freelancer-specific |

**Recall's differentiation:**  
The only tool that connects ALL past meetings forward-looking, across tools, specifically
for freelancers managing multiple client relationships.

---

## 9. Success Metrics

| Metric | Target |
|---|---|
| Briefing open rate | 80%+ before meetings |
| Meeting prep time | From 15 min → under 2 min |
| Day 7 retention | 60%+ |
| Day 30 retention | 40%+ |
| NPS signal | User reports feeling prepared, not missing key context |

---

## 10. Technical Architecture (v1)

```
Google Calendar API
       ↓
  Detect meeting 5 min before
       ↓
  Match to client name / email domain
       ↓
Notion API → fetch meeting notes for that client
       ↓
  Context freshness filter
  (recency weighting + keyword detection)
       ↓
  Generate 3-bullet briefing
       ↓
  Display in Mac menubar popup
  (local only — no server)
```

**Tech stack:**
- Mac menubar app: Swift + SwiftUI
- Notion integration: Notion Public API (OAuth)
- Calendar integration: Google Calendar API (OAuth)
- All data processing: on-device only

---

## 11. Open Questions

1. What is the minimum Notion note quality needed for Recall to work well?
2. What should the empty state look like on day 1 (no notes yet)?
3. How does Maya tag / identify which Notion pages belong to which client?
4. If notes are incomplete or missing — show a warning, skip the briefing, or show a fallback?
5. Which Mac menubar framework is best: SwiftUI menu extras vs third-party (e.g. PopClip SDK)?

---

## 12. Roadmap

| Milestone | Scope | Status |
|---|---|---|
| M1 — MVP | Menubar scaffold, Google Calendar, Notion API, 3-bullet briefing, context freshness | Building |
| M2 — Multi-source | Zoom AI recap emails via Gmail, improved client matching | Planned |
| M3 — Public launch | Public OAuth flow, Notion Integration Gallery submission, Product Hunt | Planned |

---

## 13. Key Decisions Made

| Decision | Chose | Did NOT choose | Reason |
|---|---|---|---|
| Architecture | Local-first, on-device processing | Cloud server | Freelancers have confidential client data. Privacy = trust = retention. |
| v1 data source | Notion only | Zoom + Teams + Gmail all at once | Scope creep kills MVPs. Validate the core loop first. |
| Feature scope | Pre-meeting briefing only | Post-meeting follow-up drafts | Push features are harder. Earn trust with pull first. |
| Context logic | Recency weighting + keyword detection | Show all notes from 6 months equally | Old resolved topics are noise. Every briefing must earn trust. |

---

*This spec was built iteratively through research, competitive analysis, user empathy mapping,
and ODI opportunity scoring. It reflects the thinking of a solo PM using Claude as a
cross-functional team.*
