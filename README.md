# kwant-basket-simulator

A tiny HTML + JS debug UI for the Kwant basket API (`node-basket`), built to speed up backend development and testing without Postman or waiting for the Next.js frontend.

It supports:
- **Guest baskets** via `basketId` cookie
- **Logged-in baskets** via JWT (`Authorization: Bearer <token>`)
- Collapsible + colored JSON response viewer
- Quick controls for shipping/payment/products + a “Quick Update” form

---

## Features

- ✅ Cookie-based session (`credentials: "include"`) for guests
- ✅ Optional JWT token for logged-in user flows
- ✅ Colored buttons by HTTP method:
  - **GET** = green
  - **POST** = blue
  - **PUT** = orange
  - **DELETE** = red
- ✅ Collapsible + colored **JSON viewer**
- ✅ Shipping + Payment selectors generated from basket response
- ✅ Basket **summary panel** (money totals)
- ✅ Products select (pick product → update qty / remove without typing IDs)
- ✅ “Quick Update” form:
  - email + comment
  - additional fields (checkboxes)
  - address presets (shipping/billing)
  - `promo_code` field included (currently disabled)
- ✅ Debug button:
  - `POST /debug/clear-cookie` clears `basketId`

---

## API base URL

The simulator is designed for the basket API mounted at:

```

/api/front/node-basket

```

Default configured base URL in the UI:

```

[http://localhost:3355/api/front/node-basket](http://localhost:3356/api/front/node-basket)

````

If your backend runs on a different port/host, change it in the UI.

---

## Requirements

- Running backend with the basket router available at:
  - `GET /api/front/node-basket`
- Debug route enabled:
  - `POST /api/front/node-basket/debug/clear-cookie`
- If running the simulator on a different origin than the backend:
  - backend CORS must allow the simulator origin
  - and `credentials: true`

**Recommended:** serve the simulator from the same origin as the backend to avoid CORS/cookie issues.

---

## Getting started

### 1) Clone

```bash
git clone <YOUR_REPO_URL>
cd kwant-basket-simulator
````

### 2) Run a local static server

`basket-sim.html` is in the repo root.

**Python (recommended):**

```bash
python -m http.server 8080
```

Open:

```
http://localhost:8080/basket-sim.html
```

(You can use any static server; the only important part is not opening the file via `file://`.)

---

## Usage

### Guest mode (cookie basket)

1. Make sure token input is empty (click **Guest mode**)
2. Click **POST / (A)** (or B/C) to create a basket and set `basketId` cookie
3. Click **Refresh (GET /)**

To clear guest cookie:

* click **POST /debug/clear-cookie**

---

### Logged-in mode (JWT)

1. Paste JWT into the token field **without** `Bearer`
2. Click **Refresh (GET /)**

The simulator will send:

```
Authorization: Bearer <token>
```

---

## Customization

### Predefined products

Edit these in `basket-sim.html`:

```js
const PREDEFINED = {
  A: [{ product_id: 101, quantity: 1 }],
  B: [{ product_id: 202, quantity: 1 }],
  C: [{ product_id: 303, quantity: 2 }],
};
```

> The API expects arrays, even for a single product.

### Address presets

Edit `ADDRESS_PRESETS` in `basket-sim.html`.

### Additional fields checkboxes

Edit `ADDITIONAL_FIELDS_META`:

```js
const ADDITIONAL_FIELDS_META = [
  { field_id: 3, translation: { name: 'Akceptacja regulaminu' } },
  { field_id: 23, translation: { name: 'marketing - zgoda telefon [przedsiębiorca]' } },
  { field_id: 9, translation: { name: 'Obowiązek zapłaty' } },
];
```

---

## Troubleshooting

### Cookie doesn’t set / basket doesn’t persist

* If your backend sets cookie with `secure: true` on plain `http://`, the browser won’t store it.
* Use env-based cookie config:

  * `secure: process.env.NODE_ENV === 'production'`
* Best: host the simulator from the same origin as backend.

### CORS errors

If simulator origin differs from backend origin, backend must:

* allow the simulator origin in CORS
* set `credentials: true`

---

## Security notes

This tool is intended for **local dev / internal use**.

* Don’t deploy it publicly with real tokens.
* Consider gating `/debug/*` routes behind `NODE_ENV !== 'production'` and/or internal auth.

---

## Roadmap

* Enable `promo_code` updates and validate discount calculations
* Product table view (name/qty/price/sum + inline qty controls)
* Extra basket fields (inpost, overstock handling, etc.)
