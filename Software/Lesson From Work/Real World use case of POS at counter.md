## LightSpeed
## How it works at the counter

A shopper never needs an account. The cashier has three options at the register:

1. **Do nothing.** The sale is attached to the `WALKIN` customer. This is by far the most common case.
2. **Search for an existing customer** by name, email, phone or customer code and attach them to the sale.
3. **Create a new customer on the spot.** The register has an "add customer" form, so the cashier types a name and maybe an email or phone. It takes about 10 seconds.

Cashiers usually bother with option 2 or 3 only when there's a reason: loyalty points, store credit, an on-account balance, an emailed receipt, or a return or warranty lookup. The shopper can say no, so most people never get a record. Some stores push it ("want to join our rewards?"), and others never ask.

Online stores work differently. If the merchant uses an ecommerce channel, the customer is created automatically at checkout.

## Can a normal person be a customer?

Yes, and that's the only kind there is. A "customer" in Lightspeed is just a row in the merchant's contact list. The person doesn't sign up for Lightspeed, doesn't log in, and may not even know the record exists. Lightspeed users are the store staff, and customers are only data the store keeps about its shoppers. This is why the data is so uneven: it's whatever a cashier typed.

## Can a Lightspeed store be created easily?

Creating one is easy: sign up, get a trial, and you have a store in minutes. Your `developerdemo6awyu6` domain prefix is a demo store. That's why the test customers have names like "fsdf fdfsd" with junk company names. A real merchant takes much longer to set up, because they have to add products, registers, tax, payment types and hardware, but making an account for testing is fast.

## Is the customer shared with other stores?

It depends on what "other stores" means:

- **Same business, multiple locations** (one Lightspeed account with several outlets): yes. Customers belong to the account, so the same record can be used at any outlet, and this is the usual chain-store setup.
- **Different businesses** (two separate Lightspeed accounts): no. Each account has its own customer database. If the same person shops at two unrelated Lightspeed stores, they're two unrelated records with different IDs. Lightspeed doesn't link them.

## What this means for your app

- Your `integrationId + externalId` identity is correct, because an external ID is only unique within one merchant's account.
- Expect many sales to point at the walk-in customer, and many real customers to have only a name or an email.
- Any matching across merchants or providers has to be your own logic, for example by email, because the POS systems won't do it for you.

I'm describing X-Series behavior from general knowledge, and some details (like exact register fields) can differ by plan or region.

## Same person, same phone, two Lightspeed businesses

Lightspeed treats them as two unrelated customers. Nothing links them.

Each business has its own Lightspeed account with its own customer database. The same person at Store A and Store B gets two records with different IDs, and Lightspeed doesn't compare phone numbers or emails across accounts. It couldn't anyway, because one merchant isn't allowed to see another merchant's customers.

## But in your app, it depends on what you're building

Your `integrationId + externalId` identity keeps those two records separate, which is correct at the POS level. The question is what your own system should do:

- **Two different merchants in your app** (Store A and Store B are separate customers of yours): keep them separate. Each merchant should only see their own customers. Merging across merchants would leak one business's customer data to another, which is a privacy problem and probably a contract problem too. Your `merchantId` scoping already protects against this.
- **One merchant with two Lightspeed integrations** (the same business connected two Lightspeed accounts): here a merge may make sense. Your `MerchantCustomerSyncService` already builds `identityKeys` like `email:...`, so the same person with the same email would collapse into one merchant customer. That seems to be your intent.
- **Your own end-user app** (the person has an account in your app and shops at many stores): this is the case where cross-store matching is useful. You'd link the person's app account to each store's customer record, but only with their consent and with a strong, verified identifier, ideally a verified phone or email.

## The risk with phone matching

Matching on phone is tempting, but be careful:

- **Phone numbers are shared.** Family members, partners, and workplaces often use one number, so two different people can have the same phone.
- **Phones get recycled.** A number can pass to a new owner.
- **Formats differ.** `0412 345 678`, `+61412345678` and `412345678` are the same number, and without normalization they won't match. Bibek's number has no country code, so it can't be matched reliably at all.
- **Unverified data.** A cashier typed it, and nobody confirmed it belongs to that person. Your projection shows `isVerified: false`.

So I'd treat phone as a weak hint, not an identity key. Use it only to suggest a possible match, and require a verified email or a verified phone (an OTP, for example) before you actually link records.

## Within one business, duplicates still happen

Even inside a single Lightspeed account, the same person can have two records, for example "John Smith" with a phone and "J. Smith" with an email, created by different cashiers. Lightspeed doesn't merge those automatically. Your sync will treat them as separate people unless the email matches, so plan for duplicates even in the simplest case.

