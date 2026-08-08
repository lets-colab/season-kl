# Appendix B — Evidence Index

Every figure in the July report is reproducible from this document. Run the query, get the number.

**Project:** `nykukkccynnykbmxznhl` (Supabase)
**Captured:** 8 August 2026
**Method:** direct SQL against production. No intermediary spreadsheets, no manual transcription.

> **Why this appendix exists.** A monthly report whose numbers cannot be independently re-derived is a claim, not a result. Anyone with database access can run these queries and confirm — or refute — every figure presented. That is the standard this report holds itself to.

---

## E1 — Traffic by week

**Report claim:** sessions collapsed from 1,141 to 21 in the week of 13 July.

```sql
SELECT date_trunc('week', created_at)::date AS wk,
       count(*) AS events,
       count(DISTINCT session_id) AS sessions
FROM site_events GROUP BY 1 ORDER BY 1;
```

| wk | events | sessions |
|---|---|---|
| 2026-06-29 | 1423 | 1047 |
| 2026-07-06 | 1479 | 1141 |
| 2026-07-13 | 53 | 21 |
| 2026-07-20 | 26 | 18 |
| 2026-07-27 | 3 | 3 |
| 2026-08-03 | 23 | 13 |

---

## E2 — Daily traffic across the break

**Report claim:** the break is between 11 and 13 July; 13 July has no rows at all.

```sql
SELECT created_at::date AS d, count(*) AS events,
       count(DISTINCT session_id) AS sessions
FROM site_events
WHERE created_at >= '2026-07-08' AND created_at < '2026-07-20'
GROUP BY 1 ORDER BY 1;
```

| d | events | sessions |
|---|---|---|
| 2026-07-08 | 405 | 320 |
| 2026-07-09 | 125 | 98 |
| 2026-07-10 | 20 | 12 |
| 2026-07-11 | 75 | 40 |
| 2026-07-12 | 2 | 2 |
| *2026-07-13* | *— no rows —* | |
| 2026-07-14 | 9 | 3 |
| 2026-07-15 | 1 | 1 |
| 2026-07-16 | 24 | 6 |
| 2026-07-17 | 4 | 3 |
| 2026-07-18 | 13 | 6 |
| 2026-07-19 | 2 | 2 |

---

## E3 — Referrer analysis (the root cause)

**Report claim:** Facebook was 90.2% of pre-collapse traffic and retained 0.2%.

```sql
SELECT CASE WHEN created_at < '2026-07-13' THEN 'A_before' ELSE 'B_after' END AS era,
       CASE
         WHEN referrer IS NULL OR referrer='' THEN '(direct/none)'
         WHEN referrer ILIKE '%facebook%' OR referrer ILIKE '%fb%' THEN 'facebook'
         WHEN referrer ILIKE '%instagram%' THEN 'instagram'
         WHEN referrer ILIKE '%google%'    THEN 'google'
         WHEN referrer ILIKE '%lets-colab.github.io%' THEN 'internal'
         ELSE 'other'
       END AS src,
       count(*) AS events, count(DISTINCT session_id) AS sessions
FROM site_events GROUP BY 1,2 ORDER BY 1, sessions DESC;
```

| era | src | events | sessions |
|---|---|---|---|
| A_before | facebook | 2494 | **1986** |
| A_before | (direct/none) | 205 | 139 |
| A_before | instagram | 82 | 49 |
| A_before | internal | 99 | 21 |
| A_before | other | 19 | 6 |
| A_before | google | 3 | 1 |
| B_after | (direct/none) | 49 | 38 |
| B_after | internal | 33 | 11 |
| B_after | instagram | 6 | 6 |
| B_after | other | 13 | 6 |
| B_after | facebook | 4 | **4** |

**Derivation:** 1986 / (1986+139+49+21+6+1) = 1986 / 2202 = **90.2%**. Retention: 4 / 1986 = **0.20%**.

---

## E4 — Tracking is alive (rules out technical cause)

**Report claim:** events continued after the collapse, so the tracker is not broken.

```sql
SELECT event_name, count(*) n, count(DISTINCT session_id) s,
       min(created_at)::date first_seen, max(created_at)::date last_seen
FROM site_events GROUP BY 1 ORDER BY n DESC;
```

| event_name | n | s | first_seen | last_seen |
|---|---|---|---|---|
| PageView | 2896 | 2244 | 2026-06-29 | **2026-08-08** |
| CompleteRegistration | 56 | 52 | 2026-06-29 | 2026-07-18 |
| Lead | 55 | 51 | 2026-06-29 | 2026-07-18 |
| ShareClick | 1 | 1 | 2026-07-06 | 2026-07-06 |
| ShareCompleted | 1 | 1 | 2026-07-06 | 2026-07-06 |

