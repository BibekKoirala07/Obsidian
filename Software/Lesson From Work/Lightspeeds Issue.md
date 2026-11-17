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