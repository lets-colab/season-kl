# Appendix C — Notion Workspace Audit & Cleanup Specification

**Status:** specification only — not executed.
**Reason:** the Notion connector requires interactive OAuth, unavailable in this environment.
**Executable by:** anyone with Notion access — a human, ChatGPT, or a future Claude session once the connector is authorised.

This document is written to be run as-is. It is not a description of what a cleanup might look like; it is the procedure.

---

## Objective

Management asked for four things. This spec delivers each:

| Ask | Delivered by |
|---|---|
| Remove irrelevant / duplicate pages | §3 Duplicate detection + §4 Decision tree |
| Describe every page — why it exists | §5 Description template, field `why_created` |
| Show each page's relation to operations | §5 field `operational_relation` + §6 flow map |
| Show how it simplifies work | §5 field `simplifies` |

---

## 1. Known Notion surface

From `STRATEGY.md` (repo documentation — **must be verified against live Notion**, not trusted):

| Database | ID prefix | Documented purpose |
|---|---|---|
| First Visit Pass | `b9f391ba…` | Receives pass issuance from `season_passes` |
| Guest CRM | `cecce125…` | Master guest record, pipeline stages |
| Ambassador Tracker | `b9d4f85e…` | Petal / Bloom / Season tier tracking |
| KOL tracker | *(unnamed)* | Creator relationships |
| DM Tracker | *(unnamed)* | Outreach pipeline; written by `dm-link-open`, `dm-tracker-match` |

**⚠️ First action: this table is a hypothesis.** Run §2 before trusting any row of it.

---

## 2. Step 1 — Inventory (do this first, change nothing)

Enumerate every page and database. For each, capture:

| Field | How to obtain |
|---|---|
| `page_id` | Notion API |
| `title` | Notion API |
| `type` | database / page / subpage |
| `parent` | parent page or workspace root |
| `created_time` | Notion API |
| `last_edited_time` | Notion API |
| `row_count` | databases only |
| `inbound_links` | pages linking *to* this one |
| `written_by_automation` | cross-check §6 table |

Write to `operations/reports/2026-07/notion-inventory.csv`.

**Rule: no deletions until the full inventory exists.** A page that looks orphaned may be an edge-function write target; deleting it breaks a live automation silently — exactly the failure class that cost this project two incidents in June (`6d44265`, `8bec364`).

---

## 3. Step 2 — Duplicate detection

Flag as **candidate duplicates**:

1. **Title similarity** — Levenshtein ≥ 0.85, or identical after lowercasing and stripping `copy`, `v2`, `old`, `new`, `final`, `(1)`, dates.
2. **Schema identity** — databases with identical property-name sets.
3. **Empty + shadowed** — `row_count = 0` AND a similarly-titled database has rows.
4. **Stale + unlinked** — `last_edited_time` > 30 days ago AND `inbound_links = 0` AND not an automation target.
5. **Structural clones** — subpage trees whose child titles match another tree ≥ 80%.

**Candidate ≠ delete.** Every candidate goes through §4.

---

## 4. Step 3 — Decision tree

For each candidate, in order. Stop at the first match.

```
Is it written to by any edge function? (see §6)
├─ YES → KEEP. Mark `automation_critical = true`. Never delete.
└─ NO
   ├─ Does it have rows AND a near-duplicate with rows?
   │   └─ MERGE → migrate rows to the canonical target, then ARCHIVE source.
   │      Canonical = the one an automation writes to; if neither,
   │      the one with more rows; tie → more recent last_edited_time.
   ├─ Is it empty AND shadowed by a populated near-duplicate?
   │   └─ ARCHIVE.
   ├─ Is it stale (>30d) AND unlinked AND empty?
   │   └─ ARCHIVE.
   ├─ Is it referenced by STRATEGY.md or AGENTS.md?
   │   └─ KEEP + document (§5).
   └─ Otherwise → KEEP + document (§5), flag `review_needed = true`.
```

**Archive, never delete.** Notion archive is reversible; deletion after 30 days is not. Given zero verified attendance data, no page can yet be proven worthless — archiving preserves the option to be wrong.

---

## 5. Step 4 — Describe every surviving page

Every kept page gets a callout block at the top. This is the deliverable management specifically asked for.

