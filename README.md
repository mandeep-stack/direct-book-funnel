# direct-book-funnel

Cold-traffic landing page for the ₹199 audit call, served at `book.adcomp.xyz`.
Single static file: `index.html` (Cloudflare Pages, no build step).

## Flow
Ad → landing page → any "Book Audit" button (9 on the page) → booking form → `adcomp-audit-worker` `/create-order` → Razorpay popup → `/verify-payment` → redirect to the scheduler.

## Tracking
- `book_audit_call` fires on every Book Audit click through `awTrack()`: dataLayer push (GTM Pixel tag) plus `meta-capi-worker` (server), same `event_id`. Each button sends a `placement` (`nav`, `hero`, `after_problem`, `after_process`, `after_guarantee`, `offer_card`, `after_who_for`, `final`, `sticky`).
- `audit_call_form_submit` fires on form submit (dataLayer).
- PageView goes to `https://adcomp.xyz/capi/event`.
- Server-side Purchase is sent by `adcomp-audit-worker` after payment verification.
- GTM container `GTM-W95RB6L`, same as speed.adcomp.xyz.

## Before sending ad traffic here
1. **audit worker CORS:** add `https://book.adcomp.xyz` to the allowlist in `adcomp-audit-worker` (live code in the `adcomp-speed-funnel` repo, `audit-worker/worker.js`) and redeploy. Without it the form shows "Load failed".
2. **GTM:** the `book_audit_call` trigger and the GA4/Meta "(speed)" tags are limited to hostname `speed.adcomp.xyz`; allow `book.adcomp.xyz` too, and exclude `book.adcomp.xyz` from the old click-text tags so nothing double-counts.
3. **Cloudflare Pages:** project connected to this repo, production branch `main`, no build command, output directory `/`, custom domain `book.adcomp.xyz`.
