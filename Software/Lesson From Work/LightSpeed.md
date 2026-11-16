the goal is to connect to retailer account. Can I get permission to access Bibek's Lightspeed retailer account ? One OAuth connection authorizes Nimbus to access the retailer account. The official API even provides a `GET /retailer` endpoint to retrieve the retailer entity after authorization.

Your developer account

**Nimbus application**

You register Nimbus with Lightspeed as an API application.

Lightspeed gives you:

- Client ID
    
- Client Secret
    
- Redirect URL configuration

Merchant account

**ABC Clothing's Lightspeed account**

This is the retailer account that wants to connect to Nimbus.

It has:

- Retailer identity
    
- Outlets
    
- Customers
    
- Sales
    
- Other business data

Yes. In Lightspeed, a **store (test store)** is essentially a **retailer account**.

That retailer account can contain multiple **outlets** (physical locations) and registers.

#### 2. There isn't just one Lightspeed POS

This is probably the first thing we need to establish.

Lightspeed has multiple POS products, and their APIs and authentication mechanisms are different.

|Product|Mainly used for|
|---|---|
|Lightspeed Retail X-Series|Retail stores, clothing shops, general retail|
|Lightspeed Retail R-Series|Retail businesses using the older Retail platform|
|Lightspeed Restaurant K-Series|Restaurants, cafes, hospitality|
|Lightspeed Restaurant L-Series|Restaurant businesses using another Lightspeed platform|

he general process:

1. You register your application.
    
2. You receive a `client_id` and `client_secret`.
    
3. Your merchant clicks Connect Lightspeed in your application.
    
4. They log into Lightspeed.
    
5. They authorize your application.
    
6. Lightspeed redirects them back to your backend.
    
7. Your backend exchanges the authorization code for access and refresh tokens.
    
8. You use those tokens to retrieve the merchant's data.

## ption B: Personal Access Token

This is a simpler approach.

Instead of registering a multi-merchant application and implementing OAuth, the merchant generates a personal token and gives it to your system.

Your backend uses that token:

```
GET /api/...
Authorization: Bearer YOUR_PERSONAL_TOKEN
```

Advantages:

- Simple to implement.
    
- Useful for testing.
    
- No OAuth redirect flow required.
    

Disadvantages:

- Not suitable for a scalable multi-merchant integration.
    
- X-Series personal tokens are restricted to eligible Plus-plan retailers.
    
- Every merchant needs to provide their own token.
    

Lightspeed itself recommends OAuth for applications connecting multiple retailers.

x-series-api.lightspeedhq.com+1


## Option C: Third-party integration provider

Instead of integrating with Lightspeed directly, you use an intermediary platform.

The architecture becomes:

```
Your Application
       |
       v
Integration Provider
       |
       v
Lightspeed POS
```

The provider handles some of the complexity:

- Authentication.
    
- API differences.
    
- Data synchronization.
    
- Connection management.
    
- Potentially multiple POS platforms.
    

This can be useful if you eventually support many different POS systems.

However, it introduces another dependency, potential costs, and limitations on which Lightspeed data you can access.

## Option D: Reports and CSV exports

Some businesses can export sales and financial reports from Lightspeed.

You could ingest those files into your application.

This is useful for:

- Historical data imports.
    
- Initial testing.
    
- Businesses without API access.
    

But it isn't a good primary integration for a real-time or regularly synchronized financial aggregator.


# 5. What data should we actually retrieve?

This is where your financial aggregator's requirements become important.

I would initially investigate these resources:

|Data|Why we need it|
|---|---|
|Sales|Understand what the merchant sold|
|Sale line items|Understand individual products sold|
|Payments|Understand how sales were paid|
|Refunds|Account for returned money|
|Registers|Understand checkout/register activity|
|Outlets|Identify which store generated the sale|
|Products|Identify products associated with sales|
|Customers|Customer-level reporting, if required|

But I want you to remember one important financial concept:

A practical example

STEP 1 · POS SALE

# $100.00

Customer purchases products worth $100.

STEP 2 · PAYMENT PROCESSING

# $3.00

Payment processing fee.

STEP 3 · SETTLEMENT

# $97.00

Net amount eventually deposited.

Illustrative example, not a claim about Lightspeed's actual fees or settlement behavior.

If your application simply counts the $100 POS sale and the $97 bank deposit as two independent transactions, you'll double-count revenue.

That's why we need to understand the difference between sales, payment records, and bank settlements.

# 6. What I would recommend

For your project, I'd lean towards:

- Official Lightspeed APIs.
    
- OAuth 2.0 for merchant connections.
    
- Webhooks plus scheduled polling.
    
- A normalized internal transaction model.
    
- Separate handling for sales, refunds, fees, and settlements.
    

This will also make it easier to support Shopify and other POS providers without coupling your entire financial model to one provider.

# 1. Why does Lightspeed have Retail and Restaurant?

Think about two different businesses.

