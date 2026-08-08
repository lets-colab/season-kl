# ChatGPT Handoff Brief — Season KL

**Prepared:** 8 August 2026
**For:** ChatGPT (or any assistant with Notion + Instagram access)
**Purpose:** complete the three tasks that could not be finished in the Claude Code environment.

---

## Read this first

You are picking up mid-project. The project is **Season KL** — a Kuala Lumpur nightlife brand with a live website, a Supabase backend, a Notion CRM, and an Instagram outreach operation.

A full July 2026 report has already been produced from verified production data. **Do not redo it.** Read it, then do only the parts it could not.

**Read in this order:**

1. `operations/reports/2026-07/README.md` — the July report
2. `operations/reports/2026-07/EVIDENCE.md` — every number's source query
3. `STRATEGY.md` — business strategy, targets, system alignment map
4. `AGENTS.md` — outreach agent rules (**binding — see Constraints below**)
5. `operations/reports/2026-07/NOTION-AUDIT-SPEC.md` — your main task

---

## Situation in five facts

1. Website traffic collapsed **98% on 12 July** and has not recovered. Facebook supplied 90% of it and stopped.
2. **51 First Visit Passes issued, 100% synced to Notion, 0 ever redeemed.** No door check-in exists, so attendance has never been measured.
3. The **outreach system is fully built and has never been used** — `operations/outreach/prospects.csv` has a header row and no data.
4. Engineering is strong: 145 commits, 17 PRs in July, 10 live edge functions, 70/70 records synced.
5. Last production change was **16 July**. The project has been static for 23 days.

**One-line summary:** the machine works, nobody is feeding it, and nobody is measuring what comes out.

---

## Your tasks

### TASK 1 — Notion workspace audit & cleanup ⭐ primary

**Why you:** the Notion connector requires interactive OAuth that the Claude Code environment cannot perform. You presumably have access.

**Specification:** `operations/reports/2026-07/NOTION-AUDIT-SPEC.md` — follow it as written. It contains the inventory schema, duplicate-detection rules, decision tree, page-description template, automation write-map, and target structure.

**Do not skip §6.** Ten edge functions write into Notion. Archiving a page one of them targets breaks a live automation with no error surfaced to anyone. This project has already lost data twice to exactly that failure mode (commits `6d44265`, `8bec364`).

**Deliverables:**
- `operations/reports/2026-07/notion-inventory.csv` — every page, with the action taken and the reason
- A description block (spec §5) on every surviving page
- Restructure per spec §7
- One end-to-end test: submit a real pass, confirm arrival in Guest CRM

**Rules:** archive, never delete. One category at a time. Snapshot first.

---

### TASK 2 — Notion CRM dashboard

Build a CRM dashboard over the existing Guest CRM. The July report §9 specifies the six KPIs management should track weekly:

| # | KPI | Source | Currently |
|---|---|---|---|
| 1 | Sessions | `site_events` | 2,093 (July) |
| 2 | Channel concentration % | `site_events.referrer` | 90.2% Facebook ⚠ |
| 3 | Passes issued | `season_passes` | 51 |
| 4 | **Passes redeemed** | `season_passes.redemption_status` | **0 — unmeasurable** |
| 5 | Redemption rate | derived | 0% (target 40–60%) |
| 6 | Prospects contacted | `prospects.csv` | **0** |

**Design constraints:**
- KPIs 4 and 5 are currently unmeasurable. **Show them as "Not instrumented," not as zero.** A dashboard reading 0% redemption implies guests aren't converting; the truth is nobody has ever checked. Those are different problems with different fixes, and conflating them will send management after the wrong one.
- Pipeline stages must match `STRATEGY.md` §3: IDENTIFY → OUTREACH → NURTURE → CONFIRM → ATTEND → RETAIN.
- Lead Source must match the 7 crowd streams in `STRATEGY.md` §2.
- Constrain Lead Source to a **select**, not free text. There is already a defect: `kols` and `KOL / Creator` exist as separate values (EVIDENCE.md §E8).

**If the user supplies Instagram profiles** (they offered to), load them into the Prospect Pipeline using the Season Scout qualification model in `AGENTS.md` — 5 factors scored 0–2, total 0–10. Do not invent scores; see Constraints.

---

### TASK 3 — Design system reviews

Two Claude Design projects need review. Neither could be opened — `DesignSync` requires an interactive `/design-login`.

| Project | ID | Question to answer |
|---|---|---|
| Architecture / System Guide / Brief | `e16c234e-37c1-42f7-ae11-406bb62f449a` | Does it match the **actual** backend? |
| Season KL Arrival (3D cinematic) | `348b2ab5-a1e2-44a4-88f2-8c6674a66f0c` | Is it an upgrade on the current frontend? |