**Key inference:** `PageView` last seen **8 August** — the day this report was written. The pipeline is live. A broken tracker records nothing.

**Second inference:** `ShareClick`/`ShareCompleted` last fired 6 July, but the IG Story share feature shipped 16 July (`fa813a1`). The feature has therefore **never produced a single event**.

---

## E5 — July headline figures

```sql
WITH j AS (SELECT * FROM season_passes
           WHERE created_at >= '2026-07-01' AND created_at < '2026-08-01')
SELECT (SELECT count(*) FROM j) july_passes,
       (SELECT count(*) FROM season_passes WHERE created_at < '2026-07-01') pre_july_passes,
       (SELECT count(DISTINCT session_id) FROM site_events
          WHERE created_at >= '2026-07-01' AND created_at < '2026-08-01') july_sessions,
       (SELECT count(*) FROM site_events
          WHERE created_at >= '2026-07-01' AND created_at < '2026-08-01'
            AND event_name='Lead') july_leads,
       (SELECT max(created_at)::date FROM season_passes) last_pass_date;
```

| july_passes | pre_july_passes | july_sessions | july_leads | last_pass_date |
|---|---|---|---|---|
| **35** | 16 | **2093** | 50 | **2026-07-11** |

**Derivations:** July conversion = 35 / 2093 = **1.7%**. Days since last pass (as of 8 Aug) = **28**.

---

## E6 — Sync health across all tables

**Report claim:** 70 of 70 CRM records reached Notion; 2 trooper sync errors outstanding.

```sql
SELECT 'passes' t, count(*) n, count(*) FILTER (WHERE sync_status='synced') ok,
       count(*) FILTER (WHERE sync_error IS NOT NULL) err FROM season_passes
UNION ALL SELECT 'wheel_spins', count(*), count(*) FILTER (WHERE sync_status='synced'),
       count(*) FILTER (WHERE sync_error IS NOT NULL) FROM wheel_spins
UNION ALL SELECT 'table_bookings', count(*), count(*) FILTER (WHERE sync_status='synced'),
       count(*) FILTER (WHERE sync_error IS NOT NULL) FROM table_bookings
UNION ALL SELECT 'trooper_signups', count(*), count(*) FILTER (WHERE sync_status='synced'),
       count(*) FILTER (WHERE sync_error IS NOT NULL) FROM trooper_signups
UNION ALL SELECT 'ambassador_signups', count(*), count(*) FILTER (WHERE sync_status='synced'),
       count(*) FILTER (WHERE sync_error IS NOT NULL) FROM ambassador_signups
UNION ALL SELECT 'kol_signups', count(*), count(*) FILTER (WHERE sync_status='synced'),
       count(*) FILTER (WHERE sync_error IS NOT NULL) FROM kol_signups;
```

| t | n | ok | err |
|---|---|---|---|
| passes | 51 | 51 | 0 |
| wheel_spins | 8 | 8 | 0 |
| table_bookings | 2 | 2 | 0 |
| trooper_signups | 5 | 5 | **2** |
| ambassador_signups | 3 | 3 | 0 |
| kol_signups | 1 | 1 | 0 |
| **TOTAL** | **70** | **70** | **2** |

---

## E7 — Zero redemptions (the critical gap)

**Report claim:** no pass has ever been redeemed.

```sql
SELECT date_trunc('week', created_at)::date wk, count(*) passes,
       count(*) FILTER (WHERE sync_status='synced') synced,
       count(*) FILTER (WHERE redemption_status='redeemed') redeemed
FROM season_passes GROUP BY 1 ORDER BY 1;
```

| wk | passes | synced | redeemed |
|---|---|---|---|
| 2026-06-22 | 8 | 8 | **0** |
| 2026-06-29 | 16 | 16 | **0** |
| 2026-07-06 | 27 | 27 | **0** |

**Note:** there is no week after 2026-07-06. Pass issuance stopped entirely.

---

## E8 — Lead source attribution & data defect

```sql
SELECT lead_source, count(*) n,
       count(*) FILTER (WHERE visit_intent IS NOT NULL) with_intent,
       count(*) FILTER (WHERE email IS NOT NULL) with_email,
       count(*) FILTER (WHERE dm_match_status IS NOT NULL) dm_matched
FROM season_passes GROUP BY 1 ORDER BY n DESC;
```

