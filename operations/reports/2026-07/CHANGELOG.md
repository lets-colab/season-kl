# Appendix A — Version Evolution Log

Complete build history across both repositories, from first commit to current production state.
**145 commits total** — 84 in CyberTrooper (18 Jun – 3 Jul), 61 in Season KL (23 Jun – 16 Jul).

Every entry is traceable to a commit SHA. Dates are commit dates.

---

## Timeline overview

```
Jun 18 ──── Jun 23 ──────── Jun 29 ──── Jul 3 ──── Jul 11 ── Jul 12 ── Jul 16 ──── Aug 8
   │           │               │           │          │        │         │           │
   │           │               │           │          │        │         │           └─ report
   │           │               │           │          │        │         └─ last commit
   │           │               │           │          │        └─ ⚠ TRAFFIC COLLAPSE
   │           │               │           │          └─ last pass issued
   │           │               │           └─ deploy war begins
   │           │               └─ analytics + i18n
   │           └─ Season KL split into own repo
   └─ CyberTrooper genesis
```

---

# Part 1 — CyberTrooper (parent platform)

## v0.1 — Genesis (18 Jun)
| Commit | Change |
|---|---|
| `7c428fa` | Initial two-page production build |
| `5fa69c4` | Repo init |
| `32b78ab` | Become-a-trooper recruitment page |
| `da700ba` | Notion sync wired into both submit paths |
| `af9c326` | Mobile: hamburger nav, iOS zoom fix, 480px breakpoints, safe-area insets |

## v0.2 — Notion-first architecture (19 Jun)
The foundational decision of the whole system: **Notion as source of truth, forms write to it directly.**

| Commit | Change |
|---|---|
| `ff47894` | Notion-first submission flow |
| `d67a916` | Align form select values with Notion database options |
| `fbd8fe3` | Fix schema mismatch, real reference_id from DB, Notion sync (`verify_jwt` off + apikey header) |
| `8868905` | Strip null payload fields so DB defaults apply on insert |
| `187b00a` | WhatsApp button on success overlay with dynamic deeplink |
| `4cbfe2e` | DR.X project instructions + knowledge briefing |

## v0.3 — Visual identity (19–20 Jun)
| Commit | Change |
|---|---|
| `6c385b7` | 3D UIX — particle constellation, glassmorphism 2.0, 3D tilt, cursor trail, magnetic buttons |
| `be3a752` | 10.5/10 design pass — word reveal, custom cursor, animated borders, noise grain, counters |
| `6539c93` | 3D WebGL brand site with scroll motion graphics |
| `05c5988` | Pantone Cloud Dancer 11-4201 as 2026 Color of the Year |
| `6ff9db2` | Three.js WebGL particles + Pantone Mocha Mousse |

## v0.4 — The Three.js stabilisation (20 Jun)
Seven consecutive fixes for one class of bug — WebGL canvas sizing and mobile scroll:

| Commit | Change |
|---|---|
| `83d5fcb` | Pin Three.js to r149 (r169 has no UMD build) |
| `ce48aeb` | Always use `window.innerWidth/Height` for renderer size |
| `7b127f7` | `window.innerWidth/Height` fallback on mobile |
| `2ea1563` | Remove legacy `#webgl-canvas` block pushing hero off-screen |
| `4a63c0a` | De-duplicate `index.html` — legacy script half caused scroll glitch |
| `76525fb` | Disable Lenis smooth scroll on touch devices |
| `84f577a` | Guard null DOM refs that halted the entire script |

## v0.5 — ALICIA AI assistant (20–23 Jun)
| Commit | Change |
|---|---|
| `348b613` | Creator recruitment banner + ALICIA AI chat assistant |
| `257f3cc` | 3D transparent face cutout + personal, non-AI tone |
| `b77b356` | Consultant role, personal welcome, strategy-first tone + Claude AI fallback |
| `b41ba04` | Hide floating launcher when panel open |
| `1e90d45` | Transparent cutout PNG avatar |
| `2385b29` | Illustrated portrait + tri-color neon glow |
| `de18f0c` | Apple iOS liquid glass panel + free-floating cutout avatar |