**For the architecture review**, check the design against reality — not against `STRATEGY.md`, which is documentation and may itself be stale:

- 9 Supabase tables: `season_passes`, `site_events`, `trooper_signups`, `ambassador_signups`, `kol_signups`, `wheel_spins`, `table_bookings`, `lead_safetynet`, `health_logs`
- 10 edge functions with versions — EVIDENCE.md §E10
- 8 live pages: `index`, `explorers`, `icons`, `tastemakers`, `menu`, `tables`, `spin`, `report`
- Flow: website form → Supabase → `issue-season-pass` / `sync-season-kl` → Notion

Flag anything the design shows that does not exist, and anything that exists but the design omits. **The door check-in is the one to watch** — `STRATEGY.md` describes a Proof Chain ending in "Door Verified," and that stage has never run.

**For the frontend review**, current state is `explorers.html` (46KB), `index.html` (23KB), and 6 more pages, with a cinematic scroll system (`c62d96a`), 3D logo animation (`21dfeac`), and full i18n across EN / 简体中文 / 한국어 / 广东话 (`e086b16`). Any "upgrade" must preserve:

1. **i18n across all four languages** — this serves Crowd Stream #7 (Community Partnerships) and is a strategic asset, not decoration.
2. **Analytics instrumentation** — every page writes to `site_events`. Losing it blinds the only working measurement the business has.
3. **Form → Supabase wiring** — `explorers.html` carries 97.7% of all traffic. A regression here costs everything.
4. **Mobile performance** — CyberTrooper burned seven commits on Three.js mobile bugs (`83d5fcb` through `84f577a`). Do not reintroduce that class of problem; check any 3D work on a real phone.

---

## Constraints — these are binding

From `AGENTS.md`, which governs all outreach work:

- **Research only publicly available profiles.** No private data.
- **Never invent** follower counts, location, engagement, contact details, or audience fit. Flag uncertainty as `UNVERIFIED`.
- **Do not log into Instagram** as the brand, bypass platform controls, or send messages. Research and preparation only; a human sends.
- **Respect `DO_NOT_CONTACT`.**
- **Do not modify the public website or production code** unless explicitly asked.
- Invitations are personal and selective — never mass-broadcast in tone.

Two more from this handoff:

- **Do not fabricate metrics.** Every number in the July report traces to a query in EVIDENCE.md. Hold anything you add to that standard, or mark it clearly as an estimate.
- **Do not present the outreach system as having produced results.** It is built and unused. Saying otherwise to management is the one thing that would destroy the credibility this report was written to establish.

---

## Access map

| System | Status here | Notes |
|---|---|---|
| GitHub — `lets-colab/season-kl` | ✅ | Branch `claude/new-session-v2idax` |
| GitHub — `lets-colab/cybertrooper` | ✅ | Dormant since 3 Jul |
| Supabase `nykukkccynnykbmxznhl` | ✅ | 9 tables, 10 functions |
| Live site | ✅ | `lets-colab.github.io/season-kl` |
| **Notion** | ❌ | **Your task** |
| **Claude Design** | ❌ | **Your task** |
| **Instagram** | ❌ | **Your task** |
| Facebook Ads | ❌ | Nobody has checked it — see below |

---

## The most urgent thing, if you only do one

**Someone must open Facebook Ads Manager.**

Traffic died on 12 July. Facebook was 90.2% of it. The diagnosis in the July report is inferential — derived from the referrer collapse pattern, because no one in this project has access to the ad account. It could be a paused campaign, an exhausted budget, a policy strike, or a disabled account. Each has a different fix and a different recovery time.

Every other item on the roadmap assumes traffic returns. Until someone checks, that assumption is unverified — and it has been unverified for 27 days.

---

## Suggested order

1. Open Facebook Ads Manager (5 minutes, unblocks everything downstream)
2. Task 1 — Notion audit (largest scope, clearest spec)
3. Task 3 — design reviews (needs only design access)
4. Task 2 — CRM dashboard (depends on Task 1 being finished)

---

## When you're done

Commit to branch `claude/new-session-v2idax` in `lets-colab/season-kl`:

```
operations/reports/2026-07/notion-inventory.csv
operations/reports/2026-07/design-review-architecture.md
operations/reports/2026-07/design-review-frontend.md
operations/reports/2026-07/proof/dms/index.csv
```

Then update `operations/reports/2026-07/README.md` — replace §6 (Notion) and §7 (Design reviews), both currently marked BLOCKED, with your findings.