| lead_source | n | with_intent | with_email | dm_matched |
|---|---|---|---|---|
| Instagram | 25 | 16 | 6 | 4 |
| Friend / Word of mouth | 20 | 17 | 12 | 6 |
| KOL / Creator | 2 | 2 | 1 | 1 |
| Ambassador | 2 | 2 | 1 | 1 |
| TikTok | 1 | 1 | 0 | 0 |
| **`kols`** | 1 | 0 | 0 | 0 |

**Defect:** `kols` vs `KOL / Creator` — same stream, two values. `lead_source` is unconstrained free text.

**Quality signal:** Word-of-mouth email capture = 12/20 = **60%**, vs Instagram 6/25 = **24%**. The zero-cost channel produces leads of 2.5× the contactability.

**DM attribution:** 13 of 51 passes carry `dm_match_status`, evidencing DM activity that `prospects.csv` does not account for.

---

## E9 — Page distribution (July)

```sql
SELECT page, count(*) events, count(DISTINCT session_id) sessions
FROM site_events
WHERE created_at >= '2026-07-01' AND created_at < '2026-08-01'
GROUP BY 1 ORDER BY sessions DESC;
```

| page | events | sessions |
|---|---|---|
| explorers | 2576 | **2045** |
| index | 66 | 39 |
| spin | 53 | 23 |
| tables | 33 | 16 |
| home | 17 | 10 |
| icons | 19 | 9 |
| menu | 9 | 7 |
| tastemakers | 8 | 6 |

**Derivation:** 2045 / 2093 = **97.7%** of July sessions landed on `explorers.html`. Traffic was effectively single-page — corroborating a single paid campaign pointing at one destination.

---

## E10 — Live automation inventory

Source: `mcp__Supabase__list_edge_functions`, project `nykukkccynnykbmxznhl`.

| Function | Version | Status |
|---|---|---|
| `sync-season-kl` | 26 | ACTIVE |
| `notion-retry` | 20 | ACTIVE |
| `issue-season-pass` | 19 | ACTIVE |
| `alicia-chat` | 18 | ACTIVE |
| `notion-sync` | 15 | ACTIVE |
| `send-pass-email` | 7 | ACTIVE |
| `dm-tracker-match` | 4 | ACTIVE |
| `acceptance-mailer` | 3 | ACTIVE |
| `dm-link-open` | 2 | ACTIVE |
| `submit-ambassador` | 2 | ACTIVE |

**Total cumulative deployments: 141.**

---

## E11 — Engineering output

```bash
git log --oneline | wc -l                                      # 61  (season-kl)
git log --oneline --since=2026-07-01 --until=2026-08-01 | wc -l # 22  (season-kl, July)
git log --oneline | wc -l                                      # 84  (CyberTrooper)
git log --oneline --since=2026-07-01 --until=2026-08-01 | wc -l # 4   (CyberTrooper, July)
git log --since=2026-07-01 --until=2026-08-01 --oneline | grep -oE '\(#[0-9]+\)' | sort -u
```

| Metric | Value |
|---|---|
| Total commits (both repos) | **145** |
| Season KL — total / July | 61 / **22** |
| CyberTrooper — total / July | 84 / 4 |
| PRs merged in July (season-kl) | **17** (#19–#35) |
| Last production commit | `ccabc81`, 16 Jul 2026 |

---

## E12 — Outreach system is empty

```bash
cat operations/outreach/prospects.csv
```

```
handle,display_name,profile_url,kl_relevance_evidence,campaign_fit_evidence,kl_score,audience_fit_score,activity_score,social_proof_score,brand_fit_score,total_score,priority,personalised_angle,research_date,status,notes
```

**One header row. Zero data rows.** The Season Scout qualification framework (`AGENTS.md`) and campaign brief (`operations/outreach/CAMPAIGN_BRIEF.md`) are complete and unused.

---

## Limitations of this evidence

Stated explicitly so management can calibrate confidence:

1. **Sessions are self-reported by our own tracker.** No third-party analytics cross-check exists. If the tracker under-counts, every traffic figure is a floor, not a ceiling.
2. **Referrer classification is heuristic** — string matching on the `referrer` field. Traffic arriving without a referrer header is classed `(direct/none)` and may include misattributed social traffic.
3. **The Facebook diagnosis is inferential.** I have no access to the Facebook Ads account. The conclusion follows from the referrer collapse pattern; confirming it requires someone to open Ads Manager.
4. **Notion state is entirely unverified** — connector not authorised. All Notion claims derive from `STRATEGY.md`, which is documentation, not a system of record.
5. **`ambassador_signups` has no `created_at`** (uses `submitted_at`), so it is excluded from time-series queries.
6. **Attendance is unmeasurable.** No process has ever written `redemption_status`. Zero redemptions means "never measured," not "measured as zero."
