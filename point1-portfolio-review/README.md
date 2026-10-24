# Point-1 Club · Portfolio Review LP (v3.1 draft)

**File:** `point1-portfolio-review/index.html` (single file, no dependencies beyond Google Fonts)
**Replaces:** thepersonalcfo.in
**Job:** turn Sharan's "free portfolio review" ads into OTP-verified callback requests from investors with ₹50 L+ portfolios. The managed-strategies (PMS) conversation happens on the call; the page says so plainly.
**Status:** first draft for review. Preview mode is on (no SMS, any 6-digit OTP passes).

---

## What changed in v3.1 (24 Oct, after Arjun's note)

- **"Personal CFO" is gone from the page.** It clashes with the AI CFO positioning. Every mention is now "wealth expert", "the Point-1 Club team" or "the team that manages ₹5,000 Cr+". The one remaining "CFO" on the page is the BW CFO World press wordmark.
- **The hero pitches time.** "You don't have time to manage your money. We do." then "30 minutes. Every holding. The exact steps, written." and "By the team that manages ₹5,000 Cr+. SEBI-registered."
- **The time angle runs through the page.** Steps: "Thirty minutes of your time. The rest is ours." What changes: "Funds, stocks, deposits, PMS, property. Nobody with a day job has the time to keep all of it on one plan. The review does, and hands you the steps." Who it's for: the third tile is now "You don't have the time".
- **"The exact steps"** replaces "a plan" wherever the written output is mentioned.
- **Trust strip** now reads "₹5,000 Cr+ wealth managed" (was "tracked"), per Arjun. Public press says ₹2,000 Cr under advisory and ₹4,000 to 5,000 Cr tracked on the platform, so this wording needs the client's sign-off before launch.

## What changed in v3 (24 Oct, mobile-first pass)

- **Hero cut to three lines.** Tag, headline, "30 minutes. Every holding. A written plan.", three micro words, then the form. A compounding line draws itself across the hero on load (brand yellow, with a soft glow and a pulsing endpoint). On phones it sits behind the headline.
- **The gap chart (new section 2).** ₹1 Cr over five years at three rates: the sample portfolio (7.0%), the market (13.5%) and the Point-1 Club strategy (27.0%, the five-year CAGR from the review doc). Curves draw in sequence on scroll, end values count up, a ₹1 / ₹2 / ₹5 Cr toggle rescales the labels, hovering a legend row highlights its curve. Disclaimer under it. The 27% line is the client's own number; it needs compliance sign-off because it is a PMS performance figure on a public page.
- **Six readings (what we look at).** Each check has its own micro-visualisation: return bars, a 22-square fund-rating grid, an allocation stack, a 35-dot concentration map, a cost-drag counter, after-tax bars. All animate on scroll.
- **Three steps on a dotted path.** Blue dotted line that draws itself through three yellow nodes: a wave on desktop, a vertical line on phones. One line of copy per step.
- **Before and after (new).** Four counters from the sample plan: funds 22 to 6, stocks 35 to 18, weak-rated money ₹13.8 L to ₹0, deposit income 4.6% to 7.0% after tax. Replaces the bank-RM comparison table.
- **Removed:** the comparison table, the Point-1 Club services section, the "Where this can lead" box, the "Will you recommend your own strategies" FAQ, the "Not there yet, AI CFO" line, the "wealth arm of 1% Club" sentence after Sharan, and the three outcome bullets in the hero.
- **Reviews:** a swipe carousel on phones (snap scroll, dots), three-up on desktop, with the brand's pixel quote mark, avatar initials and green stars.

**Animation references used:** Robinhood and Linear for the self-drawing line charts (stroke-dashoffset), Apple's keynote counters for the number count-ups, Stripe's dotted connectors for the step path, the 1% Club website's own staggered reveals for the grids.

## What the page does

| Section | Job | Animation |
| --- | --- | --- |
| Header | Logo + one CTA | |
| Hero + form | Headline, one line, the request form | Compounding line draws on load |
| Trust strip | SEBI RIA, ₹5,000 Cr+, 3,000+ families, 7L+ members, 30 min | |
| The gap | ₹1 Cr, five years, three rates | Curves draw, values count, toggle rescales |
| Six readings | The checks, one micro-viz each | Bars, grid, stack, dots, counter |
| Three steps | Request, review, plan | Dotted path draws through the nodes |
| What changes | Sample plan before and after | Counters run from before to after |
| Founder | Sharan, full quote, three credentials | |
| Who it's for | Three statements, ₹50 L+ | |
| Proof | Sky band, press, three quote slots | Swipe carousel on phones |
| FAQ | Five questions | |
| Final band | "One conversation. A plan for the next decade." | |
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
| ₹5,000 Cr+ wealth managed, and "the team that manages ₹5,000 Cr+" | Arjun's brief, matches the Point-1 review doc | Medium. Public press says ₹2,000 Cr under advisory and ₹4,000 to 5,000 Cr tracked. Client sign-off needed. |
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
