# Notion Audit — Findings (read-only pass)

**Ran:** 8 August 2026, after the Notion connector was authorised.
**Workspace:** Mr.Troopers Office (`2b6b35c3-fd42-8169-a835-00035c19980e`)
**Scope of this pass:** inventory and verification only. **Nothing was created, changed, moved, or archived.** This is §2 of [NOTION-AUDIT-SPEC.md](NOTION-AUDIT-SPEC.md) — "do this first, change nothing."

---

## Headline: three corrections to the July report

The report was written without Notion access. With access, three of its claims need revising — one of them a claim I made too strongly.

### ❌ Correction 1 — the sync is **not** 100%. It is duplicated and lossy.

The report said: *"70 of 70 records reached Notion. The sync layer is the strongest asset in the system."*

That was measured from **Supabase's `sync_status` column**, which records that a write was *attempted and acknowledged*. It was never reconciled against what actually landed in Notion. Reconciled:

| System | Pass records |
|---|---|
| Supabase `season_passes` | **51** |
| Notion Pass Tracker — total rows | **77** |
| Notion Pass Tracker — **distinct** `External Pass ID` | **45** |
| Surplus duplicate rows | **30** |
| Rows with no sync status and no external ID | 2 |

**Two distinct defects:**

1. **30 duplicate rows.** 75 rows carry only 45 distinct pass IDs. The same pass was written to Notion multiple times.
2. **6 passes never arrived.** Supabase holds 51; only 45 distinct reached Notion. Six passes are marked synced in Supabase and are absent from Notion.

**Probable cause:** `notion-retry` (v20 — the second-most-deployed function in the system) retrying without an idempotency key. A retry that fires after the original write actually succeeded creates a second row rather than updating the first. The version number supports this: 20 deployments is the signature of a function that kept being patched without the root cause being fixed.

**Why the report got it wrong:** `sync_status='synced'` is written by the *sender*. It is self-reported, exactly like the session counts the report's own limitations section flags as "a floor, not a ceiling." I applied that scepticism to analytics and not to the sync. The reconciliation above is the check that should have backed the claim.

**Corroboration:** Notion's Guest CRM holds **45** rows — exactly matching the 45 distinct pass IDs. The CRM is clean. The duplication is isolated to the Pass Tracker.

---

### ⚠️ Correction 2 — the door check-in front-end **exists**. It has never been used.

The report said: *"Door QR check-in front-end — specified in `STRATEGY.md`, never built. This is the direct cause of zero verified attendance."*

Half right. There is no *website* QR flow. But Notion has a fully-built door check-in system:

- **Database:** 🚪 Season KL — Door Check-In & Attendance Log
- **A working Notion form** — "Season KL Door Check-In" — with required fields for guest, night, source, group size, attendance verified, verification method, data confidence, proof status, and operating owner
- **Verification Method** options including `QR / Pass Code`
- **Three views**, including an Attendance Source Board grouped by acquisition stream
- Relations wired to First Visit Pass, Guest CRM, Events, and Weekly Reports

**Contents of that database: one row, created 2026-07-01 03:59:22Z, with every field null.** Guest name, night, source, time, owner, group size — all empty. Its "Attendance Verified" checkbox is ticked on an otherwise blank record, so even that single row records nothing.

**This changes the recommendation materially.** The report's Priority 0 item was "build door QR check-in." That is no longer the fastest path to a redemption number. **Staff could open the existing Notion form at the door this Friday and start recording verified attendance tonight, with no engineering at all.** The website QR flow remains worth building for guest experience and speed, but it is an optimisation, not the blocker.

The blocker is that nobody has opened the form. That is an operations assignment, not a build task — and it is the single cheapest fix available to this business.

---

### ⚠️ Correction 3 — the client-facing status board overstates readiness.

`Season KL — OS` (the client-visible page) publishes a "Launch status (client-safe)" table. It currently reads:

| Area | Published status | Actual |
|---|---|---|
| Door check-in | **Ready** | 1 blank row, never used |
| First Visit Pass tracking | **Ready** | 77 rows, 30 duplicates, 0 redeemed |
| Guest / RSVP tracking | **Ready** | 45 rows |
| Weekly reporting | **Ready** | not verified this pass |
| Website intake (forms) | **Ready — tested end-to-end** | true, but 6 passes never reached Notion |

"Ready" is defensible as *"the tool is built and configured."* It reads to a client as *"this is working."* For a status board that management and the client both use, that gap is a credibility risk larger than any single number in this report — and it is the same failure mode the report identifies elsewhere: a system that reports its own status without an independent check.

**Recommendation:** split the column into **Built** and **In Use**. Every row above is honestly "Built ✅ / In Use ❌", and that pairing tells the true story without retracting anything.

---

## Structure — what's actually in the workspace

### The two trees

