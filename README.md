# Tarang — bracelet store

A small handmade-bracelet storefront: product catalog, cart, wishlist, and a
real checkout wired up to **Razorpay** (the standard India-first payment
gateway — Stripe is invite-only for new Indian accounts as of 2026, so it
isn't a practical option to start with here).

## What's inside

```
tarang/
├── index.html          Home page
├── shop.html            Full catalog with category filters
├── product.html          Product detail (?id=product-id)
├── cart.html              Cart
├── wishlist.html           Wishlist
├── checkout.html            Shipping form + Razorpay payment
├── success.html               Order confirmation
├── css/style.css                Styling
├── js/products.js                 Product data — edit this to add/change items
├── js/store.js                      Cart/wishlist logic (uses localStorage)
├── api/create-order.js                Serverless function: creates a Razorpay order
├── api/verify-payment.js                Serverless function: verifies payment signature
└── package.json                           Dependency (razorpay) for the two functions above
```

Cart and wishlist are stored in the browser's `localStorage`, so no database
is needed for those. Checkout needs two small server-side functions (in
`/api`) because your Razorpay **secret** key must never reach the browser —
those functions run for free on Vercel's serverless platform.

---

## Step 1 — Get a Razorpay account (free)

1. Go to **razorpay.com** and sign up with your email/phone.
2. You don't need to finish full KYC yet — Razorpay gives you **Test Mode**
   immediately, which is enough to build and test the whole flow with fake
   card numbers before any real money is involved.
3. In the Razorpay Dashboard, go to **Settings → API Keys** and click
   **Generate Test Key**. Save the **Key ID** and **Key Secret** somewhere
   safe — you'll paste these into Vercel in Step 4.
4. When you're ready to accept real payments, come back here, complete KYC
   (PAN, bank account, business details), and generate **Live** keys — same
   process, different button.

## Step 2 — Put the code on GitHub

1. Create a free account at **github.com** if you don't have one.
2. Create a new empty repository (e.g. `tarang-store`).
3. Upload this whole `tarang` folder to it — easiest way if you're not
   familiar with git: on the repo page, use **Add file → Upload files** and
   drag in everything.

## Step 3 — Deploy to Vercel (free)

Vercel's free tier hosts the static site *and* runs the two `/api`
functions — no other backend needed.

1. Go to **vercel.com** and sign up (use "Continue with GitHub" — it's the
   fastest path and connects your repo automatically).
2. Click **Add New → Project**, select your `tarang-store` repository, and
   click **Import**.
3. Leave the build settings as default (Vercel auto-detects this as a static
   project with serverless functions in `/api`) and click **Deploy**.
4. After ~30 seconds you'll get a live URL like
   `https://tarang-store.vercel.app` — the site is already online at this
   point, just without working payments yet.

## Step 4 — Connect Razorpay to your deployment

1. In your Vercel project, go to **Settings → Environment Variables**.
2. Add two variables:
   - `RAZORPAY_KEY_ID` → your Test Key ID from Step 1
   - `RAZORPAY_KEY_SECRET` → your Test Key Secret from Step 1
3. Go to **Deployments**, open the latest deployment's menu, and click
   **Redeploy** (environment variables only apply to new deployments).

## Step 5 — Test the full flow

1. Visit your live URL, add a bracelet to the cart, and go to Checkout.
2. Fill in the shipping form and click **Pay**.
3. In the Razorpay test popup, use a test card: card number
   `4111 1111 1111 1111`, any future expiry date, any CVV — or pick UPI and
   use the success test UPI ID Razorpay shows on screen.
4. You should land on the confirmation page, and the test payment (with the
   shipping address and item list attached as notes) will appear in your
   Razorpay Dashboard under **Payments**.

## Step 6 — Go live

1. Complete Razorpay's KYC in the dashboard (business/individual details,
   PAN, bank account for payouts).
2. Generate **Live** API keys (Settings → API Keys → toggle to Live mode).
3. Replace the two environment variables in Vercel with your live key ID and
   secret, then redeploy.
4. Real payments will now land in your bank account on Razorpay's normal
   settlement schedule (T+2 working days by default).

## Optional — custom domain

In Vercel, go to **Settings → Domains** and add a domain you own (e.g.
`tarang.shop`). Vercel gives free SSL automatically. If you don't own a
domain yet, the free `*.vercel.app` URL works fine to start selling.

## Editing products

Open `js/products.js` — each product is a plain object with a name, price
(in ₹), category, description, material, and a `colors` array that drives
the bead-ring illustration. Add, remove, or edit entries there; nothing else
needs to change. To use real photography instead of the illustrated bead
rings, swap the `braceletArt(product)` call in each page's script for an
`<img>` tag pointing at your photo.

## Where orders live

There's no separate order database — each paid order shows up in your
Razorpay Dashboard (**Payments**) with the customer's shipping address and
item list attached as notes, which is enough to pack and ship from for a
small studio. If you outgrow that, a free tier of Supabase or Google Sheets
(via its API) is a natural next step for a proper order log — ask if you'd
like that added.
