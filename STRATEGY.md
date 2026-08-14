# Season KL — Strategy & System Alignment

**Source of truth:** *Season KL: Digital Marketing Planning* (Season KL × 3Sixty Marketing, Jul–Sep 2026).
This document maps that marketing plan onto the live system (website · Supabase · Notion) so all three
move as one. Where the website narrative (Bloom/Pulse/Glow) and the plan meet, this is the bridge.

---

## 1. North Star
**300 pax every Friday & Saturday by end of September 2026.** Not a hope — a number reverse-engineered
every week. Monthly ramp: July 150–200 → August 220–270 → September 280–320 (Fri 195–265 / Sat 265–340).

## 2. The 7 Crowd Streams (the engine)
Run simultaneously, every Friday, and 300 is inevitable:
1. **Filler Crowd** — Notion CRM online→venue pipeline (180–220 pax/weekend, ≥30 new contacts/week)
2. **Brand Ambassadors** — in-house promoter army (Petal / Bloom / Season)
3. **KOL Ecosystem** — weekly Fri/Sat activation, 6-week creator rotation
4. **Big Spenders** — VIP table conversion
5. **Walk-ins** — TREC KL foot traffic + social proof
6. **Friends of Ambassadors** — referral network
7. **Community Partnerships** — Chinese, Korean & local groups *(this is why the site is multilingual:
   EN / 简体中文 / 한국어 / 广东话)*

## 3. Filler Crowd Pipeline (5 stages)
IDENTIFY → OUTREACH → NURTURE → CONFIRM → ATTEND → (RETAIN).
Live flow: new follower tagged "New Lead" → auto-DM in 24h → personalised Season Invite Pass + welcome
shot + priority entry → WhatsApp confirm (24h + 3h reminders) → greeted by name + checked in →
post-visit follow-up → path to Ambassador after 2nd visit.

## 4. Ambassador Programme — Petal · Bloom · Season
Only **verified** guests count. (The site uses **Bloom** for the middle tier — matching this plan and the
Notion Ambassador Tracker. The earlier "Ember" label was a drift and has been corrected.)

| Tier | Qualify | Rewards | Target |
|------|---------|---------|--------|
| 🌸 **Petal** (Entry) | 5 guests/active night · 2 posts/mo | 20% F&B · free entry · member card · priority WhatsApp invites | 30–50 by Aug |
| 💮 **Bloom** (Active Promoter) | 10+ guests · 3+ nights/mo · 4 posts/mo (IG/Threads/WA) | **RM20 commission/guest** · **RM200/mo F&B credit** · priority table · VIP+RM50 on milestone | 15–20 by Sep |
| 🌹 **Season** (Power Host) | 20+ guests · hosts named nights · runs sub-network | **8% revenue share** · RM1,000–1,500 retainer · featured · auto-upgrade | 3–5 by Q3 |

Ops cadence: **Mon** group briefing (theme + message + invite copy) · **Wed** table check (confirmed
Fri/Sat) · **Sun** Notion performance review + top performer celebrated.

## 5. KOL Ecosystem (6-week rotation, measurable ROI)
Wk1 Nightlife photographer · Wk2 Lifestyle vlogger · Wk3 F&B/Shisha reviewer · Wk4 Music/DJ creator ·
Wk5 Couples/date-night · Wk6 Girl-group creator. Deliverables: 1 IG Reel/48h + 3 Stories + 1 TikTok
(@season_kl_official) + Xiaohongshu (RM100 bonus). Rates: Nano RM300–600 · **Micro RM500–2,000 (sweet
spot, 4–5/week)** · Mid RM2,000–5,000. Judged on **cost per verified attendee**, not views. 2 KOLs/week.

## 6. Three-Month Themed Concept Cycle (the Bloom/Pulse/Glow story)
*One continuous story. Every month a new chapter; every chapter, a fuller room.*
- **JULY · 🌸 BLOOM — "Connection over everything."** Orchid · Dusty Pink · Moonflower.
  Signature: **Bloom & Connect Singles Night** (WhatsApp reg · 150 new contacts/event · ladies free · RM30 men redeemable).
- **AUGUST · 🔥 PULSE — "Peak energy. Season KL is alive."** Violet · Coral · Jet.
  Signature: **Pulse Night Open Format Takeover** + **Expat Pulse Night** (InterNations KL · 200 expat contacts/event).