```markdown
> ## 📋 Page Purpose
>
> **What this is:** [one sentence — what the page holds]
>
> **Why it was created:** [the problem that required it. Reference the
>   commit, incident, or strategy section that motivated it.]
>
> **Operational relation:** [where it sits in the flow. Name upstream and
>   downstream systems explicitly — e.g. "Receives from Supabase
>   `season_passes` via `sync-season-kl` v26. Feeds Guest CRM pipeline
>   stage Identify→Outreach."]
>
> **How it simplifies work:** [the manual work eliminated. Be concrete:
>   "Removes manual re-keying of every pass into the guest list — ~2 min
>   per lead × 51 leads = ~1.7 hours saved in July."]
>
> **Owner:** [named person]
> **Updated by:** [automation name + version, or "manual"]
> **Review cadence:** [weekly / monthly / on-change]
```

**Quality bar:** a new team member reads only this block and can state what breaks if the page disappears. If they cannot, the description is inadequate.

---

## 6. Automation → Notion write map

**Verify every one of these before archiving anything.** Each is a live write path from Supabase into Notion.

| Edge function | v | Writes to | Trigger |
|---|---|---|---|
| `sync-season-kl` | 26 | Guest CRM, First Visit Pass | Row insert on `season_passes` |
| `notion-sync` | 15 | *(legacy — confirm target)* | Legacy path |
| `notion-retry` | 20 | Same as failed original | Retry queue |
| `issue-season-pass` | 19 | First Visit Pass | Pass issuance |
| `dm-tracker-match` | 4 | DM Tracker | Inbound lead matched to prospect |
| `dm-link-open` | 2 | DM Tracker → "Link Opened" | `?src=igdm&h=<handle>` opened |
| `submit-ambassador` | 2 | Ambassador Tracker | Ambassador form submit |
| `acceptance-mailer` | 3 | Ambassador / KOL status | Acceptance sent |
| `send-pass-email` | 7 | Pass email status | Email dispatch |
| `alicia-chat` | 18 | *(none known — confirm)* | Chat session |

**`notion-sync` (v15) is a specific risk.** It is superseded by `sync-season-kl` (v26) but remains ACTIVE. Determine whether it still writes anywhere. If it is genuinely dead, retiring it removes a whole class of double-write ambiguity — but confirm with logs before touching it.

---

## 7. Proposed target structure

Organise around the **operational flow**, not around document type. The flow is the thing people navigate.

```
Season KL (workspace root)
│
├── 📊 00 · Command Centre
│   ├── Weekly Report            ← the 6 KPIs from the July report §9
│   ├── Live Funnel Dashboard    ← sessions → passes → redeemed
│   └── Risk Register            ← open critical gaps
│
├── 🎯 01 · Acquisition
│   ├── Campaign Brief           ← mirrors operations/outreach/CAMPAIGN_BRIEF.md
│   ├── DM Tracker               ← ⚠ automation target
│   └── Prospect Pipeline        ← mirrors prospects.csv
│
├── 👤 02 · Guest CRM            ← ⚠ automation target (core)
│   ├── Guest CRM (master)
│   ├── First Visit Pass         ← ⚠ automation target
│   └── Door Check-Ins           ← ❗ currently unpopulated — see report §4.3
│
├── 🌸 03 · Community
│   ├── Ambassador Tracker       ← ⚠ automation target
│   └── KOL Tracker              ← ⚠ automation target
│
├── 🍾 04 · Revenue
│   ├── VIP Tables
│   └── Bottle Menu
│
└── 📚 05 · Reference
    ├── Strategy (from STRATEGY.md)
    ├── System Architecture
    └── Archive/                 ← everything from §4
```

**Design principle:** the hierarchy mirrors the funnel — Acquisition → CRM → Community → Revenue. Anyone who understands the business can find a page without being taught the structure.

---

## 8. Execution checklist

- [ ] Authorise Notion connector
- [ ] Run §2 inventory → commit `notion-inventory.csv`
- [ ] Run §3 duplicate detection → flag candidates
- [ ] Cross-check every candidate against §6 automation map
- [ ] Apply §4 decision tree; record the reason per page
- [ ] Migrate rows for `MERGE` outcomes; verify counts match before archiving
- [ ] Archive (never delete)
- [ ] Add §5 description block to every surviving page
- [ ] Restructure per §7
- [ ] **Re-run one end-to-end test:** submit a real pass, confirm it lands in Guest CRM
- [ ] Commit the final inventory with outcomes

**The last test is not optional.** June's two silent-failure incidents both passed visual inspection. The only proof a sync works is a record arriving at the far end.

---

## 9. Guardrails

1. **Never delete — archive.** Reversible beats tidy.
2. **Never touch an automation target without checking logs first.**
3. **Never merge databases with differing schemas** without mapping every property; unmapped properties vanish silently.
4. **Snapshot before starting** — export the workspace.
5. **One category at a time**, verify, then proceed. Not a big-bang cleanup.
6. **Record every decision** — page ID, action, reason. The audit trail is itself a deliverable.
