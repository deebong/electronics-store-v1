# BYOS — Storefront, PC Configurator & Admin Portal

Single-page app for a custom desktop PC build and accessories store (Coimbatore, Tamil Nadu).
No build step, no framework, no dependencies.

## Files

| File | Purpose |
|---|---|
| `index.html` | The entire app — markup, CSS and JS in one file |
| `manifest.webmanifest` | PWA install manifest (name, icons, shortcuts, theme) |
| `sw.js` | Service worker — offline app shell, cache-first assets |
| `icons/` | PWA + Apple touch icons (`icon-192/512`, `maskable-192/512`, `apple-touch-icon`) |

## Run it

```bash
python3 -m http.server 8000        # or: npx serve .
```

Open `http://localhost:8000/index.html`.

PWA install, the service worker and Google Fonts all need **http(s)** — opening the file via
`file://` works for the store itself, but the browser will not offer "Install app" and the
service worker will not register. Deploy to any static host with HTTPS
(Netlify, Vercel, Cloudflare Pages, GitHub Pages) for the installable/PWA experience.

Admin portal PIN in demo: **1234**.

## Data modes

* **Demo Mode** (default) — localStorage, seeded with 45 products and 5 orders. Everything
  works offline; uploads, orders, quotes and settings persist in the browser.
* **Sheets Sync Mode** — Admin → Settings → paste your Apps Script *Web App* URL and switch the
  mode. `ApiService` then issues `fetch(GAS_WEB_APP_URL, …)` against the live sheet.
  The sheet column schema and a ready-to-paste `Code.gs` are in
  Admin → Settings → *View Schema & Code.gs*.

Deploying the backend:

1. Create a Google Sheet with tabs `Products`, `Orders`, `Customers`, `Settings`.
2. Extensions → Apps Script → paste `Code.gs` → Deploy → New deployment → Web app,
   execute as **Me**, access **Anyone**.
3. Copy the `/exec` URL into Admin → Settings → Apps Script Web App URL.
4. Click **Push local data to Sheets** to seed the sheet, then switch to Sync Mode.

## Round-3 features

* **Working UPI QR** — the checkout UPI option now renders a real, scannable QR code
  (embedded QR encoder, no dependency) encoding `upi://pay?pa=<UPI ID>&pn=…&am=<exact total>&cu=INR`.
  The amount is baked into the code, and there are *Copy UPI ID*, *Open UPI App* and
  *Copy UPI Link* actions. Verified decodable by a real QR reader. Set the VPA in
  Admin → Settings → *Store UPI ID*.
* **Product pages** — clicking any product card opens `#product-<id>` with a photo gallery
  (multiple uploaded images + thumbnails), a full specification table, product details
  (SKU, HSN, GST rate, barcode, serial tracking, socket/RAM/form factor/wattage),
  quantity picker, Add to Cart / Buy Now and related products. Admin → Products → Edit
  supports multi-image upload (multiple files at once) or pasting image URLs.
* **Pre-Built PCs page** — a dedicated `#prebuilt` view with tier cards (Valor / Titan /
  Creator), a side-by-side comparison table and a *Customise in PC Builder* shortcut.
  Any product in the `Pre-Built PCs` category appears here automatically.
* **YouTube feed** — the storefront ends with a *Latest from BYOS on YouTube* rail
  (recent uploads with thumbnails, dates and view counts) plus Visit/Subscribe buttons,
  and a Google rating strip (4.8 · 1,295 reviews) with the store tagline.
* **Real store identity** — footer, contact page and invoices use the real Peelamedu
  address, both mobile numbers (**75300 15155 / 75300 15155**), the official YouTube /
  Instagram / Facebook links and the Google Maps place link + embed. Admin → Settings →
  *Contact Numbers* lets you add/remove any number of contacts; *Google Reviews* edits the
  curated review cards shown on the contact page (the 4.8 / 1,295 summary links to the
  live Google listing).
* **Selects** — all dropdowns use a custom hairline chevron pinned to the right edge
  (no more OS arrow clipping the text).
* **GST tables** — invoices and quotations now use a single clean `.gst-table` style:
  header row, right-aligned mono numbers, zebra rows, and a proper `<tfoot>` for
  Taxable Value / CGST / SGST / Grand Total. On phones the table stacks into labelled
  rows; in print it stays a clean A4 table.

## Product artwork

Every product renders a generated SVG "catalogue plate" drawn from its own data
(socket, form factor, fan count, wattage, AIO vs air, mesh vs solid front), so there are no
hotlinked stock photos to break and imagery works fully offline. Admin → Products → Edit lets
you paste an image URL or upload a photo instead — uploads are downscaled to 900 px JPEG in a
canvas so they stay inside the localStorage budget.

## Branding

Admin → Settings → *Branding & Store Identity*: store name, email, address, tagline,
Google rating/review count, logo upload,
favicon upload, accent colour, heading font (Space Grotesk / Plus Jakarta Sans / Poppins /
Inter), light or dark template, WhatsApp number and social links. Changes apply immediately to
the storefront, invoices, quotations and the PWA icon/theme colour, and are saved to
localStorage (pushed to the `Settings` sheet in Sync Mode).

## Notes for production

* Replace the PIN gate with real auth (Google Workspace SSO / Firebase Auth).
* Put live Razorpay keys behind your server; the checkout here is a working placeholder flow
  (Razorpay / UPI intent / NEFT / store pickup) that creates real order records.
* `sw.js` is versioned by the `CACHE` constant — bump `byos-v3` when you ship, so clients
  drop the old shell.
