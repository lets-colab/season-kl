# Appendix D — DM Outreach Proof of Work

**Status:** ⚠️ awaiting assets.

Instagram DMs are not connected to this stack and cannot be retrieved by any automation in the system. Screenshots must be supplied manually.

---

## How to add proof

Drop image files in this directory using this naming convention:

```
YYYY-MM-DD__<ig_handle>__<stage>.png
```

`<stage>` is one of: `sent` · `opened` · `replied` · `converted`

**Examples**
```
2026-07-14__kl_foodie_jane__sent.png
2026-07-15__kl_foodie_jane__replied.png
2026-07-16__nightlife_amir__sent.png
```

Then fill in `index.csv` (below) so each screenshot is attributable rather than merely present.

---

## `index.csv` schema

Create `index.csv` in this directory with these columns:

| Column | Meaning |
|---|---|
| `file` | screenshot filename |
| `handle` | IG handle contacted |
| `date_sent` | date of first contact |
| `campaign` | campaign name (e.g. `Season Inner Circle — First Chapter`) |
| `sender` | who sent it |
| `stage` | sent / opened / replied / converted |
| `outcome` | free text |
| `pass_id` | linked `season_passes.pass_id` if they converted |

The `pass_id` column is the important one — it is what turns a screenshot into **attribution**. Without it a screenshot proves a message was typed; with it, it proves a message produced a guest.

---

## Redaction requirement

Before adding any screenshot, redact:

- Phone numbers and email addresses
- Any part of the conversation not related to the Season KL invitation
- Third-party names appearing incidentally

These are real people's private messages. The report goes to management, and possibly beyond it.

---

## A recommendation worth reading before collecting these

Screenshots are the weakest form of proof available to this business, for three reasons:

1. **Unverifiable** — a screenshot can be edited, staged, or duplicated. It proves nothing that a determined sceptic would accept.
2. **Unattributable** — it shows a message was sent; it cannot show that the message produced a guest, revenue, or a return visit.
3. **Doesn't scale** — 20 prospects is 40+ screenshots to collect, redact, name, and index. At 200 prospects it becomes a job nobody does.

**The system already built this month produces strictly better proof.** `operations/outreach/prospects.csv` + `dm-tracker-match` (v4) + `dm-link-open` (v2) generate a timestamped, queryable, auditable trail: prospect identified → DM sent → link opened → pass claimed → pass redeemed. That chain survives scrutiny in a way an image cannot, and it costs nothing extra because it is already live.

It has produced no data because `prospects.csv` has never been filled in — see report §5.2.

**Therefore:** supply the screenshots for July, since that is the only evidence that exists for work already done. But treat this directory as a transitional artifact. From August, the tracker should be the proof, and this folder should stop growing.
