# VELORA — Fashion Storefront

![VELORA campaign](assets/banner.jpg)

**Wear your next move.** A complete fashion storefront that runs entirely in the browser — 36-product catalog, cart and checkout flow, and a voice + text shopping assistant. No backend, no database server, no paid APIs.

🔗 **Live:** https://velora-shanawar.netlify.app/
💾 **Full source code (Google Drive):** https://drive.google.com/drive/folders/1xKaNVPvahP46kFUKXuvrZXaa8Yn-BORj?usp=sharing

![Status](https://img.shields.io/badge/status-live-brightgreen) ![Type](https://img.shields.io/badge/type-frontend--only-blue) ![Backend](https://img.shields.io/badge/backend-none-lightgrey) ![Payments](https://img.shields.io/badge/payments-simulated-orange)

## What it does

- **Catalog** — 36 JSON products across 12 categories, with filters, sorting, search, full product pages and quick view.
- **Commerce** — LocalStorage cart with stock limits, coupons, wishlist, profile preferences and demo order history.
- **Checkout flow** — information, shipping, simulated payment, review and order-success steps. No payment details are ever collected; checkout addresses stay in memory only.
- **Voice + text assistant** — a shared text and speech command engine (`processAssistantCommand()`) powers both typed requests and voice: browser speech recognition and synthesis, voice status UI, and graceful error fallbacks. Tests cover the 17 requested assistant phrases.
- **Outfit builder** — four-piece outfit builder with budget-aware recommendations and editable sizes and colours.
- **3D touches** — an optional real Three.js hanger scene; sneakers and bags use photographic 3D tilt stages.
- **Craft details** — responsive layouts, keyboard focus styles, modal focus trapping, reduced-motion support, and local optimized WebP images.

## Run locally

```sh
npm start
```

Open http://127.0.0.1:4173. The command only serves static files for development. Upload the contents of `dist/` to any static web host. Serve over HTTP locally or HTTPS when hosted — opening the HTML through `file://` prevents JSON loading in many browsers.

## Verification

```sh
npm test
npm run check
```

Tests cover the 17 assistant phrases, catalog matching, outfit budgets, cart variants and stock, coupons, totals, wishlist and unavailable-storage handling. Checks validate JavaScript syntax, JSON parsing, local image files and static links. Browser QA covers the complete text-assistant shopping journey and demo checkout.

## Under the hood

No framework and no backend — the whole app is hand-built static files orchestrated by `dist/js/app.js`:

- **Data layer** (`dist/js/products.js`) — loads the catalog from `dist/data/*.json`; filtering by category, gender, size, fit, color, material, rating and price, full-text search, and five sort orders.
- **Cart & state** (`dist/js/cart.js`) — cart in `localStorage` (`velora-cart`), stock validation before every add, coupon rules (e.g. NEXTMOVE, WELCOME15), free shipping at $150+, totals recalculated instantly.
- **Assistant** (`dist/js/voice-agent.js` + `dist/js/intent-engine.js`) — typed or spoken requests parsed into intent + entities: search, filter, build an outfit, add to cart, policy answers. Browser speech only — no remote AI.
- **Outfit builder** (`dist/js/recommendations.js`) — matches top + bottom + shoes + accessory by category, color, gender and budget; returns a message rather than a broken set when nothing fits.
- **Pages** — home, shop, product, cart, checkout, wishlist, outfit builder (`look.html`), about and service, all rendered from the one orchestrator with centralized event delegation.

📐 **Full technical walkthrough:** [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md) — module-by-module architecture, checkout and state details, QA coverage and limitations.

## Structure

| Path | Contents |
|---|---|
| `dist/*.html` | Static entry points with shared client-side navigation |
| `dist/js` | Catalog, commerce, assistant, intent processing, recommendations, checkout, animation and scene modules |
| `dist/css` | Shared, responsive, animation and assistant styles |
| `dist/data` | Products, brands, categories, reviews, FAQ and coupons |
| `scripts/` | Development asset preparation and verification utilities |
| `tests/` | Dependency-free commerce tests |

All deployable files are in `dist/`. Edit the checked-in `dist/data/products.json` for catalog changes — rerunning the initial `catalog.mjs` generator resets the curated images and later revisions.

## Honest limits

- **Fictional demo brand.** Images are illustrative and documented in `ASSET-SOURCES.json`; colours and materials describe the fictional catalog. Ratings and reviews are clearly marked sample data.
- **No real transactions.** No real orders, mail subscriptions, payments or shipments are transmitted.
- **Voice depends on the browser.** Recognition availability depends on browser support and microphone permission; some browsers use their own network speech service, so recognition is not guaranteed offline. Text shopping works fully without any paid AI service.
- **Galleries show only real coverage.** Original photo coverage varies by product, so galleries show only available unique photographs rather than duplicate or invented back views.
- **Before a commercial launch:** confirm image usage rights and replace illustrative third-party product photographs.

## Tech

Vanilla HTML/CSS/JavaScript · JSON catalog · LocalStorage · Web Speech API (recognition + synthesis) · Three.js · WebP imagery · Netlify static hosting
