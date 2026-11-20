Yes. What you're describing is Square's **OAuth flow**: your Nimbus users click "Connect Square" in settings, approve access on Square's site, and your backend gets a token to pull their data. Here's the overall shape.

## 1. Set up your Square app

- Create an application in the [Square Developer Dashboard](https://developer.squareup.com/apps).
- Note your **Application ID** and **Application Secret**.
- Under **OAuth**, add your **redirect URL** (e.g. `https://app.nimbus.com/integrations/square/callback`).
- Use the **Sandbox** while building, then switch to Production.

## 2. The connect flow (in your settings page)

1. **User clicks "Connect Square"** and you redirect them to:
    
    ```
    https://connect.squareup.com/oauth2/authorize
      ?client_id=YOUR_APP_ID
      &scope=CUSTOMERS_READ ORDERS_READ PAYMENTS_READ MERCHANT_PROFILE_READ
      &session=false
      &state=RANDOM_STRING_TIED_TO_USER
    ```
    
    Sandbox uses `connect.squareupsandbox.com`.
    
2. **The user logs in to Square and approves.** Square redirects back to your callback with `?code=...&state=...`.
    
3. **Verify `state`** matches what you stored (this prevents CSRF), then exchange the code on your **backend**:
    
    ```
    POST https://connect.squareup.com/oauth2/token
    {
      "client_id": "...",
      "client_secret": "...",
      "code": "...",
      "grant_type": "authorization_code"
    }
    ```
    
4. **Store the response securely**, encrypted and tied to that Nimbus user or organization:
    
    - `access_token`
    - `refresh_token`
    - `expires_at` (access tokens last about 30 days)
    - `merchant_id`
    
    Never expose these to the frontend.
    
5. **Refresh before expiry** with `grant_type: "refresh_token"`. Handle revocation too: if calls start returning 401, mark the integration as disconnected and prompt a reconnect.
    

## 3. Pulling the data

Send `Authorization: Bearer <access_token>` and a `Square-Version` header on every call.

|Data|Endpoint|
|---|---|
|Locations (needed for orders)|`GET /v2/locations`|
|Customers|`GET /v2/customers` or `POST /v2/customers/search`|
|Orders|`POST /v2/orders/search` (requires `location_ids`)|
|Transactions / payments|`GET /v2/payments` (filter by `begin_time`/`end_time`)|

All of these are **paginated** via a `cursor`, so loop until it's empty. Orders belong to locations, so fetch the merchant's locations first.

## 4. Keeping data in sync

- **Initial import:** after connecting, run a background job that backfills customers, orders, and payments.
- **Ongoing:** subscribe to **webhooks** (`customer.created`, `order.updated`, `payment.updated`, etc.) so you don't have to poll. Verify the webhook signature and make handlers idempotent.
- Use `updated_at` filters for incremental syncs as a fallback.

## 5. Things to plan for

- **Multi-tenancy:** one Square connection per Nimbus user or org, so store tokens per tenant.
- **Rate limits and retries:** use exponential backoff on 429/5xx responses.
- **Disconnect button:** call `POST /oauth2/revoke` and delete the stored tokens.
- **Least privilege:** only request the scopes you need.

Square has an official SDK for Node, Python, Java, and others if you'd rather not hand-roll the HTTP calls. Check Square's docs for the latest API version and scope names before shipping.

To give you actual code, which backend stack is Nimbus using (Node, Python, Laravel, etc.)? I can write the OAuth callback and a sync job for it.




Because the redirect only proves the merchant approved. It doesn't connect anything yet.

## What the redirect gives you

When Square sends the browser back to `https://dev-web.nimbusreceipt.com.au/?code=...&state=...`, the only thing you have is a **temporary authorization code**. It's not an access token. It can't call any Square API, and it expires quickly (about 5 minutes).

## What `connectSquare` does with it

1. **Checks `state`.** It confirms the `state` belongs to this logged-in merchant and hasn't expired or been used, which prevents CSRF.
2. **Exchanges the code for tokens.** It sends `code` plus your `client_secret` to `/oauth2/token`. The secret lives only on your backend, so this can't happen in the browser.
3. **Saves the integration.** It stores `access_token`, `refresh_token`, `expires_at` and `merchant_id` against that merchant.
4. **Starts the first sync** of customers, orders and payments.

Until this runs, your database has no tokens, so `syncSquare`, `squareCustomers` and the other queries have nothing to work with. They'd fail with "not connected".

## So who calls it?

Your **frontend**, automatically. The browser lands on your site, and the page there should:

1. Read `code` and `state` from the URL.
2. Call `connectSquare(code, state)` with the merchant's login token.
3. On success, show "Square connected" and remove the params from the URL.

For testing now, copy `code` and `state` from the address bar and run the mutation manually in your GraphQL playground, as in my last message. Do it quickly, since the code is short-lived and the state is single-use.

## For the real flow

Add a dedicated callback route in your frontend, such as `/integrations/square/callback`. It runs the steps above. Then update `SQUARE_REDIRECT_URL` and the Sandbox Redirect URL in the dashboard to that path. Landing on the root domain works for testing, but the root page may redirect and lose the `code`.


## Order vs transaction in Shopify

**Order** is the sale itself: who bought, what they bought, and what it cost. It holds:

- the customer
- line items (product, variant, quantity, price)
- discounts, taxes and shipping
- totals
- fulfillment and payment status

**Transaction** is a money event attached to an order: an authorization, capture, sale or refund against a payment. One order can have several, for example a payment, then a partial refund. Each has an amount, status, gateway and card details such as brand and last 4 digits.

Shopify's order is closest to Square's **order**, and Shopify's transaction is closest to Square's **payment**. Your Square code already stores both, so the structure carries over.

## What's most useful in your case

Your domain is `nimbusreceipt`, and you have merchants and customers. If the product is receipts, the data you need, roughly in order of value, is:

1. **Orders with line items.** A receipt without items, quantities, prices, tax and discounts is just a total. This is the most important data. Your Square sync stores the order in `rawData`, but you only extract the total and state. You'll likely want to pull the line items out into proper fields.
2. **Payments or transactions.** These give you the payment method (card brand and last 4), the status, and on Square a `receipt_url` and `receipt_number`.
3. **Customers.** These matter because they link a purchase to a Nimbus customer user. Match on verified email or phone. Without that link, customers can't see their own receipts.
4. **Refunds.** A receipt system needs to show returns and adjusted totals. Square has refund events and Shopify has refund transactions. Your current code doesn't handle `refund.created` or `refund.updated`.
5. **Locations and merchant profile.** You need the business name, address, tax ID and the store location for the receipt header.
6. **Products and catalog** are lower priority. They're only worth syncing if you want product-level analytics or images on receipts, because line items already carry names and prices.

Inventory, payouts and staff data aren't relevant unless you plan analytics or accounting features.

## If you add Shopify

- You'd use the same OAuth pattern, but with Shopify's own flow (per-shop domain, HMAC-verified callback, `X-Shopify-Hmac-Sha256` on webhooks).
- The scopes would be `read_orders` and `read_customers`. By default, Shopify only exposes about the last 60 days of orders, so you need to request the `read_all_orders` scope to backfill older history. Check Shopify's current docs on this before building.
- Your `PosProvider` enum and the `PosIntegration` model already support multiple providers. I'd keep one shared normalized shape (customer, order with line items, payment) and write a small mapper per provider, rather than separate collections per POS.

If you'd like, I can sketch that normalized receipt model, or update your Square `upsert` to extract line items and refunds.



So the fix is on the frontend and in config, not in the backend:

1. Add a frontend page, e.g. `/square/callback`, which reads `code` and `state` and calls `connectSquare`.
2. In the Square dashboard, update the Sandbox Redirect URL to `https://dev-web.nimbusreceipt.com.au/square/callback`.
3. Set `SQUARE_REDIRECT_URL` in `.env` to exactly the same value, and restart the backend.