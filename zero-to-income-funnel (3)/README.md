# Zero-to-Income Blueprint: Sales Funnel

Two dependency-free static pages. No build step, no framework, no secrets.

```
index.html            -> "/"           (sales page)
thank-you/index.html  -> "/thank-you"  (post-payment page)
```
Upload the folder to any static host (Netlify, Vercel, Cloudflare Pages, GitHub Pages). `/thank-you` resolves automatically.

## 1. Add your payment link
Open `index.html`, find `const CONFIG = {` near the bottom and set:
- `PAYMENT_URL` to your checkout link (all 6 buttons use it)
- `PRICE` (e.g. `"$19"`), `ORIGINAL_PRICE` (leave `""` to hide it)
- `SUPPORT_EMAIL`
- `PRIVACY_URL` / `TERMS_URL` already point to `/privacy/` and `/terms/` (see below)

Until `PAYMENT_URL` is set, buttons show a notice instead of going anywhere. No payment is simulated.

## 2. Add your ebook download link
Open `thank-you/index.html`, find `const CONFIG = {` and set `DOWNLOAD_URL` (and `SUPPORT_EMAIL`).
Use a direct PDF link. See section 4 before using a plain public link.

## 3. Set the redirect in your payment gateway
In your gateway's checkout / payment-link settings, find "success URL", "redirect after payment" or "callback URL" and enter
`https://YOUR-DOMAIN/thank-you`. Deploy first, then use the live address. Test with your gateway's test mode.

## 4. Payment verification and secure delivery (not built yet)
**Visiting `/thank-you` does not prove payment.** Anyone can type the URL, and a public PDF link can be shared.
The page makes no such claim and is ready for these upgrades:

1. Keep the PDF in private storage (not in this folder).
2. After payment, have the gateway redirect to a small server function with a session or payment ID.
3. That function verifies the payment server-side with your gateway's secret key (kept only on the server; check the gateway docs for its verification method or signature check).
4. If valid, redirect to `/thank-you?token=...` where the token is a short-lived signed value tied to that order, or generate an expiring signed file URL (e.g. 10-15 minutes).
5. Also use a gateway webhook to record orders and email the buyer their link (covers closed tabs).

Hooks already in `thank-you/index.html`:
- `REQUIRE_TOKEN: true` hides the download unless `?token=` is present
- `DOWNLOAD_URL` may contain `{token}`, replaced with the URL token

The token check on the page is only a convenience. Real protection must be enforced by your server when it serves the file.

## Notes
- The page describes the 27-page guide. The PDF is supplied separately and should be delivered through a protected download method.
- No testimonials, customer counts, timers or discounts are shown.
- Not set yet: social share image (add `og:image` with an absolute URL after deploying), real privacy/terms pages.
- Tested in headless Chromium at 1366, 820 and 390 px wide: no console errors, no horizontal scroll, CTAs, FAQ accordion and sticky mobile bar work. Not tested on physical devices or other browsers.

## Privacy and Terms pages
`privacy/index.html` and `terms/index.html` are templates linked from the footer. Replace every highlighted `[PLACEHOLDER]` (business name, payment provider, refund policy, governing law, email, dates) and have a qualified professional review them. They are not legal advice. Delete the yellow notice box once complete.

## Added brand assets
- `assets/zero-to-income-ebook-cover.webp` — updated eBook mockup used in the hero and product showcase.
- `assets/founder-naman.jpg` — founder portrait used in the author section near the bottom of the sales page.

## Before publishing
The payment and support details in `index.html` are still placeholders. Set `PAYMENT_URL`, `PRICE`, and `SUPPORT_EMAIL` in the `CONFIG` object before accepting orders. The existing thank-you page also needs its `DOWNLOAD_URL` configured, and the privacy/terms templates need their placeholders completed. The payment verification and secure PDF delivery workflow described above is not implemented in this static template.


## Configuration status (prepared for Naman Pahuja / Digitex)

- Suggested launch price set to `₹199` (change it in `index.html` if needed).
- Support email set to `support.digitex@gmail.com`.
- `PAYMENT_URL` is intentionally still a placeholder. Add your real payment checkout link before publishing.
- `DOWNLOAD_URL` on `/thank-you/` is intentionally still a placeholder. Do not expose the paid PDF as a public static asset if you want paid-only access.
- This is a static website. A static thank-you URL does not verify payment. For protected delivery, use a payment-provider webhook/server function and private file storage, then issue a short-lived download link.
- Privacy and terms pages are templates and still require your real business/payment-provider details and a suitable refund policy before launch.


## Razorpay link connected

The main landing-page purchase buttons now open the supplied Razorpay Payment Link: https://rzp.io/rzp/fK7uOeB

Important launch requirements still outstanding:
- Confirm in Razorpay Dashboard that this link is active, for the correct eBook, and charges the intended price (the page displays ₹199). The short link could not be independently inspected from this environment.
- Configure the Payment Link's successful-payment redirect in Razorpay to your deployed `/thank-you/` URL, if you want a browser redirect. Razorpay documents the redirect/callback option in its Payment Link settings/API.
- Configure a server-side webhook for `payment_link.paid`, verify Razorpay's signature, and only then issue a secure, expiring download token. Do not treat a visit to `/thank-you/` as proof of payment.
- The thank-you download remains locked (`REQUIRE_TOKEN:true`) until a verified backend and private PDF storage are configured. No API keys or webhook secrets belong in frontend HTML.
- The supplied eBook PDF is a separate deliverable and is not publicly exposed in this website ZIP.