[Barcode Scanners for NZ Retail: Faster Checkout Starts Here](https://images.openai.com/static-rsc-4/q9oDnNAZcJgGXGcQr_rgKe6GshjZX555j3J-Zl2U0__FquxL0MIFQc7JP40rkOOeoi_vtMoJuP-0b0Sw3fNgZYiqy7lsPkqYU4UT7d3BcN_fiMAfgAcwmECYBeG4BJY4ipgqV99M6WLcUxnZjhc6LhJuaESst_YsdJHu0AHolkw?purpose=inline)

Business A: Clothing store

A customer buys a shirt.

The business needs:

- Products and inventory.
    
- Barcode scanning.
    
- Sales and payments.
    
- Customer records.
    
- Returns and refunds.
    

[Effective POS System Training: Empower Your Staff | Pecan POS Blog](https://images.openai.com/static-rsc-4/Hfi0ddeid9BXOL7hWRwDidHpP3aj0N_JjyM5X57rf804o_JWtZvKgO6eRI1-IwU549fbHbE_NnGQV47Jm1t10Y4YgyB0L0hsrXqJ0w4V7-P0TzPwew-8ElMx5L3fHdj4gy6mybG75R9j9og4EKF-SY-L84bHiUvlql6zZArfzpc?purpose=inline)

Business B: Restaurant

A customer orders food.

The business needs:

- Menus and food items.
    
- Tables and reservations.
    
- Kitchen orders.
    
- Bills and tips.
    
- Payments and refunds.
    
- Customer records.
    

Both businesses need to record sales and payments, but their operational workflows are different.

That's why Lightspeed has different POS products.

# 2. Are they completely different applications?

Essentially, yes, from an integration perspective.

They belong to the same Lightspeed company, but they have different product architectures, APIs, and sometimes authentication and permission models.

Think about it this way:

||Retail|Restaurant|
|---|---|---|
|Main purpose|Selling physical products|Selling food and drinks|
|Core entity|Product|Menu item|
|Transaction|Sale|Order / sale|
|Additional entities|Inventory, outlets|Tables, kitchen, menus|
|API|Product-specific|Product-specific|

For example, a restaurant might have an order containing:

```
{
  "order": {
    "items": [
      {
        "name": "Chicken Burger",
        "quantity": 2,
        "price": 15
      }
    ],
    "total": 30
  }
}
```

A retail store might have a sale containing:

```
{
  "sale": {
    "line_items": [
      {
        "name": "T-Shirt",
        "quantity": 2,
        "price": 15
      }
    ],
    "total": 30
  }
}
```

These are illustrative examples, not actual Lightspeed API response schemas.

Notice something?

Both represent a business transaction, but their terminology and data structures can differ.

That matters when designing your aggregator.

# 3. There are actually more than two products

Lightspeed has several product families.

[Welcome to Retail POS (X-Series) – Lightspeed Retail (X-Series)](https://images.openai.com/static-rsc-4/I13JMl-qxhvmz3ED0mlSljpnBgckiUCO41wgnolXmfc4R8Hql7hG1ag_KEdB_PVmqQc01g5CyF8qX2lNlkndrbPlw6FiP6b4OpF2Y8_A_eSftYb1dOmrHMGMP2-rfBGn4yyIJdwb1GnSKydTes2u1j6qHTFVHNIywjhQXKlPi_I?purpose=inline)

Retail X-Series

Modern retail POS. Used by shops and retailers.

[Processing payments – Lightspeed Retail (R-Series)](https://images.openai.com/static-rsc-4/7CGGO5PXgwMJlh-JWfypnax0yKxIOdBFhjy0w2MyTH-NlnnS1w0wPllwXrSQIqHNIUA9dIzFh7o0FDFbUtip3Zc_rwiSBA-CLDF0jDUmgkL-3J67C7-5BDLCkkor-BqeMnfHDCHnoo6iVuKzNr9PIbRG0vxLehKTc5G6IGDgKB0?purpose=inline)

Retail R-Series

Another retail platform with its own integration ecosystem.

[Understanding the Register screen – Lightspeed Restaurant (K-Series)](https://images.openai.com/static-rsc-4/9-d8aDFRTEUxOmQoYDp85ZWjfAjlgT5xzO3z_-mL4xsym2IPa1zH1vGMIyZ0AGO1ly_-8fJt9KzvXi7t4EyaQzgt6PN1SsSV86NXFqiHEcgist40VB4y_sbgQD05Ne-Jn4u62tS9jK_yf57JYIbwj6tlET2moBt_0ILW7HbPrv0?purpose=inline)

Restaurant K-Series

Restaurant POS with tables, orders, menus and payments.

[Searching for products – Lightspeed Restaurant (L-Series)](https://images.openai.com/static-rsc-4/874yZWOOJkmdWJduIv_sSXQK3fZEPNfx9lsfY8V2kJHG0PVpuw4cW2PjOVG7wuLiLbhIpL0pmwrpr7P1eYiUY3lF7RfoXuS6b2zmM5rAPYQ7CiTVZClzSPEhtQHuS87QpPutGs5clqV8YwNyetCJslSRe1rS2Mk6F8naMo55PN0?purpose=inline)

Restaurant L-Series

Another restaurant POS platform.

There are other products too, including U-Series and other regional/product offerings

# 5. One very important thing: API access restrictions

I want to flag something before we go further.

Having an API does not automatically mean you can build a public integration for every merchant.

For example, the official K-Series documentation currently distinguishes between:

- Custom integrations for an individual business.
    
- Approved integrations for multiple merchants.
    

Its custom integration documentation explicitly says that commercial integrations for multiple merchants require a formal partnership. Its custom API access also has limitations, including unavailable order API scopes.

Developer Documentation+2  https://api-portal.trial.lsk.lightspeed.app/quick-start/custom-integrations?utm_source=chatgpt.com

# 6. One last concept: What exactly are we fetching?

You mentioned orders, transactions and customers.

Let's distinguish them:

| Concept     | Meaning                                             |
| ----------- | --------------------------------------------------- |
| Order       | What the customer requested or purchased            |
| Sale        | A recorded commercial sale                          |
| Payment     | How the customer paid                               |
| Transaction | A financial event; exact meaning depends on the API |
| Customer    | The person who made the purchase                    |
| Refund      | Money returned to the customer                      |
These concepts can be linked, but they aren't interchangeable.

For your aggregator, I'd initially focus on:

1. Sales/orders and their line items.
    
2. Payments and refunds.
    
3. Customers.
    
4. Financial reports and settlements, where available.
    

And we should investigate whether each Lightspeed product actually exposes these resources through an API you are permitted to use.

My main takeaway: You don't need to understand every Lightspeed product as a POS user. You need to understand the different product families as an integration developer, because they determine which API, authentication, permissions and data model your backend will work with.

X-Series vs R-Series vs K-Series — the particular Lightspeed product/platform they use.


# 1. What are X, R, K and L?

They are just Lightspeed's internal product-series names. Think of them as different editions or generations of software.

| Name     | Full product name       | Business type |
| -------- | ----------------------- | ------------- |
| X-Series | Retail POS X-Series     | Retail        |
| R-Series | Retail POS R-Series     | Retail        |
| K-Series | Restaurant POS K-Series | Restaurant    |
| L-Series | Restaurant POS L-Series | Restaurant    |
|          |                         |               |
|          |                         |               |

For example:

- A clothing shop might use X-Series.
    
- Another clothing shop might use R-Series.
    
- A cafe might use K-Series.
    
- Another restaurant might use L-Series.
    

X and R are not different types of shops. They are different retail POS platforms. Similarly, K and L are different restaurant POS platforms.

Historically, these product names also reflect different products and acquisitions. For example, X-Series was formerly Vend. You don't need to memorize their history.

x-series-api.lightspeedhq.com+2

### The core idea

Users never give you their Lightspeed password. The integration uses OAuth 2.0, the same “Connect with Google” pattern you’ve seen elsewhere. The flow looks like this:

1. **You register once as a developer** with Lightspeed and create an “app”. This gives you a client ID and client secret. Your developer account is separate from any store account, so a store login won’t work there. [nango](https://nango.dev/docs/api-integrations/lightspeed-retail/how-to-register-your-own-lightspeed-retail-api-oauth-app)
2. **The user opens Settings in your app and clicks “Connect Lightspeed”.**
3. **Your app redirects them to Lightspeed**, where they log in with their own credentials and approve the permissions (scopes) you asked for.
4. **Lightspeed redirects back to your app with a code.** Your backend swaps it for an access token and a refresh token.
5. **You store those tokens securely**, encrypted and tied to that user or business.
6. **Your backend calls the Lightspeed API** on their behalf, refreshing the token when it expires.
For X-Series, only the Authorization Code Grant is currently supported. A “Disconnect” button in settings just deletes the stored tokens. [lightspeedhq](https://x-series-api.lightspeedhq.com/docs)

### The main catch: “Lightspeed” is several products

Lightspeed grew through acquisitions, so there isn’t one API. Lightspeed Retail X-Series was formerly Vend, and there are separate product lines with separate APIs: [supergood](https://supergood.ai/api-report-card/vend)

- **Retail X-Series** is the former Vend.
- **Retail R-Series** is the older retail product.
- **Restaurant K-Series** and **O-Series**.
- **eCom C-Series** is the online store product.

The surface splits across R, X, and O series, which complicates the choice of integration. Each has different docs, endpoints, and quirks. A user on X-Series can’t connect through the R-Series API.

Some examples of the differences:

- **X-Series** needs the store’s domain prefix, from https://{domain_prefix}.retail.lightspeed.app, so your connect screen may need to ask for it. [lovable](https://docs.lovable.dev/integrations/lightspeed)
- **R-Series** has an extra step. The accountID isn’t the OAuth client_id. You resolve it separately right after the token exchange, and every later call needs it. [stitchflow](https://www.stitchflow.com/user-management/lightspeed/api)
- **K-Series** is more restricted. Its developer portal is gated to approved partners through the Partner Program. [supergood](https://supergood.ai/api-report-card/lightspeed)

### Approval and limits

- Building against the public developer portal is self-serve. Publishing a Marketplace add-on requires Developer Certification and a Partnerships review. [supergood](https://supergood.ai/api-report-card/lightspeed-pos)
- For X-Series, an unapproved app can only connect to 30 Lightspeed Retail stores. That’s usually enough for a pilot, but you’d need approval before going wide. [k-hosting](https://5067.k-hosting.pl/xe6az0/lightspeed-oauth.html)

### Other practical points

- **Scopes:** ask only for what you need, for example read products and sales, not everything. X-Series apps must request the appropriate scopes during the retailer authorization step. [lightspeedhq](https://x-series-api.lightspeedhq.com/docs/scopes)
- **Webhooks vs polling:** X-Series has webhooks on core objects, while some R-Series areas don’t, so you may need scheduled syncing.
- **Single-store shortcut:** X-Series also supports personal tokens, which suit one-retailer integrations but not a multi-user app like yours.
- **Shortcut for building it:** platforms like Nango handle OAuth, token refresh, and storage for you, if you don’t want to build that yourself.

### What the letters mean

Lightspeed sells POS (cash register) software, and each product is built for a different kind of business. The letters are just product names.

| Product                 | Built for                                       | Examples                              |
| ----------------------- | ----------------------------------------------- | ------------------------------------- |
| **Retail X-Series**     | Shops that sell products (newer retail product) | Clothing store, phone shop, bookstore |
| **Retail R-Series**     | Same kind of shops (older retail product)       | Same as above                         |
| **Restaurant K-Series** | Restaurants, cafes, bars (newer)                | Restaurant, cafe, bar                 |
| **Restaurant O-Series** | Restaurants, cafes, bars (older)                | Same as above                         |


- **Retail** = shops that sell items.
- **Restaurant** = places that serve food and drinks.
- **X vs R**, and **K vs O**, are two versions of the same idea, like Windows 10 and Windows 11. Some businesses are on the old one and some on the new one.

Sales are not a separate product. Every one of these products records sales, because that’s what a POS does. “Sales” is just a type of data you can read from each.

Start with one product only. For shops, that’s most likely X-Series. For restaurants, K-Series.

### Limits on your side

- **X-Series:** until Lightspeed approves your app, you can only connect to 30 stores. That’s fine for testing and early users, but you’d apply for approval before growing past it.
- **K-Series (restaurants):** you have to be accepted into Lightspeed’s partner program before you can build against it, so it’s slower to start.

### Summary

| Situation                                            | Can the user connect?       |
| ---------------------------------------------------- | --------------------------- |
| Owner with the right Lightspeed product              | Yes, one click              |
| Staff without admin rights                           | Probably not                |
| Uses a different Lightspeed product than you support | No, until you add it        |
| Doesn’t use Lightspeed                               | No                          |
| More than 30 stores on X-Series, before approval     | No, you need approval first |
|                                                      |                             |
|                                                      |                             |


### Lightspeed makes two main kinds of software

1. **One for shops** (sells items: clothes, phones, groceries).
2. **One for restaurants** (sells food: tables, orders, kitchen).

That’s it. The letters are only the names:

- **X** = the shop software (the newer one)
- **K** = the restaurant software (the newer one)
- **R** = the shop software, older version (ignore it)

So X and K are not two versions of the same thing. **X is for shops. K is for restaurants.**

### What you do

You don’t need to understand all of them. You only need to answer one question:

**Are your users shops or restaurants?**

- Shops: build for **X**
- Restaurants: build for **K**

No, the client ID and secret are only step one. Think of them as your app's ID card for Lightspeed. You still have to build the part that uses them.

## Step 1: Get your credentials (Lightspeed side)

Register as a developer and create an app. When you do, you'll set:

- **Redirect URL:** the address in your app where Lightspeed sends the user back after they approve. It must match exactly what your code uses.
- **Scopes:** the permissions you want. For you, read-only ones, such as reading products and sales.

Then you get your **client ID and secret**. Keep the secret on your backend only, never in frontend code.

## Step 2: Build the connection (your side)

1. **A "Connect Lightspeed" button** in user settings that sends the user to Lightspeed's login page.
2. **A callback route** in your backend at the redirect URL. It receives the code from Lightspeed and swaps it for an access token and a refresh token.
3. **Token storage:** save both tokens, encrypted, linked to that user in your database.
4. **Token refresh:** access tokens expire, so your backend uses the refresh token to get a new one automatically.
5. **API calls:** your backend uses the token to fetch the data you need.
6. **A "Disconnect" button** that deletes the stored tokens.

## Summary

|Part|Done by|
|---|---|
|Developer account, client ID and secret|You, once|
|Login and approval on Lightspeed|The user, with one click|
|Callback, token storage, refresh, data fetching|Your code|

So you need the credentials first, but they aren't enough on their own.

One thing to confirm first: if your users are **shops (X-Series)**, you can start right now because developer signup is self-serve. If they're **restaurants (K-Series)**, you must apply to Lightspeed's partner program first, so there's a waiting period.

Which are your users? Once I know, I can give you the exact steps for your product.



Yes, the APIs are different. They’re separate systems with different addresses, different login pages, and different data formats. The shop one (X) and the restaurant one (K) don’t talk to each other.

### What the user connects

They connect **their business account** at Lightspeed, which holds their store’s data. When they approve, they’re saying: “this app may read the data in my Lightspeed account.”

Example:

- Ali owns a phone shop and uses Lightspeed X.
- He clicks Connect in your app and approves.
- Your app can now read the products, sales, and so on of **Ali’s shop**, and nothing else.

Another user, Sara, does the same with her own shop. Your app keeps a separate connection for each user. Nobody sees anyone else’s data.

If a business has several branches, those usually sit under the same account, so one connection covers all of them. You may need to let the user pick which branch’s data they want.


Here are the links, grouped by product. I’m only listing ones that appeared in my research.

### Shops: Retail X-Series (start here)

- **Main docs:** [https://x-series-api.lightspeedhq.com/docs](https://x-series-api.lightspeedhq.com/docs)
- **Authorization (OAuth) guide:** [https://x-series-api.lightspeedhq.com/docs/authorization](https://x-series-api.lightspeedhq.com/docs/authorization)
- **Scopes (permissions list):** [https://x-series-api.lightspeedhq.com/docs/scopes](https://x-series-api.lightspeedhq.com/docs/scopes)
- **Step-by-step on registering your OAuth app (third-party, clear):** [https://nango.dev/docs/api-integrations/lightspeed-retail/how-to-register-your-own-lightspeed-retail-api-oauth-app](https://nango.dev/docs/api-integrations/lightspeed-retail/how-to-register-your-own-lightspeed-retail-api-oauth-app)

### Restaurants: K-Series

- **Developer portal (guides):** [https://api-portal.lsk.lightspeed.app/](https://api-portal.lsk.lightspeed.app/)
- **Authorization quick start:** [https://api-portal.lsk.lightspeed.app/quick-start/authentication/authorization-overview](https://api-portal.lsk.lightspeed.app/quick-start/authentication/authorization-overview)
- **API reference (endpoints):** [https://api-docs.lsk.lightspeed.app/](https://api-docs.lsk.lightspeed.app/)

### Restaurants: O-Series (older)

- **Developer application page:** [https://o-series-support.lightspeedhq.com/hc/en-us/articles/31329349293211-Submit-your-Developer-Application](https://o-series-support.lightspeedhq.com/hc/en-us/articles/31329349293211-Submit-your-Developer-Application)

### Shops: R-Series (older, only if your users are on it)

- The base API address is [https://api.lightspeedapp.com](https://api.lightspeedapp.com), but I didn’t find a verified official docs link. Search “Lightspeed Retail R-Series API” on Lightspeed’s developer site if you need it.

### Overview and comparison

- **Summary of all Lightspeed APIs and how hard each is to access:** [https://supergood.ai/api-report-card/lightspeed](https://supergood.ai/api-report-card/lightspeed)
- **Same for restaurants (K, L, O, G):** [https://supergood.ai/api-report-card/lightspeed-restaurant](https://supergood.ai/api-report-card/lightspeed-restaurant)

### What to read first

1. Open the **X-Series Authorization page**. It explains the connect flow we discussed.
2. Then the **Scopes page** to see which read permissions you need.
3. Skim the **K-Series portal** so you know what the restaurant side looks like.

One note: the third-party report card says K-Series has no self-serve key flow, so you must be approved as a partner first. X-Series is easier to start with, so it’s a good one to experiment on while you figure out which product your users have.


## Can you use dev/test right now?

For **shops (X-Series), yes**. You don't need anyone's permission to start.

A newly created app is marked "Not Approved", but you can still use it. It simply means you can connect to only 30 stores, which Lightspeed says is plenty for private apps and testing or staging environments.

So "dev/test" and "prod" aren't two separate setups. It's the same kind of app, with a limit that lifts once approved.

## What you need to start testing

1. **A Lightspeed developer account.** It's separate from any store login. The dashboard is at https://developers.retail.lightspeed.app/applications
2. **Create an app** there, set your redirect URL, and get your client ID and secret.
3. **A Lightspeed X-Series store to connect to.** Without one, you have nothing to read. Ask a friendly shop owner, or check whether Lightspeed offers a trial store. I didn't verify that.
4. **Your own code** for the connect button, callback, token storage, and data fetching, as we discussed.

## What changes for production

You can launch with the unapproved app, but you hit a ceiling:

||Dev/test (Not Approved)|Production (Approved)|
|---|---|---|
|Stores you can connect|Up to 30|No 30-store cap|
|Who it's meant for|Private apps, testing, early users|Production-ready, public applications|
|Needs Lightspeed's approval|No|Yes|

One practical point: if your app is live with real users and you reach store #31, new users can't connect until you're approved. So request approval before you get close to 30.

## What approval requires

I couldn't find a public checklist. The X-Series docs only say you need approval for production-ready, public apps, not what they review. One third-party source mentions Developer Certification and a Partnerships review for listing on Lightspeed's Marketplace, but I can't confirm the exact steps or timeline. I'd rather tell you that than guess.

The best way to find out is to open your app in the developer dashboard once you've created it, since the approval request is likely done from there, or contact Lightspeed's developer support.

## If your users turn out to be restaurants (K-Series)

K-Series does have a test environment, a demo URL (api.trial.lsk.lightspeed.app) and a production URL (api.lsk.lightspeed.app). But developers must be approved as partners first, with no self-serve key flow, so you can't just sign up and start testing as you can with X-Series.

## What I'd do next

1. Create a developer account and an app on the X-Series dashboard, since it's free to try.
2. Get the connect flow working with one test store.
3. Meanwhile, find out whether your users are shops or restaurants.
4. Ask Lightspeed what approval requires, before you get near 30 users.

Do you want me to outline what to put in the app registration form (redirect URL, scopes) for a read-only integration?


## Can you use dev/test right now?

For **shops (X-Series), yes**. You don't need anyone's permission to start.

A newly created app is marked "Not Approved", but you can still use it. It simply means you can connect to only 30 stores, which Lightspeed says is plenty for private apps and testing or staging environments.

So "dev/test" and "prod" aren't two separate setups. It's the same kind of app, with a limit that lifts once approved.

## What you need to start testing

1. **A Lightspeed developer account.** It's separate from any store login. The dashboard is at https://developers.retail.lightspeed.app/applications
2. **Create an app** there, set your redirect URL, and get your client ID and secret.
3. **A Lightspeed X-Series store to connect to.** Without one, you have nothing to read. Ask a friendly shop owner, or check whether Lightspeed offers a trial store. I didn't verify that.
4. **Your own code** for the connect button, callback, token storage, and data fetching, as we discussed.

## What changes for production

You can launch with the unapproved app, but you hit a ceiling:

||Dev/test (Not Approved)|Production (Approved)|
|---|---|---|
|Stores you can connect|Up to 30|No 30-store cap|
|Who it's meant for|Private apps, testing, early users|Production-ready, public applications|
|Needs Lightspeed's approval|No|Yes|

One practical point: if your app is live with real users and you reach store #31, new users can't connect until you're approved. So request approval before you get close to 30.

## What approval requires

I couldn't find a public checklist. The X-Series docs only say you need approval for production-ready, public apps, not what they review. One third-party source mentions Developer Certification and a Partnerships review for listing on Lightspeed's Marketplace, but I can't confirm the exact steps or timeline. I'd rather tell you that than guess.

The best way to find out is to open your app in the developer dashboard once you've created it, since the approval request is likely done from there, or contact Lightspeed's developer support.

## If your users turn out to be restaurants (K-Series)

K-Series does have a test environment, a demo URL (api.trial.lsk.lightspeed.app) and a production URL (api.lsk.lightspeed.app). But developers must be approved as partners first, with no self-serve key flow, so you can't just sign up and start testing as you can with X-Series.

## What I'd do next

1. Create a developer account and an app on the X-Series dashboard, since it's free to try.
2. Get the connect flow working with one test store.
3. Meanwhile, find out whether your users are shops or restaurants.
4. Ask Lightspeed what approval requires, before you get near 30 users.

Do you want me to outline what to put in the app registration form (redirect URL, scopes) for a read-only integration?



## What this screen is

This is Lightspeed's **developer dashboard**, where you manage your integration. Your Lightspeed shop login is separate from it. The left menu means:

- **My Apps / Applications:** your list of integrations. Each app has its own client ID, secret, and redirect URL. Yours will appear here now that you've created one.
- **Test Stores:** this is the answer to my earlier question about where to get a store to test with. It looks like you can create fake Lightspeed stores here, with sample products and sales, so you can connect to them without a real shop owner. Open that tab and create one.
- **Organisations:** I haven't verified this, but it's most likely for grouping apps under a company or team, so several developers can manage the same apps. You can ignore it for now.
- **Changelog:** Lightspeed's list of API changes. Handy later.
- **Settings:** your developer account settings.

## What the redirect URL is

When a user clicks "Connect Lightspeed", they leave your app, log in at Lightspeed, and approve. Lightspeed then needs to send them **back to your app**, and the redirect URL is the address it sends them to. It's an endpoint in your **backend**, not a page users see.

Lightspeed sends the user there with a temporary code, like this:

`https://yourapp.com/api/lightspeed/callback?code=abc123`

Your backend catches that code and swaps it for the access token and refresh token.

## What to enter

Use the real address of the callback route you'll build:

- **For production:** `https://yourdomain.com/api/lightspeed/callback`
- **For local testing:** `http://localhost:3000/api/lightspeed/callback` (use your own port)

The path can be anything you like, but your code must handle exactly that path. The URL has to match **exactly**, down to the slash at the end and http vs https. A mismatch is the most common reason this flow fails.

I haven't verified whether Lightspeed accepts `localhost` or http addresses, or whether one app can hold several redirect URLs. If the form rejects localhost, you may need a public tunnel address (for example from ngrok), or a separate app for local development.

## Next steps

1. **Test Stores:** create one.
2. **Your app:** note down the client ID and secret, and keep the secret on your backend only.
3. **Scopes:** choose read-only permissions only. You'll find them listed at https://x-series-api.lightspeedhq.com/docs/scopes

What does your app's form look like? If you share a screenshot of the form or the redirect field, I can tell you exactly what to enter.

In Lightspeed X-Series, a **sale** is the transaction record, and a **payment** is the money received against it. Sales are what you're syncing. Payments live inside each sale.

## Sale

One sale is a single checkout or order. It holds:

- **Line items:** the products, quantities, prices, discounts and tax.
- **Who and where:** the customer (optional), the staff user, and the register or outlet.
- **Totals:** `total_price`, `total_tax` and so on.
- **A status**, which matters a lot (below).
- **Dates:** when the sale was created and closed.

Returns and refunds are also sales, with negative amounts.

## Payment

A payment is one tender applied to a sale: cash, card, gift card, store credit and so on. In the API, each sale carries a `payments` array, where each entry records the payment type, amount and date.

- **A sale can have several payments** (split tender, such as part cash and part card).
- **A payment can be less than the sale total.** That happens with layby (a deposit plan) or on-account sales, where the customer pays later.
- **Payment types are configured per store** (cash, Visa, Afterpay and so on), so a payment gives you a type ID that you look up in the store's payment types list.

## Sale statuses

Don't treat every sale as revenue. The common statuses are:

- **CLOSED:** completed and fully paid. This is what you usually want for revenue.
- **OPEN / SAVED:** in progress or parked on a register, not a finished sale.
- **ONACCOUNT / LAYBY:** the customer owes money, so the sale is not fully paid yet.
- **VOIDED / DISCARDED:** cancelled, so exclude it.

There are a few more variants, such as awaiting pickup or dispatch, so check the exact list in the docs.

## What this means for your code

- I already store each sale's full payload in `raw`, so the payments are saved. They just aren't queryable yet.
- For reports like "revenue by payment method", I'd add a `payments` field to the sale schema (type ID, amount, date) and a sync of `/api/2.0/payment_types` so you can show names instead of IDs.
- When you show orders in your app, filter or label by status so open and voided sales don't inflate totals.

Want me to add the payments extraction and the payment types sync?




Yes, that customer is the default one. Lightspeed puts a placeholder customer named **WALKIN** (`customer_code: "WALKIN"`) in every store, used for sales with no customer attached. It has no name, email or phone, so it's what you'd expect from a store with no data. Your import is correct, and the sales count is zero because the store has no sales yet.

## Add your own test data

You can add all of it in the store's back office:

1. **Customers:** go to **Customers → Add Customer**. Add 2 or 3 with a name and email.
2. **Products:** go to **Products → Add Product**. Give each one a name and a price, because a sale needs something to sell.
3. **Sales:** open **Sell** (the register), and open the register if it asks for an opening float. Add a product, attach a customer, and take a payment (cash is fine). Complete 3 or 4 sales. Try one with no customer, one with a split payment, and one you void or return, so you see different statuses.

## Get them into your app

Your customers and sales states are already `completed`, so reconnecting won't pull the new data. Either delete the early return in `importResource`, or delete this integration's two documents from `lightspeed_import_states` and reconnect.

## Remove the debug log before you commit

The `Lightspeed first customer ... {...}` line writes a full customer record, which can contain names, emails, phones and addresses, into your logs. It's harmless with the placeholder, but it'll expose real personal data once real merchants connect, so delete it.

Also, your HTTP logger prints the full `/lightspeed/callback?code=...&state=...` URL. That code is single-use and already spent, so this one is dead. Still, exclude that route's query string from request logging, since logs often end up somewhere less protected than your database.

Your logged `scope` is `products:read sales:read customers:read`, which matches what we expected and is enough for sales with their embedded payments. After you add the data and re-import, check that `lightspeed_sales` and `lightspeed_payments` fill up.







Absolutely. Let's approach this as an actual integration design for Nimbus, not a coding tutorial.

I want you to understand **Lightspeed X Series from the perspective of an integration architect**: what happens when a merchant connects their account, what Nimbus needs to store, how data flows, what the frontend does, and what the backend is responsible for.

We'll focus on the best integration architecture from the beginning, rather than building something now and redesigning it later.

# **1. First, understand Lightspeed X Series**

[Introduction to the Lightspeed Retail (X-Series) API](https://images.openai.com/static-rsc-4/WXvnSQwmhgPS9Ni-wWDVYgOKfrqD1ZtlUTsTuD8ixX29MmgGFaSbzX35l6YQcOedWgcSHuOFbsKpV6BiAS5DkiSnE8tbASg4-MJ_Fk4TodtFte8pOHTU7kuAqLeACa7lNqe3uyT6vBtVOYeIv3CactgUufi0tO19BP4RL6SSCz4?purpose=inline)

**Lightspeed Retail X Series**

Retail POS and commerce management platform

A system retailers use to manage their stores, products, sales, customers, inventory and payments.

Think of Lightspeed as the merchant's existing business management system.

A merchant might already use Lightspeed to:

- Register customers.
    
- Record sales.
    
- Process payments.
    
- Manage products.
    
- Manage multiple outlets.
    
- Maintain customer purchase histories.
    

Nimbus doesn't need to replace any of these functionalities.

Nimbus needs permission to **read the relevant information from Lightspeed and make it available through its own customer and merchant applications.**

That's the fundamental integration model.

# **2. Understand the integration architecture**

There are three important parties:

**Merchant**

Owns the Lightspeed account

Authorizes access

**Lightspeed X Series**

External source of business data

API + Webhooks

**Nimbus Backend**

Authentication, data synchronization, normalization and storage

**Merchant Web**

Business data

**Customer Mobile**

Personal transactions

The most important architectural decision:

**Your frontend should never communicate directly with Lightspeed APIs.**

Your backend owns the integration. The frontend communicates with Nimbus, and Nimbus communicates with Lightspeed.

This gives you centralized token management, data normalization, synchronization, security and error handling.

# **3. Merchant connection: exactly what happens?**

Lightspeed X Series supports OAuth 2.0 Authorization Code Grant for applications connecting multiple retailer accounts. This is the correct approach for Nimbus.

Do not use Personal Tokens for your production multi-merchant integration. Lightspeed itself recommends OAuth for applications connecting multiple stores.

x-series-api.lightspeedhq.com+1

Let's walk through the actual user journey.

## **Step 1 — Merchant clicks Connect Lightspeed**

[Navigating in Retail POS (X-Series) – Lightspeed Retail (X-Series)](https://images.openai.com/static-rsc-4/Q-Di4mUJl9p-5lRU2YrehiQ5IR2NiTbsZB3pw2r--QqoWDfytnKNvk_Q4scKFOEaRyg9OCWkARBHFak3DKX9kUt-ZZ-dIKP5J-3RNOpScOe_MpyiRwhA7SQO8K0O7JgKDK-uGgIicArLjCSUCp8mHhPm4FPZavJt7MKEnRWtw3w?purpose=inline)

[x-series-support.lightspeedhq.com](https://x-series-support.lightspeedhq.com/hc/en-us/articles/25534118889115-Navigating-in-Retail-POS-X-Series?utm_source=chatgpt.com)

**Frontend — Merchant Web**

The merchant goes to:

Settings → Integrations → Lightspeed → Connect

Your frontend should:

1. Display Lightspeed as an available integration.
    
2. Show a Connect button.
    
3. When clicked, call your Nimbus backend to initiate the connection.
    
4. Receive an authorization URL.
    
5. Redirect the merchant to Lightspeed.
    

**Backend — Nimbus**

Your backend:

1. Generates a unique, unpredictable OAuth `state`.
    
2. Associates that state with the currently authenticated merchant.
    
3. Stores the pending authorization attempt.
    
4. Generates the Lightspeed authorization URL.
    
5. Returns the URL to the frontend.
    

Why store the state?

Imagine Merchant A starts the connection process. Before returning from Lightspeed, someone tries to manipulate the callback to associate another merchant's account.

The state allows Nimbus to verify that the authorization response belongs to the original connection attempt. It protects against CSRF attacks.

Important: The state must be validated on callback, not just generated.

## **Step 2 — Merchant authorizes Nimbus in Lightspeed**

[Beejek - Digital Receipts Platform for Retail Shopping](https://images.openai.com/static-rsc-4/6XqmiISN45dYSd3gROzsYj7jtPqtR41xwfzMxsDfsmuAfZmvnfEP14Rp1YB3HFt6lOgRLK15_Fmrw_QFQty9NGg19Nqr83tAn4YYK6qOslcAyBZ5xkbJgEAThqKUDV1TdqLNb6sUzb4BQtc4yU0i2OoEPvfPfrloknV9pQ7ZmG4?purpose=inline)

[beejek.com](https://www.beejek.com/blog/partner-guides/integrate-lightspeed-retail-r-series?utm_source=chatgpt.com)

The merchant is redirected to Lightspeed.

Lightspeed handles:

- Merchant authentication.
    
- Authorization.
    
- Consent to requested permissions.
    

You are not collecting their Lightspeed username or password.

You are asking Lightspeed to authorize Nimbus to access the merchant's data.

For Nimbus, I would initially request only the permissions needed for:

- `customers:read`
    
- `sales:read`
    

Additional permissions should only be requested if your actual integration requirements need them.

Lightspeed exposes scopes for different data domains, and the merchant is shown the requested permissions during authorization.

x-series-api.lightspeedhq.com+1

## **Step 3 — Lightspeed redirects back to Nimbus**

After authorization, Lightspeed redirects to your registered backend callback URL.

It returns information including:

- Authorization code.
    
- Domain prefix.
    
- State.
    
- Granted scopes.
    

The `domain_prefix` is particularly important.

For example, imagine the merchant's Lightspeed store has the domain prefix `abc123`.

Your backend needs this information to communicate with the correct retailer's API host.

**Backend responsibilities:**

1. Validate the returned state.
    
2. Identify the merchant who initiated the connection.
    
3. Exchange the authorization code for access and refresh tokens.
    
4. Store the returned credentials securely.
    
5. Store the retailer's domain prefix.
    
6. Record the granted scopes and token expiry.
    
7. Mark the integration as connected.
    

The authorization code is short-lived and single-use, so exchange it promptly.

One important detail: Lightspeed rotates the refresh token when refreshing an access token. You must save the new refresh token every time. The old refresh token is no longer valid.

x-series-api.lightspeedhq.com

# **4. What exactly should Nimbus save?**

This is one of the most important parts of the integration.

I would separate the data into two categories:

- Integration configuration and credentials.
    
- Actual business data imported from Lightspeed.
    

## **A. Integration information**

Think of this as a `merchant_integrations` record.

|Field|Purpose|
|---|---|
|id|Nimbus integration ID|
|merchant_id|Which Nimbus merchant owns it|
|provider|LIGHTSPEED_X|
|external_account_id|Lightspeed retailer/account identifier|
|domain_prefix|Lightspeed API domain prefix|
|access_token|Encrypted access token|
|refresh_token|Encrypted refresh token|
|token_expires_at|Access token expiration|
|granted_scopes|Permissions authorized|
|status|Connected, expired, revoked, error|
|connected_at|Connection date|
|last_synced_at|Last successful synchronization|
|created_at|Record creation|

Also maintain synchronization state separately, so the integration record doesn't become overloaded with operational information.

For example:

- Last successful customer sync.
    
- Last successful sales sync.
    
- Last attempted sync.
    
- Current sync status.
    
- Last sync error.
    

**Important:** Never return access tokens or refresh tokens to the frontend.

The frontend only needs safe information such as connection status, store name and last synchronization time.

## **B. Business data**

This is where Nimbus starts becoming useful.

I would have these logical data domains:

**1. Merchant / Retailer**

Represents the Lightspeed retailer connected to Nimbus.

External retailer ID, name, currency, timezone, domain prefix.

**2. Outlets**

Represents the merchant's individual retail locations.

External outlet ID, name, address, contact information.

**3. Customers**

Represents customers registered in Lightspeed.

External customer ID, name, email, phone, customer metadata.

**4. Sales / Transactions**

Represents purchases made by customers.

External sale ID, customer ID, outlet ID, total, tax, status, timestamps.

**5. Sale Line Items**

Represents individual products within a sale.

Product ID, product name, quantity, price, discount and tax.

**6. Payments**

Represents how a sale was paid.

Payment ID, sale ID, payment type, amount and payment timestamp.

Why separate Sales, Sale Line Items and Payments?

Consider this purchase:

A customer buys:

|Item|Amount|
|---|---|
|Shoes|$100|
|Socks|$20|
|Discount|-$10|
|**Total**|**$110**|

They pay $60 using cash and $50 using a card.

This is:

- One sale.
    
- Two sale line items.
    
- Two payment records.
    

If you store everything in one transaction record, you'll lose important details or create an unnecessarily complicated data structure.

For Nimbus, this distinction is important because customers should be able to see the transaction details, not just the total amount.

**One more thing:** Don't assume every sale has a customer attached. Lightspeed may contain sales without an identified customer. Your database must support nullable customer relationships.

# **5. Initial data synchronization**

Now the merchant has successfully connected Lightspeed.

What happens next?

**Nimbus should automatically start importing the merchant's existing data.**

This is called the initial synchronization or historical sync.

I would not make the merchant manually click separate buttons to import customers and sales.

The connection should initiate the synchronization automatically.

## **Backend synchronization flow**

1. **Fetch retailer information**
    
    Identify the connected retailer and retrieve its basic account information.
    
2. **Fetch outlets**
    
    Retrieve the merchant's retail locations.
    
3. **Fetch customers**
    
    Retrieve existing customer records, using pagination.
    
4. **Fetch sales**
    
    Retrieve historical sales, including associated customer, outlet and line-item information.
    
5. **Fetch payment information**
    
    Retrieve the payment details associated with the sales.
    
6. **Normalize and persist**
    
    Convert Lightspeed's data structures into Nimbus's common data model and save them.
    
7. **Mark initial sync complete**
    
    Update synchronization status and make the imported data available to the applications.
    

You should use background jobs for this process.

Why?

Imagine a merchant has:

- 15,000 customers.
    
- 80,000 sales.
    
- 200,000 sale line items.
    

You cannot reasonably expect a single HTTP request from the merchant's browser to wait until everything has been imported.

Instead:

1. Merchant connects Lightspeed.
    
2. Nimbus creates the integration.
    
3. Nimbus queues an initial synchronization job.
    
4. A background worker imports the data.
    
5. The frontend displays synchronization progress.
    
6. Merchant can continue using the application.
    

Lightspeed provides pagination and rate-limiting guidance, so your synchronization workers must respect those constraints.

x-series-api.lightspeedhq.com+1

## **What should the frontend display?**

**Lightspeed**

Connected

[Integrations - Lightspeed](https://images.openai.com/static-rsc-4/bHluUvNu5O18o1Cm_X84BeXQjF26sciid_L55MWVGXgNN0GTpT-3jQk2AH4sKDUhRFT0h-5dYqDI1L3HLGOPVhLw-EKh6P05Fn65mCewXblR4zcTm04mNbaIzCpgvYBDa0FQg-hyZlCGbLdiq3gp_DVTOvzKKV3RlAZPEu6Anjo?purpose=inline)

**My Retail Store**

Lightspeed X Series

**Initial synchronization**

Importing sales and customers

**65%**

Customers imported

**4,500**

Sales imported

**12,000**

Last updated

**In progress**

Illustrative merchant integration screen, not actual Lightspeed data.

The backend should expose synchronization status and progress through Nimbus APIs. Don't calculate progress in the frontend based on guesses.

# **6. Keeping Nimbus synchronized with Lightspeed**

This is where many integrations become unreliable.

Initial synchronization only gets you the historical data.

What happens tomorrow when the merchant makes another sale?

What happens when a customer is updated?

What happens when a sale is voided or changed?

You need ongoing synchronization.

My recommendation is:

Recommended architecture

**Webhooks + scheduled polling + reconciliation**

Lightspeed supports webhooks, but its documentation explicitly warns that webhook delivery is not guaranteed. It recommends polling the APIs to maintain synchronization.

x-series-api.lightspeedhq.com+1

Here's how I would implement this.

## **A. Webhooks — near-real-time updates**

Suppose a customer purchases something at the merchant's physical store.

**Customer purchases an item**

Lightspeed POS

**Lightspeed generates an event**

Sale created or updated

**Nimbus webhook endpoint**

Receives event and validates it

**Background worker**

Fetches latest entity and updates Nimbus database

**Customer sees updated transaction**

Nimbus Mobile API

I would avoid treating the webhook payload as your entire source of truth.

Use the webhook as a notification that something changed. Then fetch the latest entity from Lightspeed and update your database.

This helps handle incomplete event payloads and changes that happen close together.

## **B. Scheduled polling — reliability**

Even with webhooks, periodically ask Lightspeed:

- Have any new sales been created?
    
- Have any existing sales changed?
    
- Have any customers changed?
    
- Have any relevant records been updated?
    

Use the appropriate date filters, pagination and overlap windows supported by the API.

Also, don't assume a record's absence from a response means it was deleted.

## **C. Reconciliation**

Reconciliation means comparing the data in Nimbus against the data in Lightspeed and correcting differences.

For example:

|Lightspeed|Nimbus|Action|
|---|---|---|
|Sale exists|Sale exists|Check for updates|
|Sale exists|Sale missing|Import sale|
|Sale updated|Old version|Update sale|
|Customer updated|Old version|Update customer|

This is how you avoid relying entirely on webhook delivery.

# **7. How to handle customer identity**

This is particularly important for Nimbus because you have a customer-facing mobile application.

Imagine:

- Merchant A has a customer named Bibek.
    
- Merchant B also has a customer named Bibek.
    
- Both customers have the same email address.
    

Should Nimbus automatically assume they are the same person?

Not necessarily.

I recommend separating two concepts:

**External customer:** The customer record inside Lightspeed.

**Nimbus customer:** The person who registered in your mobile application.

Your database should maintain a mapping between them.

For example:

**Nimbus customer**

**Bibek**

bibek@example.com

**External customer identities**

Lightspeed — Merchant A

Linked

Lightspeed — Merchant B

Linked

Shopify — Merchant C

Linked

The important database relationship is:

- One Nimbus customer can be associated with multiple external customer records.
    
- Each external customer record belongs to a particular merchant and integration.
    
- A customer can only view transactions that are legitimately linked to their identity.
    

Do not automatically merge customer records solely because their email addresses match. Build a verified identity-linking process, with appropriate handling for shared emails, changed emails and duplicate records.

Also, don't assume every Lightspeed sale contains a customer ID. Anonymous or guest sales should not automatically appear in someone's mobile history.

# **8. What happens when a merchant disconnects Lightspeed?**

Your integration lifecycle needs to account for this from the beginning.

Merchant Web → Settings → Integrations → Lightspeed → Disconnect.

When they click Disconnect:

**Frontend**

- Ask for confirmation.
    
- Explain that Nimbus will stop synchronizing their Lightspeed data.
    
- Show the disconnected status after the backend confirms the operation.
    

**Backend**

1. Mark the integration as disconnecting.
    
2. Stop scheduled synchronization jobs.
    
3. Stop processing pending jobs for that integration.
    
4. Remove or deactivate registered webhooks where possible.
    
5. Revoke authorization if supported by the provider.
    
6. Securely delete the stored access and refresh tokens.
    
7. Mark the integration as disconnected.
    

One important product decision: disconnecting an integration does not necessarily mean deleting historical transaction data.

I would keep previously imported data according to Nimbus's retention policy, but stop all new synchronization. Provide a separate data deletion process where required.

# **9. What about the CMS?**

Your super admin should be able to monitor the integration without needing access to the merchant's Lightspeed credentials.

I would provide an integration management section.

**CMS — Integration Management**

# **125**

Total integrations

# **118**

Connected

# **5**

Sync errors

# **2**

Disconnected

**Recent integrations**

|Merchant|Provider|Status|
|---|---|---|
|Store A|Lightspeed|Connected|
|Store B|Lightspeed|Sync error|
|Store C|Shopify|Connected|
|Store D|Square|Syncing|

The CMS should allow the super admin to:

- See connected merchants.
    
- See connection status.
    
- See the last successful synchronization.
    
- See synchronization failures.
    
- Inspect error logs.
    
- Trigger a resynchronization.
    
- Disable an integration if necessary.
    
- Inspect synchronization history.
    

Don't expose merchant access tokens to CMS users either.

# **10. My recommended Nimbus data architecture**

I want to emphasize something here.

You're integrating three different providers:

- Shopify
    
- Lightspeed X Series
    
- Square
    

You should not create three completely separate transaction systems.

Instead, create a unified transaction model.

**Provider integrations**

Shopify · Lightspeed · Square

**Provider-specific integration services**

Authentication · API clients · Webhooks · Sync

**Normalization layer**

Maps external data into Nimbus models

**Nimbus core data**

Customers

Transactions

Line items

Payments

**Nimbus APIs**

Merchant Web · Customer Mobile · CMS

This architecture lets you add a fourth integration in the future without redesigning your entire transaction system.

A few important rules for this architecture:

1. Every imported record must retain its original provider and external ID.
    
2. Use a unique identity for an external record based on provider, connected account and external ID.
    
3. Make synchronization idempotent. Receiving the same event twice must not create duplicate transactions.
    
4. Preserve the original provider data where useful for troubleshooting and remapping.
    
5. Store normalized values separately from raw provider payloads.
    
6. Keep provider-specific fields available without forcing every provider into identical fields.
    
7. Never let one merchant access another merchant's data.
    

Also, use a date-based Lightspeed API version rather than building a new integration against an old unversioned contract. Lightspeed explicitly recommends date-based versions for new integrations.

x-series-api.lightspeedhq.com+1

# **11. What you actually need from Lightspeed X Series**

For Nimbus, I would concentrate on these API areas:

|Lightspeed API area|Nimbus requirement|Priority|
|---|---|---|
|Authorization|Connect merchant account|Essential|
|Retailer|Identify connected store|Essential|
|Outlets|Identify store locations|High|
|Customers|Import customer records|Essential|
|Sales|Import transactions|Essential|
|Sale line items|Display purchase details|Essential|
|Payments|Display payment information|High|
|Webhooks|Receive change notifications|Essential|

You do NOT need the Lightspeed Payments API for this use case. That API is intended for integrating payment processing into the Lightspeed POS checkout screen, not simply reading historical transactions.

x-series-api.lightspeedhq.com+1

Here are the official resources I recommend studying:

- [Lightspeed X Series API documentation](https://x-series-api.lightspeedhq.com/?utm_source=chatgpt.com) — your main documentation.
    
- [OAuth authorization](https://x-series-api.lightspeedhq.com/v2026.07/docs/authorization?utm_source=chatgpt.com) — understand merchant connection.
    
- [OAuth scopes](https://x-series-api.lightspeedhq.com/v2026.04/docs/scopes?utm_source=chatgpt.com) — understand permissions.
    
- [Webhooks](https://x-series-api.lightspeedhq.com/docs/webhooks?utm_source=chatgpt.com) — understand event synchronization.
    
- [Quick Start](https://x-series-api.lightspeedhq.com/v2026.07/docs/quick_start?utm_source=chatgpt.com) — understand the actual integration process.
    

# **12. The complete integration lifecycle**

This is the architecture I would settle on for Nimbus.

1. **Merchant initiates connection**
    
    Merchant Web → Connect Lightspeed.
    
2. **OAuth authorization**
    
    Merchant authorizes Nimbus through Lightspeed.
    
3. **Credential storage**
    
    Nimbus securely stores tokens, retailer identity and granted permissions.
    
4. **Initial synchronization**
    
    Background workers import customers, sales, line items, payments and outlets.
    
5. **Normalization**
    
    External Lightspeed data becomes Nimbus-standard data.
    
6. **Continuous synchronization**
    
    Webhooks, polling and reconciliation keep the database updated.
    
7. **Customer identity matching**
    
    Associate verified external customer records with Nimbus customer accounts.
    
8. **Data consumption**
    
    Merchant Web and Customer Mobile retrieve data from Nimbus APIs.
    
9. **Monitoring**
    
    CMS monitors integrations, synchronization and failures.
    

## **My advice before you start implementing**

There are three things I would settle before writing integration code:

1. **Lightspeed account identity:** How you uniquely identify a retailer and support multiple stores belonging to one Nimbus merchant.
    
2. **Customer identity mapping:** How an existing Lightspeed customer becomes a verified Nimbus customer.
    
3. **Transaction ownership:** How you guarantee that a customer sees only their own transactions, even if the same email exists in multiple merchant accounts.
    

These are more important architectural decisions than which API endpoint you call first.

And one final distinction: make sure you're integrating specifically with **Retail X Series**, not Lightspeed R Series or another Lightspeed product. Their APIs and authorization models are not interchangeable.

My recommendation is to treat this as the first provider implementation of a reusable Nimbus integration architecture. Get the data model, connection lifecycle and synchronization engine right here, and Shopify and Square will become much easier to integrate afterward.



Yes. **Your overall approach is correct. This is the right OAuth integration flow for Lightspeed X Series.**

What you've implemented follows the expected architecture:

1. Merchant clicks **Connect Lightspeed** in Nimbus Web.
    
2. Frontend calls `lightspeedInstallUrl`.
    
3. Backend generates a secure OAuth `state`.
    
4. Backend constructs the Lightspeed authorization URL.
    
5. Backend returns that URL to the frontend.
    
6. Frontend redirects the merchant to Lightspeed.
    
7. Merchant authorizes Nimbus.
    
8. Lightspeed redirects to your backend callback.
    
9. Backend exchanges the authorization code for access and refresh tokens.
    
10. Backend saves the integration credentials and starts the initial import.
    

Correct approach

A few important confirmations:

- Using OAuth Authorization Code Grant is correct.
    
- Generating and storing a hashed state is good.
    
- Associating the state with the initiating merchant is correct.
    
- Having the backend handle the callback is correct.
    
- Keeping tokens on the backend is correct.
    
- Starting the import after successful connection is correct.
    

**One distinction:** `lightspeedInstallUrl` is not actually installing anything. It's initiating the OAuth authorization process. The actual connection is established when Lightspeed authorizes Nimbus and your backend successfully exchanges the authorization code.

So yes, continue with this architecture. You don't need to redesign this connection flow.

I'd move on to the next part: **fetching and importing Lightspeed customers and sales after successful authorization.**

This is a very important distinction to understand before you continue building Nimbus.

**Lightspeed X Series is the POS system used by a retailer. The thing your merchant connects to Nimbus is their Lightspeed retailer account, not simply a shop name.**

Let's understand the terminology first, then we'll walk through OAuth and exactly what Nimbus receives and saves.

# **1. What exactly is Lightspeed X Series?**

[Welcome to Retail POS (X-Series) – Lightspeed Retail (X-Series)](https://images.openai.com/static-rsc-4/I13JMl-qxhvmz3ED0mlSljpnBgckiUCO41wgnolXmfc4R8Hql7hG1ag_KEdB_PVmqQc01g5CyF8qX2lNlkndrbPlw6FiP6b4OpF2Y8_A_eSftYb1dOmrHMGMP2-rfBGn4yyIJdwb1GnSKydTes2u1j6qHTFVHNIywjhQXKlPi_I?purpose=inline)

[Navigating in Retail POS (X-Series) – Lightspeed Retail (X-Series)](https://images.openai.com/static-rsc-4/Q-Di4mUJl9p-5lRU2YrehiQ5IR2NiTbsZB3pw2r--QqoWDfytnKNvk_Q4scKFOEaRyg9OCWkARBHFak3DKX9kUt-ZZ-dIKP5J-3RNOpScOe_MpyiRwhA7SQO8K0O7JgKDK-uGgIicArLjCSUCp8mHhPm4FPZavJt7MKEnRWtw3w?purpose=inline)

[Setting up outlets and registers – Lightspeed Retail (X-Series)](https://images.openai.com/static-rsc-4/6aO1tzmY_qc5eWIVQfk823r153h_hoqffDdl0wSabIq8Gq8JJu3RsF0yGT_zg0yySO8Ccstx-vNVkUKMw39oqkXVG4Ep5MxSdxL5xHUshr5k7CPJZqidGweMcfxw1do8rwRm1xRQ0Hj6n4yP0WA7_KMcEoh31yYTh83LMe_uADw?purpose=inline)

5

Think of a real-world retail business.

Suppose someone owns a clothing business called **ABC Clothing**.

They use Lightspeed X Series to manage their retail operations.

Inside Lightspeed, they might have:

- A retailer account representing their business.
    
- One or multiple retail outlets.
    
- Employees who operate the POS.
    
- Customers who purchase products.
    
- Products and inventory.
    
- Sales and payments.
    

The important terminology:

|Term|Meaning|
|---|---|
|Retailer|The business/account using Lightspeed|
|Outlet|A physical store location belonging to the retailer|
|Register|A POS register used to process sales|
|User|An employee or staff member using Lightspeed|
|Customer|Someone purchasing from the retailer|
|Sale|A recorded transaction|
|Product|An item sold by the retailer|

For example:

**ABC Clothing**

Lightspeed Retailer

One retailer account

**Outlet A**

Kathmandu

Register 1

Register 2

**Outlet B**

Pokhara

Register 1

Register 2

**Shared retailer data**

- Customers
    
- Products
    
- Sales
    
- Payments
    

This is an illustrative business structure, not a requirement that every retailer has multiple outlets or registers.

**Now, here's the important thing for Nimbus:**

A merchant can connect their Lightspeed retailer account, and Nimbus can retrieve the information associated with that authorized retailer account, subject to the permissions granted.

You don't need the merchant to connect each customer, outlet or register individually.

# **2. What exactly is being connected?**

Let's say Nimbus has a merchant called Bibek's Store.

Bibek already uses Lightspeed X Series.

He opens Nimbus Web and clicks Connect Lightspeed.

At this point, Nimbus is asking:

> Can I get permission to access Bibek's Lightspeed retailer account?

Not:

> Can I connect to Bibek's physical shop?

The distinction matters because a Lightspeed retailer account represents the business's data and configuration, while outlets represent the physical locations within that account.

For example, one retailer might have:

- One outlet in Kathmandu.
    
- Another outlet in Pokhara.
    
- Several registers across those outlets.
    
- Thousands of customers.
    
- Thousands of sales.
    

**One OAuth connection authorizes Nimbus to access the retailer account.**

You can then retrieve its retailer information and associated data according to the granted scopes.

The official API even provides a `GET /retailer` endpoint to retrieve the retailer entity after authorization.

x-series-api.lightspeedhq.com+1

# **3. Understand OAuth in Lightspeed**

Before we discuss the flow, understand that there are two different applications/accounts involved.

Your developer account

**Nimbus application**

You register Nimbus with Lightspeed as an API application.

Lightspeed gives you:

- Client ID
    
- Client Secret
    
- Redirect URL configuration
    

Merchant account

**ABC Clothing's Lightspeed account**

This is the retailer account that wants to connect to Nimbus.

It has:

- Retailer identity
    
- Outlets
    
- Customers
    
- Sales
    
- Other business data
    

This is an important distinction.

Your `LIGHTSPEED_CLIENT_ID` and `LIGHTSPEED_SECRET` belong to the Nimbus application.

They are not the merchant's credentials.

You register Nimbus once. Different merchants authorize that same Nimbus application to access their individual retailer accounts.

Lightspeed calls these developer applications _Add-ons_.

x-series-api.lightspeedhq.com+1

## **4. The complete OAuth process**

Let's use a concrete example.

- Application: Nimbus
    
- Merchant: ABC Clothing
    
- Lightspeed retailer: ABC Clothing's existing Lightspeed account
    

## **OAuth authorization flow**

1. **Nimbus creates an authorization request**
    
    Your backend constructs a URL containing:
    
    - `client_id`
        
    - `redirect_uri`
        
    - `response_type=code`
        
    - `scope`
        
    - `state`
        
    
2. **Merchant visits Lightspeed**
    
    The merchant is redirected to Lightspeed and signs in if necessary.
    
3. **Lightspeed identifies the retailer**
    
    Lightspeed knows which retailer account the merchant is authorizing against.
    
4. **Merchant grants permission**
    
    An appropriately authorized Lightspeed user approves the Nimbus application's requested access.
    
5. **Lightspeed sends an authorization code**
    
    Lightspeed redirects to Nimbus's callback URL with the authorization code, domain prefix, state and granted scope information.
    
6. **Nimbus exchanges the code**
    
    Your backend sends the code, client ID, client secret, redirect URI and grant type to Lightspeed's token endpoint.
    
7. **Lightspeed returns tokens**
    
    Nimbus receives an access token and refresh token, along with token metadata and retailer domain information.
    

The official documentation describes this exact Authorization Code Grant flow.

x-series-api.lightspeedhq.com

### **What is the authorization code?**

The authorization code is a temporary credential.

It basically means:

> Lightspeed has received authorization for this application to access this retailer account. Here is a temporary code that the application can exchange for API credentials.

It is not the actual API access token.

The code is short-lived (10 minutes) and single-use.

x-series-api.lightspeedhq.com

### **What is the domain prefix?**

This is particularly important in Lightspeed X Series.

Imagine the callback gives you:

CALLBACK DATA — SIMPLIFIED EXAMPLE

```
code = temporary_code_123
domain_prefix = abcclothing
state = generated_state
scope = customers:read sales:read
```

The `domain_prefix` identifies the retailer's API hostname.

For example:

`https://abcclothing.retail.lightspeed.app`

Your backend uses this to communicate with the correct Lightspeed retailer.

It is not the retailer's display name, and it is not an OAuth token.

This is why your code constructs the token URL using:

`https://${domainPrefix}.retail.lightspeed.app/api/1.0/token`

That is the correct concept.

### **What happens when you exchange the authorization code?**

Your backend makes a POST request to the retailer-specific token endpoint.

Lightspeed returns something conceptually like this:

Example token response

```
{
  "access_token": "ACCESS_TOKEN",
  "token_type": "Bearer",
  "expires": 1790000000,
  "expires_in": 86400,
  "refresh_token": "REFRESH_TOKEN",
  "domain_prefix": "abcclothing",
  "scope": "customers:read sales:read"
}
```

Illustrative values, not actual credentials.

Now Nimbus has the credentials required to communicate with ABC Clothing's Lightspeed account.

There are two important tokens:

|Token|Purpose|
|---|---|
|Access token|Used to access Lightspeed APIs|
|Refresh token|Used to obtain a new access token when needed|

The access token is sent in the API request's Bearer authorization header.

The refresh token is used against the token endpoint to obtain replacement credentials. Lightspeed rotates refresh tokens, so every replacement must be persisted.

x-series-api.lightspeedhq.com+1

# **5. What exactly does Nimbus need to save?**

This is probably the most important part of your question.

I would divide the information into three groups.

## **A. Nimbus application credentials**

These are global application credentials, not merchant-specific credentials.

|Information|Where to keep it|
|---|---|
|Client ID|Environment/secrets configuration|
|Client Secret|Secure secrets configuration|
|Redirect URI|Environment/configuration|
|Requested scopes|Environment/configuration|

You don't create a new client ID and secret for every merchant.

## **B. Merchant's OAuth credentials**

These are different for every connected retailer account.

I would save something like this in your `pos_integrations` collection:

|Field|Example|Purpose|
|---|---|---|
|`merchantId`|Nimbus merchant ID|Owner of integration|
|`provider`|LIGHTSPEED_X|Integration provider|
|`externalAccountId`|Lightspeed retailer ID|Actual external account identity|
|`domainPrefix`|abcclothing|API hostname identifier|
|`accessToken`|Encrypted token|API access|
|`refreshToken`|Encrypted token|Renew API access|
|`tokenExpiresAt`|Date|Access token expiry|
|`scope`|`customers:read sales:read`|Granted permissions|
|`status`|CONNECTED|Integration status|
|`connectedAt`|Date|Connection timestamp|

Notice something:

**I would store** `externalAccountId` **and** `domainPrefix` **separately.**

Why?

The domain prefix tells you which API hostname to use.

The external account ID identifies the actual Lightspeed retailer entity. You should retrieve that from the retailer API after connecting.

Don't use the domain prefix as your only external account identity.

## **C. Retailer information**

After successfully obtaining the access token, make another API request:

`GET /api/{version}/retailer`

This retrieves the retailer entity.

For example, conceptually:

```
{
  "id": 12345,
  "name": "ABC Clothing",
  "timezone": "Asia/Kathmandu",
  "currency": "NPR"
}
```

This is illustrative, not a guaranteed response schema.

You can use the actual retailer response to populate your integration's metadata:

- Retailer ID
    
- Retailer name
    
- Currency
    
- Timezone
    
- Country
    
- Other relevant configuration
    

You may keep this information in the integration record or in a separate retailer metadata collection, depending on your existing data model.

The important thing is that Nimbus now knows which Lightspeed retailer it has connected to.

# **6. What does the OAuth connection actually give Nimbus?**

Let's make this crystal clear.

Suppose ABC Clothing authorizes Nimbus.

Nimbus does NOT immediately receive all the customers and transactions in the OAuth callback.

Instead, Nimbus receives credentials that allow it to request permitted information from Lightspeed.

For example:

Authorized

**Nimbus can access ABC Clothing's Lightspeed account**

Using the access token and granted scopes.

**What Nimbus can request**

Retailer information

Read

Outlets

Read

Customers

Read

Sales

Read

Payments

Read

Assuming the corresponding read permissions have been granted. These are API capabilities, not data automatically delivered during OAuth.

For your use case, the scopes worth investigating are:

|Scope|Why|
|---|---|
|`retailer:read`|Retailer information|
|`outlets:read`|Store locations|
|`customers:read`|Customer records|
|`sales:read`|Sales and payments|
|`webhooks`|Manage webhook subscriptions|

Lightspeed's current scope reference identifies these permissions and their purposes.

x-series-api.lightspeedhq.com

I would start with the minimum permissions needed. You don't need product write, customer write, sales write or inventory write for Nimbus's read-only use case.

One subtle point: `sales:read` covers sales and payments in the scope description, so don't automatically request `payments:read` as an additional scope without checking the specific endpoints you're going to use.

# **7. How this maps to your existing implementation**

Looking at your code, you already have the core OAuth connection process.

Here's how I would map your existing methods to the Lightspeed concepts:

|Your method|What it does|
|---|---|
|`lightspeedInstallUrl()`|Initiates authorization|
|`handleCallback()`|Receives authorization response|
|`consumeState()`|Validates and consumes OAuth state|
|Token exchange|Obtains API credentials|
|`posIntegrationService.connect()`|Saves retailer integration|
|`initialImport.startInBackground()`|Starts data import|
|`disconnectLightspeed()`|Disconnects the integration|

Your overall flow is correct.

The next thing I'd do after your existing token exchange is retrieve the retailer entity and save its actual external ID and metadata.

That way, the integration record represents a known Lightspeed retailer, rather than merely a collection of OAuth tokens.

# **8. What happens after the connection?**

Here's the complete picture using ABC Clothing.

1. **Merchant connects Lightspeed**
    
    Nimbus stores the retailer's OAuth credentials and identity.
    
    Connected
    
2. **Nimbus retrieves retailer information**
    
    Gets the retailer ID, name, timezone, currency and other relevant information.
    
3. **Nimbus retrieves outlets**
    
    Gets the physical store locations belonging to the retailer.
    
4. **Nimbus retrieves customers**
    
    Fetches the existing customer records.
    
5. **Nimbus retrieves sales**
    
    Fetches historical transactions and their associated details.
    
6. **Nimbus saves everything**
    
    Stores normalized records in its own database, retaining their original Lightspeed IDs.
    
7. **Nimbus continues synchronization**
    
    Uses webhooks and background synchronization to keep the information updated.
    

## **9. One important thing about API versions**

There is a small distinction worth knowing for your implementation.

Lightspeed's OAuth token endpoint currently uses:

`/api/1.0/token`

But its newer resource APIs use date-based versions, for example:

`/api/2026-07/retailer`

`/api/2026-07/customers`

`/api/2026-07/sales`

So don't confuse the OAuth token endpoint version with the version of the actual resource APIs. New integrations should use date-based API versions.

x-series-api.lightspeedhq.com+1

## **10. Official documentation to keep open**

These are the four most useful resources for understanding what we've discussed.

1. [Lightspeed X Series — API Introduction](https://x-series-api.lightspeedhq.com/v2026.07/docs/introduction?utm_source=chatgpt.com)
    
2. [Lightspeed X Series — Authorization](https://x-series-api.lightspeedhq.com/v1.0/docs/authorization?utm_source=chatgpt.com)
    
3. [Lightspeed X Series — OAuth Scopes](https://x-series-api.lightspeedhq.com/v2026.04/docs/scopes?utm_source=chatgpt.com)
    
4. [Lightspeed X Series — Quick Start](https://x-series-api.lightspeedhq.com/v2026.07/docs/quick_start?utm_source=chatgpt.com)
    

**My final takeaway for you:**

Think of the integration like this:

- Nimbus has one registered application with Lightspeed.
    
- Each merchant has their own Lightspeed retailer account.
    
- Each merchant authorizes Nimbus to access that retailer account.
    
- Nimbus receives account-specific tokens and a domain prefix.
    
- Nimbus uses those credentials to retrieve the retailer's data.
    
- Nimbus stores that data in its own database.
    

That's the relationship. Once you understand that, the rest of the integration becomes much easier to reason about.