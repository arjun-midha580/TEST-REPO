# Point-1 Club · Free Portfolio Review LP (v1 draft)

**File:** `point1-portfolio-review/index.html` (single file, no dependencies beyond Google Fonts)
**Replaces:** thepersonalcfo.in
**Job:** turn Sharan's "free portfolio review" ads into OTP-verified, qualified applications. PMS is pitched on the call, not on the page.
**Status:** first draft for review. Preview mode is on (no SMS, any 6-digit OTP passes).

---

## What the page does

| Section | Job | Notes |
| --- | --- | --- |
| Header | Logo + one CTA | Zero nav links. SEBI RIA pill on desktop. |
| Hero | The question hook + sample output | "What did your money actually return?" uses the winning 1% Club mechanic: a number about them they can't answer today. Right side shows the actual review output (sample figures from the Point-1 review doc). |
| Trust strip | 5 proof points | SEBI RIA, ₹5,000 Cr+ tracked, 3,000+ families, 7L+ members, Gruhas. See "Numbers to confirm". |
| Sound familiar | 4 pain tiles | One tile per ad script: Funds Bleeding, Rahul RM, Decision Making, plus overlap. |
| What the review covers | 8 checks, sample stats | Mirrors the 8 parameters of the Point-1 review. |
| How it works | 4 steps + "the honest part" | Pre-empts the "what's the catch" objection. Says PMS may come up, nothing is sold on the call. |
| Review vs RM | Comparison table | Answers the Rahul RM ad directly. Stacks into cards on phones. |
| Founder | Sharan module | Portrait slot, quote, 4 credentials. |
| Who it's for | Soft qualification | ₹25k SIP / ₹25 L invested / ₹25 L income. Pre-frames the form questions so they don't feel like a filter. |
| Proof | Press wordmarks + 3 quote slots | Quote slots are dashed placeholders. Fill only with verified client quotes. |
| FAQ | 8 conversion blockers | Free, who, passwords, time, minimum, PMS pitch, why OTP, data. |
| Final band | Ad line as headline | "One conversation. The kind you've been avoiding." |
| Sticky bar | Phone only | Appears once the hero CTA scrolls out. |
| Exit intent | One field | Mobile only field, feeds the same OTP flow with the number pre-filled. |

## The application flow (modal)

1. **Name + mobile** → Send OTP
2. **6-digit OTP** → auto-advance, paste, resend after 30s, change number
3. **Three required questions** as tap chips: monthly SIP, invested assets, annual income. Two optional: where the money sits, biggest worry.
4. **Done** screen with reference ID and the three next steps.

Lead tier is computed client-side and sent with the payload so routing can happen before anyone reads the lead:

- **A** → ₹50 L+ invested, or ₹50k+ monthly SIP, or ₹50 L+ income with ₹25 L+ invested. Done screen says "A Personal CFO calls within 24 hours".
- **B** → ₹25 L+ invested, or ₹25k+ SIP, or ₹25 L+ income.
- **C** → everything else. Done screen says "Our team calls within 48 hours".

## CTA label

One constant, used everywhere. Change it in `P1_CONFIG` and every button updates:

```js
ctaLong: 'Apply for My Free Review',
ctaShort: 'Apply Now',
```

The brief said "Click Now". I went with "Apply" because the page positions the review as something you qualify for, which matches the invite-only tone of the Point-1 review doc and feeds the qualification questions naturally. Swap the two strings above if you want the literal copy.

## Integration (for the developer)

Set `window.P1_CONFIG` before the page script, or edit the defaults in the file:

```js
window.P1_CONFIG = {
  demo: false,
  otpSendUrl:   '/api/otp/send',    // POST {phone, name, utm}  → {ok:true}
  otpVerifyUrl: '/api/otp/verify',  // POST {phone, otp}        → {ok:true, token}
  leadUrl:      '/api/lead',        // POST payload below       → {ok:true, ref:'P1-123456'}
};
```

Lead payload:

