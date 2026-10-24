# Point-1 Club · Portfolio Review LP (v2 draft)

**File:** `point1-portfolio-review/index.html` (single file, no dependencies beyond Google Fonts)
**Replaces:** thepersonalcfo.in
**Job:** turn Sharan's "free portfolio review" ads into OTP-verified callback requests from investors with ₹50 L+ portfolios. The managed-strategies (PMS) conversation happens on the call; the page says so plainly.
**Status:** first draft for review. Preview mode is on (no SMS, any 6-digit OTP passes).

---

## What changed in v2 (24 Oct, after Arjun's review)

- **Form in the fold, one screen.** Name, email, one qualifying dropdown (investments today), best time to call, phone with Send OTP inline, six-box OTP inline, submit greyed until verified. Built off the reference card Arjun shared. The modal, the three chip questions and the exit-intent popup are gone.
- **One qualifying question, not three.** "Investments today" in four ranges (₹50 L to 1 Cr, 1 to 5 Cr, 5 Cr+, under 50 L). No SIP, no income. It reads as routing, not prying.
- **30 minutes everywhere.** Call length, steps, FAQ, success screen.
- **No PAN, no CAS, no account aggregator.** The only data line left is "have your apps or statements handy, no passwords, ever".
- **Copy rewritten for a ₹50 L+ investor, 28 to 45.** Hero is the outcome ("Your portfolio, reviewed by a Personal CFO") with three outcome bullets. The ad-mirror pain tiles, "22 funds, mostly the same stocks", "₹25,000 a month in SIPs" and the question hook are gone.
- **"Nothing to buy" is gone.** Replaced with the app's own line, "Free · 30 minutes · No commitment", and a plain "Where this can lead" box that says the Personal CFO may show you Point-1 Club's managed strategies.
- **Gruhas / Nikhil Kamath removed** from the trust strip and the founder bullets.
- **Point-1 Club services section added**, lifted from the in-app Point-1 Club page: full review, investment strategies, tax, insurance, credit cards, private sessions and the circle. This is the aspirational layer.
- **Sharan's full quote** from the app replaces the short one.

## What the page does

| Section | Job | Notes |
| --- | --- | --- |
| Header | Logo + one CTA | Every CTA scrolls to the form. |
| Hero + form | Outcome headline, three outcomes, the request form | Yellow top bar "Limited review slots each week", line "Next available slot: within 48 hours" (both need ops sign-off). |
| Trust strip | SEBI RIA, ₹5,000 Cr+, 3,000+ families, 7L+ members, 30 min | |
| What you walk away with | Sample review card + three outcomes | Sample figures from the Point-1 review doc. |
| What we look at | Six checks with sample stats | Real return, fund quality, allocation, concentration and overlap, cost drag, tax efficiency. |
| How it works | Three steps, 30 minutes | "Where this can lead" box names the managed-strategies conversation. |
| Point-1 Club | Six service cards, dark | From the in-app page. |
| Personal CFO vs bank RM | Comparison table | Stacks into cards on phones. |
| Founder | Sharan module | Portrait slot, full quote, three credentials. |
| Who it's for | Three statements, ₹50 L+ | "Not there yet" line routes to the app's AI CFO. |
| Proof | Sky band, press wordmarks, three quote slots | Slots only. Pull the app's Point-1 Club testimonials or verified quotes. |
| FAQ | Seven questions | Free, who, own strategies, what to bring, minimum, OTP, data. |
| Final band | "One conversation. A plan for the next decade." | Scrolls to the form. |
| Sticky bar | Phone only, shows while the form is off screen | |

## The request flow (inline)

1. Fill name, email, investments today, best time to call (optional).
2. Enter phone, tap **Send OTP**. Six boxes appear under the phone row. Auto-advance, paste, resend after 30s, change number.
3. On a match the phone button turns green "Verified" and **Request a Callback** activates.
4. Submit replaces the form with the received state: reference ID and three next steps.

Lead tier is computed from the one dropdown and sent with the payload: **A** = ₹1 Cr+, **B** = ₹50 L to 1 Cr, **C** = under ₹50 L. A and B see "A Personal CFO calls you within 24 hours"; C sees "Our team calls you within 48 hours" and can be routed to the app's AI CFO review.

## CTA label

One pair of constants, used everywhere. Change them in `P1_CONFIG` and every button updates:

```js
ctaLong: 'Request a Callback',
ctaShort: 'Request a Callback',
```

"Request a Callback" follows the reference form Arjun shared. "Apply Now" or "Click Now" is a two-string swap.

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
  "email": "rohan@company.com",
  "phone": "+919876543210",
  "otp_verified": true,
  "investments": "₹1 Cr to ₹5 Cr", "investments_code": 2,
  "call_time": "evening",
  "tier": "A",
  "source_cta": "hero",
  "utm": { "utm_source": "ig", "utm_campaign": "funds_bleeding" },
  "page": "https://…", "ts": "2026-10-24T10:00:00.000Z"
}
```

OTP provider: MSG91 or Exotel both work with this shape. Rate-limit `otpSendUrl` per number (3 per 10 minutes) and expire codes at 5 minutes.

Tracking: paste the Meta Pixel and GA4/GTM snippets in the head comment. The page fires `Lead` on OTP verification and `CompleteRegistration` on the callback request, and pushes every step to `dataLayer`: `cta_click`, `otp_sent`, `otp_verified`, `otp_resent`, `callback_requested` (with `tier`). Optimise the Meta campaign on `CompleteRegistration`.

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
| Forbes 30 Under 30 Asia, 2023 | Multiple bios | High |
| "A Personal CFO calls within 24 hours" (tiers A and B), "within 48 hours" (C), "Next available slot: within 48 hours", "Limited review slots each week" | Service promises I set | Needs ops sign-off |
| ₹50 L threshold in the tag, "Who it's for" and the FAQ | Arjun's brief, matches the review doc | Confirmed |
| "Where this can lead" box: the Personal CFO may show managed strategies and fees on the call | Arjun's brief | Confirmed |

## What I'd test first

1. **Hero headline.** "Your portfolio, reviewed by a Personal CFO" vs "Know exactly what your portfolio should look like" vs "A second opinion on ₹50 lakh and above".
2. **Top bar.** "Limited review slots each week" vs a dated line like the reference ("Reviews this week: 25 slots"), once ops confirms a real number.
3. **OTP before or after the qualifying dropdown.** Currently the dropdown sits above the phone row. If drop-off shows at the dropdown, move it below the OTP so every verified number is captured first.
4. **Sample card position.** Below the fold (current) vs replacing the three outcome bullets in the hero on desktop.

## Not done in v2

- No WhatsApp CTA. One action across the board. A WhatsApp line on the received screen is a one-line add.
- No testimonial copy. Slots only. The in-app Point-1 Club page already carries client reviews (Radhesh Bhat and others); export those.
- Single theme, by design: the 1% Club website and app define their own dark and light surfaces, so the page does not follow the OS theme.