## v0.6 — Quality & separation (21–23 Jun)
| Commit | Change |
|---|---|
| `ca41ce6` | Content audit: align all pages to Notion source of truth, remove aspirational stats |
| `35b6197` | Steve Jobs audit: 10 fixes — country code, tier CTAs, ALICIA fallback, validation UX |
| `0a53833` | Self-serve WhatsApp pass delivery + personalised success screen |
| `e1c0e8e` | Fix 3 bugs from code review |
| `a1646c0` | Obsidian brain graph: full-page background animation |
| `8916f91` | Remove "3Sixty Marketing" attribution |
| `d4f4293` | **Remove season-kl subfolder — Season KL becomes its own repo** |

## v0.7 — Security hardening (23 Jun – 3 Jul) — *final CyberTrooper state*
| Commit | Change |
|---|---|
| `5061b55` | CodeQL analysis workflow |
| `c9c021c`, `ef8f30e` | Fix code scanning alert 1 — bad HTML filtering regexp |
| `58e3209`, `d760512` | Fix code scanning alert 2 — bad HTML filtering regexp |
| `754c33b` | Node.js CI workflow |

**Status: dormant since 3 July.** All July activity was CI/security housekeeping only.

---

# Part 2 — Season KL (current product)

## v1.0 — Foundation (23 Jun)
| Commit | Change |
|---|---|
| `f91feaf` | Initial commit |
| `ba8e1ee` | Site files + GitHub Pages deployment workflow |
| `cf9cacb` | Chrome SEASON PNG logo, nav/footer |
| `fc609a8` | **Neon-cool multi-page architecture** — the structural basis of the current site |
| `ac43aa6` | Production-quality rewrite of explorers & tastemakers |
| `147e765` / `f7837b4` | Hero logo, glass shimmer, crowd category framing |
| `7c79108` | Copy: "a community, not a club"; How It Works cut to 3 steps; SVG logo |

## v1.1 — Cinematic layer (26–27 Jun)
| Commit | Change |
|---|---|
| `c62d96a` | Cinematic scroll system across all 4 pages |
| `6668f26` | Impeccable audit: bugs + accessibility across all 4 pages |
| `7b5462c` | Cinematic scroll + Notion sync + audit |
| `7514db6` | Supabase MCP server config + agent skills |
| `582d663` | Steve Jobs copy pass + Meta Pixel on all 4 pages |

## v1.2 — 🔥 The silent-failure crisis (27–30 Jun)
The most important sequence in the build. Forms were accepting submissions and losing them.

| Commit | Change |
|---|---|
| `6d44265` | **Replace expired Supabase anon key — forms were silently failing with 401** |
| `7e48611` | Fix field names and `pass_id` extraction for `issue-season-pass` |
| `a43ddc3` | Form resilience, honest errors, correct API key |
| `8bec364` | **CRITICAL — forms + analytics were silently failing (api vs public schema)** |