- **SEPTEMBER · ✨ GLOW — "You've earned your place."** Champagne · Midnight · Rose Chrome.
  Signature: **Glow Up Night — Invite Only** + **Glow Muse Photo Night** (50–80 creators/month from Sept).

## 7. First Visit Pass — the viral loop
"You've been invited to bloom." Each first-timer gets a unique QR pass + **Bloom Potion** (edible-flower
welcome cocktail) + **"My First Season" Polaroid** + **Season Token** (acrylic collectible).
Loop: **Pass It On** (show a stranger → bonus shot) → **Post Your Pass** (IG/Xiaohongshu → DM → Season
Family Wall) → **Pass Forward** (receive 2 passes to gift). Targets: redemption 40–60%, repeat 30%.

## 8. Acquisition funnel & engagement perks
Awareness (Meta + TikTok, 15-sec Reels, cold KL 21–38) → Consideration (ManyChat auto-DM ≤24h) →
Activation (digital Invite Pass, welcome drink, priority before 11PM) → Conversion (WhatsApp confirm,
24h+3h reminders, table upgrade) → Retention (post-visit loop, Petal onboarding after 2nd visit).
Zero-cash perks: Follow → welcome shot · Share to Story → free entry that weekend · Tag 2 friends →
2-for-1 house cocktail · Post a Reel + tag → featured. Reply-rate goal 30–40%. Budget RM4,300–5,800/mo.

## 9. Weekly content engine
Mon DJ Spotlight · Tue Weekend Recap Reel · Wed Story Poll · Thu Event Teaser/Urgency · Fri Doors-Open
Live · Sat In-Night Stories · Sun Recap. Channels: IG · TikTok · Xiaohongshu.

## 10. Launch roadmap
Foundation (Jul 16–22) → Build (Jul 23–Aug 2, soft open Jul 25–26) → Momentum (Aug 3–16) →
Pre-launch (Aug 17–28) → **Grand Launch (Aug 29–30 / Sep 5–6): 250–300 pax, all 7 streams live.**

---

## System alignment map (website ↔ Supabase ↔ Notion)
The live funnel: **website forms → Supabase → `issue-season-pass` / sync → Notion**. One system; no layer
ahead of another. Changes are additive-first (add new, dual-write, verify, retire old) so no signup is
lost while campaigns run.

| Plan concept | Website | Supabase | Notion |
|--------------|---------|----------|--------|
| First Visit Pass | `explorers.html` form | `season_passes` | First Visit Pass DB `b9f391ba…` + Guest CRM `cecce125…` |
| Ambassador tiers | `icons.html` (Petal/**Bloom**/Season) | `ambassador_signups.tier_interest` | Ambassador Tracker `b9d4f85e…` (Tier select) |
| KOL | `tastemakers.html` | `kol_signups` | KOL tracker |
| Lead Source / 7 streams | *(gap → adding)* | `lead_source` | CRM `Lead Source` select |
| Visit Intent / Guest Count | *(gap → adding)* | *(adding)* | CRM `Visit Intent` / `Guest Count` |
| Pipeline stages | — | — | CRM `Pipeline Stage` (Identify→…→Retained) |

**Notion status: ~95% aligned** — it already runs the 7-stream Lead Source model, the full pipeline, the
Petal→Bloom→Season→Champion→Micro-KOL flywheel, a Proof Chain (Captured→Supabase Synced→WhatsApp
Confirmed→Door Verified→Reported), Door Check-Ins, VIP tables, Weekly Reports.
**Open gaps being closed:** (a) the web form hardcodes source → every lead reads "IG DM"; add real
**Lead Source**, **Visit Intent**, **Guest Count** capture (additive); (b) **Door QR check-in** front-end
to populate verified attendance.

---

## Steve Jobs verdict — the plan vs. the system
They are not competitors. **The plan is the script; the system is the stage.**
- *3Sixty marketing plan:* **9/10 vision** — complete strategy, but a deck. It states the destination.
- *The system (site + Supabase + Notion):* **9/10 engine** — real, running, already executing the plan;
  the Notion CRM is arguably ahead of the deck (proof-chain + door verification). As shipped it was held
  to ~6/10 by two faults: the live site was dark (deploy broken) and one tier wore the wrong name (Ember).
- **Verdict:** the system is the more valuable asset *because it is real* — but only worth its weight if
  it mirrors the plan exactly. Both faults are now fixed. Don't choose between plan and system; keep the
  plan as the brief and hold the system to it, so the gap between them disappears.
