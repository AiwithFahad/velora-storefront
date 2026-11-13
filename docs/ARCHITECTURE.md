# VELORA — Technical Architecture

A deep look at how the VELORA storefront works under the hood. For the project overview, see the [README](../README.md).

## At a high level

VELORA is a **frontend-only fashion storefront demo** for a fictional brand, built as a collection of static HTML, CSS and JavaScript files. The app starts from `server.mjs`, and its runtime logic is centered in `dist/js/app.js`.

It is a boutique-style shopping site with:

- Landing page
- Shop listing with filters
- Product detail pages
- Cart and checkout flow
- Wishlist
- Outfit builder
- Voice/text shopping assistant

It is designed as a **demo brand experience, not a real ecommerce backend**. No database, login system, payment gateway or real order processing exists. All user state is stored in browser `localStorage` when available, with a fallback in-memory storage.

## How it runs

The project uses Node for local serving. From `package.json`:

| Command | What it does |
|---|---|
| `npm start` | Serves the built static site on http://127.0.0.1:4173 |
| `npm test` | Validates shopping and assistant behaviors |
| `npm run check` | Checks JS syntax, JSON validity, static links and local image assets |

The actual site is in `dist/` — it is not a framework app (no React/Vue/Next). The server simply serves static files from `dist/` and blocks invalid paths.

## Project structure

| Path | Role |
|---|---|
| `dist/index.html` | Home page |
| `dist/shop.html` | Product catalog page |
| `dist/product.html` | Product detail view |
| `dist/cart.html` | Shopping bag |
| `dist/checkout.html` | Simulated checkout |
| `dist/wishlist.html` | Saved items |
| `dist/look.html` | Outfit builder |
| `dist/about.html` | Brand story |
| `dist/service.html` | Shipping / returns / help |
| `dist/data/` | Catalog JSON for products, reviews, coupons, FAQs |
| `dist/js/` | App logic |
| `dist/css/` | Styling |
| `dist/assets/` | Optimized product and campaign images |
| `scripts/` | Asset prep and validation tools |
| `tests/` | Commerce and voice behavior tests |

## Core app architecture

### Product data layer

The site loads catalog data via `fetch` from `dist/data/*.json`. The main catalog logic lives in `dist/js/products.js`, which exports:

`products` · `faq` · `coupons` · `reviews` · `loadCatalog()` · `getProduct()` · `filterProducts()` · `sortProducts()` · `money()` · `escapeHTML()`

The catalog supports:

- Filters: category, gender, size, fit, color, material, rating, price
- Full-text search
- Collection filtering
- Sort options: featured, newest, price, rating, popularity

### Cart and state management

Shopping cart logic is in `dist/js/cart.js`. It stores cart data in `localStorage` under the key **`velora-cart`**, and also tracks the wishlist, the applied coupon, the shipping preference and saved orders.

Important behavior:

- Validates stock before adding
- Prevents exceeding product stock
- Supports quantity updates and removal
- Computes subtotal, discount, shipping and total
- Coupon logic checks minimum subtotal and discount type

Totals logic, in order:

1. Start with the subtotal
2. Apply a percent discount or a free-shipping coupon
3. Shipping is free at **$150+**, or if the coupon qualifies
4. Totals are recalculated instantly

### UI rendering and page routing

`dist/js/app.js` is the main orchestrator. It renders the home page featured products, the shop listing with filters + sort, the product detail page with color/size picker and gallery, the cart page, the checkout stepper, the wishlist page, the outfit builder page, the account page, the about page and the service page.

It uses **event delegation** on document `click` and `submit` events, so handlers are centralized. It also handles modals, toast notifications, navigation state, keyboard focus management, and an image fallback if a product image doesn't load.

### Voice + text shopping assistant

The assistant is implemented in `dist/js/voice-agent.js`. It is a **browser-powered assistant, not a remote AI backend**. It supports typed requests, speech recognition via the Web Speech API, speech synthesis for spoken replies, and fallback messaging when browser speech isn't supported.

The assistant relies on `dist/js/intent-engine.js`, which parses natural language into **intent + entities**. Intents it recognizes include:

- Search product
- Filter by category / color / size
- Show sale / new arrivals
- Build outfit
- Add to cart
- Open cart
- Wishlist actions
- Checkout
- Shipping / return policy questions
- Customer support

Request examples:

> “Find me a black oversized hoodie.”
> “Build me an outfit under $200.”
> “What is your return policy?”

It uses `searchFromEntities()` to find matching products and `buildOutfit()` to recommend 4-piece looks under budget.

### Outfit builder

Logic is in `dist/js/recommendations.js`. It creates a four-piece outfit by matching a **top, a bottom, shoes and an accessory**, selecting candidates based on category, color, gender and budget. If no valid set fits the constraints, it returns a message instead of broken results. It is used by both the assistant and the outfit page.

### Checkout flow

`dist/js/checkout.js` handles the demo purchase process. It simulates customer info, shipping selection, payment acknowledgement, the review step and the final order success state. It does not charge real money or store real data outside the browser. Orders are saved locally and can be viewed in account history.

### Wishlist and account logic

The wishlist is in `dist/js/wishlist.js` and stores favorite product IDs in `localStorage`. The account page shows order history and saved preferences. There is no backend account system.

## Styling and UX

The CSS is highly custom and designed for a premium editorial brand feel:

- Typography-heavy fashion branding
- Responsive mobile layout (`dist/css/responsive.css` handles desktop and mobile layouts)
- Modal support
- Keyboard accessibility
- Reduced-motion support
- Skip-link, focus states, ARIA labels
- Animated hero and assistant UI

## Visual scene and motion layer

`dist/js/three-scene.js` adds a 3D studio/hanger scene when appropriate, using Three.js vendor assets in `dist/js/vendor/`. Product cards and hero sections also include motion and animation logic from `dist/js/animations.js` and `dist/css/animations.css`.

## Data and brand content

The product catalog is a curated demo dataset with fictional products, materials, sizes, ratings, reviews and pricing. It is intentionally realistic, but not real commercial inventory.

`QA.md` confirms the app was tested for: shopping assistant commands, catalog matching, filtering and stock validation, coupons and totals, wishlist persistence, browser speech flow, women's collection correction and responsive checks.

## Important limitations

- No real backend
- No real checkout / payment processing
- No user accounts or authentication
- Speech recognition depends on browser support, permissions and device hardware
- Product photos are illustrative, and some products have a limited image set
- The app is meant to be a demo / portfolio-style storefront

## How the site behaves in practice

You browse the collection and filters update the grid instantly. Selecting a product opens a full detail page with color, size and quantity choices. Add-to-bag enforces stock limits, and cart totals update in real time. You can apply coupons like **NEXTMOVE** or **WELCOME15**, depending on the catalog. The assistant can help you search or build a look without needing a full backend. Because the app uses local browser state, your bag, wishlist and orders persist across reloads on the same device.

**In one sentence:** a polished static storefront demo with a premium editorial design, real browser-side state management, and a built-in shopping assistant that interprets natural language requests and guides users through browse, outfit building, cart and demo checkout.