```
Welcome to Cyber Trooper → … (9 levels) … → Home → Client Portal
   └── Season KL — OS                    ← client-facing hub
         ├── Season KL — Command Center
         ├── Season KL — Live Operating Dashboard  (inline db)
         ├── Season KL — KOLs / Ambassadors / First Visit Pass
         ├── Season KL — CRM / RSVPs
         └── Season KL — Weekly Reports

Seasonkl — Client Engine Rooms           ← internal
   └── Season KL — PRIVATE
         ├── 🎟️ First Visit Pass Tracker      (77 rows)
         ├── 🚪 Door Check-In & Attendance Log (1 blank row)
         ├── Guest CRM / Filler Crowd Pipeline (45 rows)
         ├── Ambassador Tracker
         ├── KOL Campaign Manager
         ├── Table Bookings · Wheel Spins
         └── Crowd Stream Tracker · First Visit DM Tracker
```

**`Season KL — OS` sits nine ancestor levels deep.** Four of those ancestors have blank titles. Reaching the client hub means traversing a path that cannot be read or navigated by name.

### `STRATEGY.md` database IDs — verified ✅

All three IDs cited in `STRATEGY.md` resolve correctly. No drift.

| `STRATEGY.md` | Resolves to |
|---|---|
| `b9f391ba…` | 🎟️ Season KL — First Visit Pass Tracker ✅ |
| `cecce125…` | Season KL — Guest CRM / Filler Crowd Pipeline ✅ |
| `b9d4f85e…` | Ambassador data source ✅ |

### Duplicate and overlapping pages — candidates, not verdicts

Flagged per spec §3. **None of these has been touched.** Each needs the §4 decision tree and an automation cross-check before any action.

**Dashboards — three overlapping surfaces:**
- `Season KL — Command Center`
- `Season KL — Live Operating Dashboard` (inline database)
- `Season KL — Live Dashboard`

**Page/database name collision:**
- `Season KL — First Visit Pass` (page) vs `Season KL — First Visit Pass Tracker` (database)

**Audit / status / progress pages — eight covering overlapping ground:**
`Internal Readiness Audit` · `Updated Audit Progress Report` · `10.5/10 audit truth — 2026-06-29` · `Steps 1–7 Execution Status` · `100% Operating Execution Log` · `Controlled Live Test Pack` · `Supabase Verification Request` · `GitHub & Supabase Technical Progress Report`

**Naming inconsistency:** `Seasonkl — Client Engine Rooms` breaks the `Season KL — ` convention every sibling follows.

**An unresolved problem page:** `Season KL Automation Map rows are in Trash / uneditable` — a page whose title is a bug report. Needs resolving or archiving.

**Non-Season-KL content in scope:** `MONO Malaysia`, `Troopers Brain`. Out of scope for this audit; noted so a cleanup doesn't touch them.

---

## What I did not do, and why

**No page was archived, merged, renamed, moved, or edited.**

Spec §4 requires every archive candidate to be cross-checked against the automation write-map first, because ten edge functions write into this workspace and archiving a live target breaks a sync with no error surfaced. This project has already lost data twice to that exact failure mode (`6d44265`, `8bec364`) — and Correction 1 above proves the sync is *currently* misbehaving.

Cleaning up while a duplication bug is actively running would mix two problems together and make both harder to diagnose. **Fix the sync first, then clean.**

---

## Recommended order

| # | Action | Why first |
|---|---|---|
| 1 | **Assign the door check-in form to a named person for Friday** | Zero engineering. Produces the first real redemption number this business has ever had |
| 2 | **Add an idempotency key to `notion-retry`** | Stops the duplication getting worse. Every day it runs adds rows |
| 3 | **Reconcile the 6 missing passes** | Supabase 51 vs Notion 45 distinct — find them or explain them |
| 4 | **De-duplicate the 30 surplus rows** | Only safe after #2, or they regenerate |
| 5 | **Split the OS status table into Built / In Use** | Removes the credibility risk in one edit |
| 6 | **Then** run spec §3–§7 cleanup | With the sync stable and the automation map verified |

---

## Evidence

Reproducible via the Notion MCP. Data source IDs:

| Database | Data source |
|---|---|
| First Visit Pass Tracker | `collection://107ad85b-a91f-4b69-a4b6-e279598329ec` |
| Door Check-In & Attendance Log | `collection://099e20b5-d29c-495b-a224-c3ddb5cbaa0e` |
| Guest CRM / Filler Crowd Pipeline | `collection://9a5082db-9ff3-4331-94ef-e4a6f8207957` |

```sql
-- The duplication finding
SELECT COUNT(*) AS rows_total,
       COUNT(DISTINCT "External Pass ID") AS distinct_ext_ids,
       COUNT(*) - COUNT(DISTINCT "External Pass ID") AS surplus
FROM "collection://107ad85b-a91f-4b69-a4b6-e279598329ec"
WHERE "External Pass ID" IS NOT NULL AND "External Pass ID" != '';
-- → 75 / 45 / 30

-- The door check-in finding
SELECT COUNT(*) FROM "collection://099e20b5-d29c-495b-a224-c3ddb5cbaa0e";
-- → 1  (all fields null, created 2026-07-01)
```