**Lesson institutionalised:** this class of failure is why `lead_safetynet` (#19) was later built. Two silent-failure incidents in four days drove a permanent architectural safeguard.

## v1.3 — Self-hosted analytics (28 Jun)
Meta Pixel replaced with a first-party pipeline — the source of every metric in this report.

| Commit | Change |
|---|---|
| `6b40f37` | OG tags, Meta Pixel, copy pass, 404 page |
| `ca4bc8d` | **Replace Meta Pixel with self-hosted analytics (`site_events` table)** |
| `bb1c5d3` | Analytics on explorers, icons, tastemakers |

## v1.4 — Internationalisation (28–29 Jun)
Directly serves Crowd Stream #7 (Community Partnerships).

| Commit | Change |
|---|---|
| `924256f` | Language selector, new hotline, remove duplicate nav logo |
| `e086b16` | **Full site i18n — EN, 简体中文, 한국어, 广东话** |
| `ce7d882` | Language options finalised |
| `3bafb4d` | System hardening — SEO files, WA validation, required-field indicators |
| `b537ccb` | Full i18n + hotline update + security hardening |

## v1.5 — Strategic alignment (29 Jun)
| Commit | Change |
|---|---|
| `987980c` | **Align site to 3Sixty marketing plan** — Bloom tier, real source capture, honest scarcity |
| `edc3057` | Capture Visit Intent + Guest Count on First Visit Pass |
| `0e69b5e` | Remove public claim/count numbers (honest scarcity) |
| `ab1313e` | KOL WhatsApp routing + international numbers |
| `21dfeac` | 3D logo animation + strategic chapter pill labels |

*Note: `987980c` corrected the "Ember" → "Bloom" tier naming drift documented in `STRATEGY.md`.*

## v1.6 — Revenue features (2–3 Jul)
| PR | Commit | Change |
|---|---|---|
| #19 | `1486558` | **Lead safety-net — no form submission can vanish silently** |
| #20 | `194e62f` | Cinema-style table reservation (clickable floor map + bottle menu) |
| #21 | `41b4858` | Interactive floor-plan booking, Season Wheel game, **live campaign report** |
| #22 | `c24c7b3` | Email confirmations — optional email capture on all forms |
| #23 | `540e95d` | Season Wheel retarget — lucky-follower rewards + IG share-to-story |
| #24 | `561ff63` | Wheel hero banner + RSVP button + menu page |

## v1.7 — ⚔️ The deploy war (3–8 Jul)
Six PRs against one adversary: org policy blocking unpinned third-party GitHub Actions.

| PR | Commit | Change |
|---|---|---|
| — | `9334af1` | Retrigger Pages deploy |
| #26 | `fb7ea7e` | Zero-action Pages publish — org policy blocks unpinned/third-party actions |
| #27 | `24defeb` | Keep `.nojekyll`, explicitly request Pages build each deploy |
| #28 | `0e5360a` | Grant `pages:write` so build request succeeds |
| #29 | `ab7abdf` | **Dual-path publish — branch + official SHA-pinned Pages actions** |
| #30 | `ec9dcc8` | Mode-aware legacy Pages build request |
| — | `93f3880` | Skip deploy-pages job when Pages is in branch mode |

**Resolution:** the workflow now detects at runtime whether Pages is in branch or workflow mode and takes the path that isn't blocked. Six iterations, but the outcome is a deploy pipeline that survives a hostile policy environment without manual intervention.

## v1.8 — Offer iteration (11–15 Jul)
| PR | Commit | Change |
|---|---|---|
| #31 | `d6c440c` | First Visit Pass: 3 free drinks + RSVP CTA to menu & bottles |
| #33 | `6a4febc` | First Visit Pass: **real offer** — 2 signature shooters + 3 club drinks |

## v1.9 — Outreach system (14–15 Jul)
| Commit | Change |
|---|---|
| `c350fe9` | Season KL campaign brief |
| `c3e5746` | Prospect tracker template |
| `bf5151a` | Season Scout agent instructions |
| `ca45434` | Season Scout research agent set up (PR #32) |

**⚠️ Never executed — `prospects.csv` remains empty.**

## v2.0 — Attribution & virality (15–16 Jul) — *current production state*
| PR | Commit | Change |
|---|---|---|
| #34 | `5eb4c3b` | DM attribution — tracking links prefill IG handle + lead source |
| #35 | `fa813a1` | Success screen — one-tap IG Story poster share |
| — | `ccabc81` | DM tracking links report "Link Opened" to the tracker |

**⚠️ Shipped 3–4 days after the traffic collapse. Never exercised at volume.**

---

## Backend evolution — edge function versions

Version number = number of deployments. A high version indicates heavy iteration.

| Function | Version | Interpretation |
|---|---|---|
| `sync-season-kl` | **26** | Most-iterated component in the system — the Supabase↔Notion contract |
| `notion-retry` | 20 | Retry semantics repeatedly hardened |
| `issue-season-pass` | 19 | Core issuance path |
| `alicia-chat` | 18 | AI assistant tuning |
| `notion-sync` | 15 | Legacy path, superseded by `sync-season-kl` |
| `send-pass-email` | 7 | Email delivery |
| `dm-tracker-match` | 4 | July addition |
| `acceptance-mailer` | 3 | July addition |
| `dm-link-open` | 2 | Newest — 16 Jul |
| `submit-ambassador` | 2 | Newest — 16 Jul |

**Reading:** 141 cumulative deployments across 10 functions. The concentration in `sync-season-kl` (26) and `notion-retry` (20) shows where the real engineering difficulty lived — keeping two systems of record consistent.

---

## Current production version

| Component | Version | Last change |
|---|---|---|
| Season KL site | **v2.0** | 16 Jul 2026 (`ccabc81`) |
| CyberTrooper | v0.7 | 3 Jul 2026 (`754c33b`) |
| Backend | 10 functions, 141 deployments | 16 Jul 2026 |
| Database | 9 tables, 3,007 events, 70 CRM records | Live |

**Days since last production change: 23.**