This is general knowledge of how Lightspeed X-Series works, and your exact setup (such as whether you're linking multiple accounts per merchant) is what decides which of the cases above applies to you.

## Square
## How it works at the counter in Square

Square is similar to Lightspeed, with a few differences.

**At the register (Square POS):**

1. **Do nothing.** The sale has no customer attached. Unlike Lightspeed, Square has no `WALKIN` placeholder record. The payment simply has no `customer_id`, so there's nothing to filter out in your sync.
2. **Search for an existing customer** and attach them to the sale.
3. **Add a new customer** from the checkout screen with a name, email, phone, or a mix.

**Customers get created automatically more often than in Lightspeed**, and this is the biggest difference:

- **Card on file or receipts:** If a shopper asks for an emailed or texted receipt, Square can create a customer from that email or phone.
- **Loyalty and gift cards:** Signing up for loyalty usually requires a phone number, so those customers tend to have one.
- **Online orders:** Square Online, invoices, appointments (Square Appointments), and online checkout create customers from the form the shopper fills in.
- **Customer Directory auto-creation:** Square has a setting that can add customers from payments automatically, so a store can end up with many records the cashier never typed.

## Can a normal person be a customer?

Yes, same idea as Lightspeed. A customer is a row in the merchant's directory. The person doesn't need a Square account. Square also has a separate consumer side (Cash App, and the Square receipt/loyalty experience), but that isn't the same thing as the merchant's customer record.

## Can a Square store be created easily?

Yes, even more easily than Lightspeed. Sign up, get a Square account, and you can take payments in minutes. There's also a Sandbox for developers with test accounts, which is probably what you'll use. Like your Lightspeed demo, sandbox data tends to be junk test names.

## Are customers shared between stores?

- **Same Square account with multiple locations:** yes. The Customer Directory belongs to the merchant account, and all locations use it.
- **Different Square accounts:** no. Each merchant has its own directory, and IDs are unrelated. Square doesn't link the same person across businesses.

## What this means for your app

- **No walk-in record to skip,** but expect orders and payments with no `customer_id`. Make sure your sales and payments import handles a missing customer, not just a null one.
- **Phone is more common than in Lightspeed,** because of loyalty and receipts, but it's still optional. Square needs only one of name, company, email or phone, so some customers have no email at all.
- **Duplicates are likely.** Auto-created customers mean the same person can appear twice, for example once from a receipt phone number and once from an online order email. Square can merge customers, but not every merchant does. Your identity logic shouldn't assume one person equals one record.
- **Records can be merged or deleted on Square's side,** so handle customer-deleted and customer-merged webhooks if you subscribe to them.

As before, this is from general knowledge of Square's behavior, and details like the auto-create setting can vary by account, region, and product (Retail, Restaurants, Appointments). Check Square's current docs for anything your code depends on.

## Same person, same phone, two Square businesses

Same answer as Lightspeed: Square treats them as two unrelated customers.

Each Square merchant account has its own Customer Directory. Store A and Store B each create their own record with a different `customer_id`, and Square doesn't link them or compare phone numbers across accounts. When you connect through OAuth, each merchant authorizes your app separately, so you only see that merchant's directory.

## Differences from Lightspeed

- **Phone is more likely to be present and cleaner.** Loyalty, receipts, and online orders often collect a phone number. I believe Square's API also rejects clearly invalid numbers, so you'll see fewer junk values like `42323123` with no country code. Still verify this, and normalize numbers yourself.
- **Customers get created without a cashier.** The same person can appear at Store A through a receipt, and at Store B through a loyalty signup, without anyone deliberately creating them.
- **Square has a consumer side.** Shoppers can have a Square or Cash App identity (saved cards, digital receipts), but this isn't exposed to merchants as a cross-store customer ID. Don't count on it for matching.

## Duplicates within one Square account

This is more likely than in Lightspeed. Because customers are auto-created, the same person can end up as two records in one directory, for example one with a phone from a receipt and one with an email from an online order. Square's dashboard has a merge tool for duplicates, but merchants have to use it. When a merge happens, one customer ID disappears, so handle the `customer.deleted` and merge-related webhooks, or periodically re-sync, so you don't keep a stale record.

## What to do in your app

- **Different merchants in your app:** keep them separate. Your `merchantId` scoping handles this, and merging across merchants would leak data.
- **One merchant with two Square accounts:** matching on a verified or normalized email is reasonable. Treat phone as a hint only.
- **Cross-store matching for your own end users:** link only through a verified identifier and the person's consent.
- **Identity key:** `integrationId + externalId` is correct for the raw record, same as Lightspeed. For merchant-level matching, normalize phone numbers to E.164 and use email first. Phone alone is weak because of shared family numbers and recycled numbers.

Same caveat as before: this is general knowledge of Square's behavior, so check the current Customers API docs for merge and phone validation details before relying on them.

## Shopify
## How it works in Shopify

Shopify is the most different of the three, because it's online-first and the customer record is tied closely to checkout.

**Online store (the main case):**

- A shopper can check out as a **guest** or with an **account**. Merchants choose whether accounts are required, optional, or disabled.
- At checkout the shopper must give an email (or a phone number, depending on store settings). Shopify then creates a customer record from that, even for guest checkout, so most orders end up linked to a customer.
- So unlike Lightspeed, there's no placeholder record. Nearly every online order has a real customer, and that customer usually has at least an email.

**In person (Shopify POS):**

1. **Do nothing.** The order is completed with no customer attached. Like Square, there's no `WALKIN` record, so the order just has a null customer.
2. **Search for an existing customer** and attach them to the cart.
3. **Add a new customer** at the POS with a name, email, or phone.

Cashiers often skip this for walk-in shoppers, so POS orders without customers are common.

## Can a normal person be a customer?

Yes. A customer is a row in the merchant's store, and the person doesn't need a Shopify account. Shopify also has a separate shopper side (Shop app, Shop Pay), but that is not the same as the merchant's customer record. An account on a store's own login is a "customer account" for that one store only.

## Can a Shopify store be created easily?

Yes, very easily. Sign up for a trial and you have a store in minutes. For development, Shopify has **development stores** through a Partner account, which is probably what you'll use. Like your Lightspeed demo, test stores are full of fake customers and orders.

## Are customers shared between stores?

- **Same Shopify store with multiple locations:** yes, customers belong to the store, and POS locations share them.
- **Different Shopify stores:** no. Each store has its own customer list, and IDs are unrelated. There are tools for syncing across stores in Shopify Plus setups, but that's not default behavior.

## What this means for your app

- **Email is more reliable here** than in Lightspeed, but not guaranteed. Phone-only customers exist, and POS-created customers can have just a name.
- **Customers can exist with no orders,** for example from newsletter signups, manual creation or imports, so don't assume every customer has sales.
- **Orders can have no customer,** especially from POS, drafts, and some third-party channels. Handle a missing `customer` on orders.
- **Consent is separate for email and SMS,** and it's tracked per channel. Check the specific consent before sending anything.
- **Duplicates happen** when a shopper uses different emails, or checks out as a guest and later creates an account. Don't assume one person equals one customer record.
- **Customers can be merged or deleted,** and Shopify has privacy webhooks for customer data requests and erasure that apps are expected to handle.

Same caveat as before: this is general knowledge of Shopify's behavior, and settings like required contact method and account type vary per store, so check Shopify's current docs for anything your code depends on.

## Same person, same phone, two Shopify stores

Same answer as the other two: Shopify treats them as two unrelated customers.

Each store has its own customer list, with its own customer IDs, and Shopify doesn't expose any link between them to merchants. Your app connects to each store through its own install and access token, so you only see that store's data.

## Differences from Lightspeed and Square

- **Shopify has a shopper identity across stores, but you can't use it.** Shop Pay and the Shop app let a person check out quickly at many stores, and their details can be prefilled. That is Shopify's own consumer identity. It isn't exposed to merchants or apps as a cross-store customer ID, so don't plan around it.
- **Email and phone are normally unique within one store.** If a merchant tries to create a second customer with an email or phone already in use, Shopify usually rejects it. This makes exact-match duplicates less common than in Square. Duplicates still happen with different emails, a typo, or a guest checkout followed by a later account.
- **Phone is stored in a normalized format.** Shopify generally expects numbers in E.164 (`+61412345678`), so phones are cleaner than Lightspeed's `42323123`. A customer's phone and the phone on an address can still differ.
- **Access to customer data is restricted.** Shopify requires apps to be approved for "protected customer data" before they can read names, emails, phones, and addresses. A development store lets you test, but a production app needs that approval, or those fields will come back empty or blocked. This is the Shopify-specific problem most likely to catch you out.

## Duplicates within one store

Merchants can merge customers in the admin, and there is an API for it. After a merge, one customer ID disappears, so you should handle the delete and merge webhooks (check the current topic names), or re-sync periodically. Otherwise you'll keep a stale record, the same issue as with Square.

## What to do in your app

- **Different merchants in your app:** keep them separate. Your `merchantId` scoping handles this, and merging across merchants would leak one business's customers to another.
- **One merchant with two Shopify stores:** matching on a normalized email is reasonable. Phone should be a hint only, since family members share numbers and numbers get recycled.
- **Cross-store matching for your own end users:** link only through a verified identifier and the person's consent.
- **Identity key:** `integrationId + externalId` is right for the raw record. For merchant-level matching, use email first, then E.164 phone as a weak hint.
- **Privacy webhooks:** you must handle `customers/data_request` and `customers/redact`, which Shopify requires of apps. When one arrives, you need to delete or anonymize that customer in both your raw table and your merchant customer table.

This is general knowledge of Shopify's behavior, and the exact rules (uniqueness, merge webhooks, protected data levels) change over time, so check the current Admin API docs before building on them.