```json
{
  "name": "Rohan Malhotra",
  "phone": "+919876543210",
  "otp_verified": true,
  "sip_monthly": "₹50,000 to ₹1 L", "sip_code": "3",
  "liquid_assets": "₹1 to 5 Cr", "assets_code": "4",
  "annual_income": "₹50 L to 1 Cr", "income_code": "3",
  "holdings": ["mf", "stocks", "fd"],
  "concern": "toomany",
  "tier": "A",
  "source_cta": "hero",
  "utm": { "utm_source": "ig", "utm_campaign": "funds_bleeding" },
  "page": "https://…", "ts": "2026-10-24T10:00:00.000Z"
}
```

OTP provider: MSG91 or Exotel both work with this shape. Rate-limit `otpSendUrl` per number (3 per 10 minutes) and expire codes at 5 minutes.

Tracking: paste the Meta Pixel and GA4/GTM snippets in the head comment. The page fires `Lead` on OTP verification and `CompleteRegistration` on submit, and pushes every step to `dataLayer`: `cta_click`, `otp_sent`, `otp_verified`, `otp_resent`, `application_submitted` (with `tier`), `exit_intent_shown`. Optimise the Meta campaign on `CompleteRegistration`.

## Assets needed from 1% Club

- [ ] Point-1 Club and 1% Club logo SVGs (replaces the typographic lockup in header and footer)
- [ ] Sharan portrait, 4:5, studio or a still from the founder film
- [ ] Press logos: Forbes, Inc42, Entrepreneur India, Storyboard18, BW CFO World (currently text wordmarks)
- [ ] 3 verified client quotes with name, city and consent (Trustpilot for thepersonalcfo.in, or call recordings)
- [ ] SEBI RIA registration number, legal entity name, registered office, links for Privacy, Terms, Investor charter, Grievance redressal
- [ ] OG image (1200×630) for WhatsApp and Meta previews

## Numbers to confirm before launch

All of these are on the page. Each needs a yes from the client or a source.

| Claim | Where it came from | Confidence |
| --- | --- | --- |
| SEBI Registered Investment Adviser | Inc42, Entrepreneur India, Feb 2025 | High |
| ₹5,000 Cr+ wealth tracked | Point-1 review doc and the Pro checkout page | Medium. Public press says ₹2,000 Cr AUA and ₹4,000 Cr tracked. Pick one wording. |
| 3,000+ families advised | Point-1 review doc | Medium |
| 7L+ 1% Club members | Pro checkout page trust strip | Medium. App launch decks say 6L+ users. |
| Backed by Gruhas, Nikhil Kamath's fund | Inc42 coverage | High |
| Forbes 30 Under 30 Asia, 2023 | Multiple bios | High |
| "A Personal CFO calls within 24 hours" (tier A) and "within 48 hours" | Service promise I set | Needs ops sign-off |
| ₹25 L / ₹25k thresholds in "Who it's for" | My call, softer than the review doc's ₹50 L | Client decision |
| "Nothing is bought or sold on the call" | From the ad scripts | Needs sales sign-off |

## What I'd test first

1. **Hero headline.** "What did your money actually return?" vs the ad line "Your bank doesn't care about your portfolio" vs "Nothing to buy. No course. No membership." The first is the strongest on the 1% Club message filter, but the ad line has the tightest message match.
2. **Form position.** Modal on click (current) vs an inline form in the hero on desktop. Modal keeps the hero clean and works on phones; inline usually wins on desktop for cold traffic.
3. **Qualification before or after OTP.** Currently after, so every verified number is captured even if they abandon the questions. If lead quality is the problem, move the three questions before the OTP.
4. **Thresholds.** ₹25 L vs ₹50 L in "Who it's for". Higher cuts volume, lifts tier-A share.

## Not done in v1

- No WhatsApp CTA. The brief said one action across the board. Easy to add a WhatsApp fallback in the done screen if the ops team wants it.
- No testimonial copy. Slots only. I won't invent client quotes on a SEBI-regulated page.
- Light theme only, by design. The review doc is a white-world document and the LP matches it.
