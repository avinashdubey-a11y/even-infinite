# Even Infinite — Onboarding prototype

Static, mobile-first HTML/CSS/JS prototype of the **Even Infinite** activation flow.

Every page is a single self-contained `.html` file with inlined CSS and base64-encoded SVG assets, so any one of them can be opened standalone — no build step, no bundler, no framework. Navigation is plain `<a href>` and `window.location.href`.

## Pages & flow

```
index.html         → landing
  └ (Get Even Infinite for free → location permission modal)
details.html       → name + 10-digit mobile + city
  └ (city ≠ Bengaluru → "not available in your city" modal + Notify-me waitlist)
otp.html           → 6-digit OTP entry, auto-advance, 0:23 resend timer
insurance.html     → "How do you currently have health insurance?"
  └ (no insurance link → "Health insurance required" info modal)
insurer.html       → pick partner insurers (Digit / Aditya Birla / Manipal Cigna) or Other
  └ (only Other selected → "We'll try to onboard your insurer soon" + waitlist)
  └ Add / edit family members (name, relation chips, mobile)
policy.html        → per-insurer policy number or document upload, consent
  └ "Don't know how to find?" → policy-help.html
policy-help.html   → tabbed "Self purchased" / "From employer" with 3 + 4 methods
success.html       → "Our team is reviewing your details" + status timeline + App Store / Play Store / QR
```

Plus:

- **admin.html** — read-only viewer for everything submitted on this device. Lives at `/admin.html`, not linked from the flow. Shows a table with filters, search, raw-JSON inspector, JSON export, and a clear-all button.
- **result.html** — older "not available" page used as the destination for an earlier flow variant; superseded by the in-page modal on `details.html`.

## State

The flow uses `sessionStorage` for in-progress values (`evenName`, `evenMobile`, `evenCity`, `evenInsuranceSource`, `evenInsurers`, `evenCustomInsurers`, `evenMembers`) so back-navigation restores the form, and `localStorage.evenApplications` for terminal events:

| Event | Status |
| --- | --- |
| Submit application | `submitted` |
| Notify me · city not available | `waitlist_city` |
| Notify me · insurer not supported | `waitlist_other_insurer` |

## Running locally

It's plain static files, so any static server works. The included helper:

```bash
python3 .claude/serve.py 5179
# → http://127.0.0.1:5179/index.html
```

Or any equivalent (`npx serve`, `python3 -m http.server`, Caddy, nginx, etc.).

## Assets

The `assets/` folder holds the SVG illustrations and brand logos shipped from Figma. The HTML files also inline each asset as a base64 data URI so individual pages can be opened standalone over `file://` without missing-image errors.

## Caveats

- The "upload document" picker on `policy.html` is wired to real `<input type="file">`, but only the file *name* and *size* are stored (no bytes are persisted) — this is a UI mock for the activation flow, not a real upload pipeline.
- `localStorage` is per browser per device, so the admin viewer only shows attempts made on the same device.
- Typography uses "Centra No2" with system fallbacks — the proprietary face is not bundled.
