Each version in lightspeed is supported for 12 months.  If the version is not supported, then the most previous version which is supported is auto used. Next releases is available in beta so that we could integrate our changes. 

Based on what I found, you probably don't pay Lightspeed anything for API access to X-Series (the product your `retail.lightspeed.app` endpoints belong to). You do need Lightspeed's approval before going live with many merchants, and the exact commercial terms are worth confirming with them.

**API fees**

- The developer terms say API access is currently provided free of charge, and Lightspeed reserves the right to charge fees in the future with at least 60 days' advance notice. That text is on the older Vend terms page, so check that the current developer agreement still says the same.
- Registering as a developer and creating an app has no listed cost. Your developer account is separate from any Lightspeed retailer account.

**Approval for production**

- Lightspeed's docs say you only need to get their approval for production-ready, public applications. Since your app will connect many merchants, that is you.
- One integrator's help page says that if their app is not approved in Lightspeed, the integration is limited to a maximum of 30 stores. That is one vendor's description, not an official Lightspeed statement, but expect a cap on unapproved apps. Ask Lightspeed what it is for yours.
- Approval seems to go through a Developer Certification or Partnerships review. Another source says publishing requires Developer Certification / Partnerships review.

**Who pays what**

- Each merchant needs their own Lightspeed subscription, and they pay Lightspeed directly. That is outside your costs.
- Lightspeed also says that costs from providers other than Lightspeed may include building, testing, hosting, support and maintenance. For you that means your own infrastructure, the queue, and compliance work.

**Other terms to check**

- The terms say Lightspeed can modify, suspend or terminate API access, and that a merchant must expressly consent through Lightspeed's interface. Your OAuth consent flow covers that.
- The terms also say you must not request more personal data than needed and must not retain it longer than necessary. You currently store full `rawData`, including customer PII and payment details. Be ready to justify that during review, and set a retention policy.
- Apps must be able to move to newer API versions after deprecation.

**What to do**

1. Register at the Lightspeed developer portal and request production or partner review.
2. Ask them directly: is there any fee or revenue share, is there a store cap before approval, and what do they require on security and data retention?
3. Get the answers in writing, since pricing terms can change.

If you want, I can help you list what the review will likely look at: encrypted tokens, no PII in logs, data retention, and disconnect and revocation behavior.



In an effort to ensure the safety of customer's data, we have implemented a function which will automatically redacts all credit card related data coming into the API.  
When the server detects credit card data on certain fields, it will be replaced with a generic non-alphanumeric character without reporting an error and a `200 OK` response will be returned.

## 

Affected fields on API entities

[](https://x-series-api.lightspeedhq.com/docs/data_security#affected-fields-on-api-entities)

- Customer
    - first_name
    - last_name
    - company_name
    - note
    - postalAddress1
    - postalAddress2
    - postalCity
    - postalPostcode
    - postalState
    - postalSuburb
    - physicalAddress1
    - physicalAddress2
    - physicalCity
    - physicalPostcode
    - physicalState
    - physicalSuburb
    - custom_field_(1-4)
- Sale
    - note



## **What I meant by "all merchant subscription tiers"**

Lightspeed charges merchants for using its retail POS software. Different merchants may have different Lightspeed plans.

For example:

- Merchant A has a basic Lightspeed subscription.
    
- Merchant B has a higher-tier subscription.
    
- Merchant C has an older or custom Lightspeed subscription.
    

Your concern is whether **all three merchants can connect their Lightspeed X-Series accounts to Nimbus**, regardless of their subscription.


Hello Lightspeed Developer Support,

We are developing Nimbus, a transaction aggregation platform that allows merchants to connect their existing Lightspeed Retail X-Series accounts through OAuth 2.0.

Nimbus is a read-only integration. We retrieve authorized merchant customer records, sales, and embedded payment information to display transaction history in our merchant web application and securely matched purchase history in our customer mobile application.

Could you please confirm:

1. Can merchants on all active Lightspeed Retail X-Series subscription plans authorize our OAuth application?
    
2. Are there any API access fees, per-request charges, or per-connected-merchant fees?
    
3. Is partner approval or a commercial agreement required to launch this integration publicly?
    
4. Are there any restrictions on storing and displaying authorized sales, payment, and customer data in Nimbus?
    
5. What is the process for obtaining approval to connect more than 30 retailer accounts?
    

Thank you.


So your concern is strictly about **moving from testing to production and connecting real merchants who already pay for Lightspeed**.

You want to know whether:

1. Real merchants can connect their existing Lightspeed X-Series stores without upgrading their subscription.
    
2. Lightspeed will charge Nimbus for connecting those merchants or making API requests.
    
3. Your existing OAuth application needs production approval.
    
4. Anything will stop working when you move from test stores to real stores.


**### **Your actual production situation**

Suppose Nimbus launches with the same OAuth application you're using for testing.

- **First 10 real stores:** Within the documented 30-store limit, assuming no other connected stores consume the allowance.
    
- **At 30 connected stores:** You've reached the unapproved application's documented limit.
    
- **Store 31:** You need Lightspeed approval to expand beyond the limit.
    

Lightspeed's documentation doesn't clearly establish whether existing test-store connections count differently, so don't assume you have 30 additional production slots.

### **What you should do now**

Since your implementation is finished, I would focus on three things before launching:

1. **Check the Lightspeed developer dashboard.** Is your Nimbus application marked `Approved` or `Not Approved`?
    
2. **Request production approval.** Explain that Nimbus is a read-only application fetching authorized customer, sale, and payment records.
    
3. **Confirm commercial terms.** Ask whether approval, production API access, or merchant connections incur any charges. No published per-request price is not the same as a guarantee of zero fees.**