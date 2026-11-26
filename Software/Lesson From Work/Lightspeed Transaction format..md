## Sale
```js
{
  "merchantId": "673c51b2e2a91d1a7a101001", // mongoDB
  "integrationId": "673c51b2e2a91d1a7a102001", // mongoDB
  "externalId": "sale-ls-5001", // unnique lightspeed payment ID
  "customerExternalId": "customer-ls-3001", // lightspeed customer
  "outletId": "outlet-ls-01", // lightspeed store where the sale originated
  "registerId": "register-ls-01", // register associated with the sale
  "userId": "staff-ls-01", // lightspeed member who did the sale for that store
  "invoiceNumber": "INV-1001",
  "source": "USER", // origin/type of the sale
  "sourceId": null, // external source reference
  "completeOpenSequenceId": null, // Register sequence reference associated with sale completion.
  "accountsTransactionId": null, // Legacy accounting transaction reference.
  "hasUnsyncedOnAccountPayments": false, // Indicates unsynchronized on-account payments.
  "status": "CLOSED", // Legacy sale status, not returned by the current date-based API.
  "state": "closed", // Current sale lifecycle state. pending, parked, closed, voided
  "note": "Customer purchased clothing items.", // cashier le rakheko note Cashier-entered sale note.
  "shortCode": "H72AIn", // Short sale reference used for lookup/loyalty.
  "returnFor": null, // Reference to the original sale when this is a return.
  "returnIds": [], // IDs/references of returns associated with the sale.
  "lightspeedCreatedAt": "2026-11-18T10:25:00.000Z", // When Lightspeed created the sale.
  "lightspeedUpdatedAt": "2026-11-18T10:31:00.000Z", // When Lightspeed last updated the sale.
  "saleDate": "2026-11-18T10:30:00.000Z", // Business timestamp of the sale.
  "lightspeedDeletedAt": null, // Provider deletion timestamp.
  "lineItems": [ // products/service in the sale
    {
      "id": "line-ls-001",
      "product_id": "product-ls-101",
      "name": "T-shirt",
      "quantity": 2,
      "price": "25.00",
      "total": "50.00",
      "status": "CONFIRMED"
    },
    {
      "id": "line-ls-002",
      "product_id": "product-ls-102",
      "name": "Jeans",
      "quantity": 1,
      "price": "100.00",
      "total": "100.00",
      "status": "CONFIRMED"
    }
  ],
  "payments": [ // payment for the sale. 
    {
      "id": "pay-ls-1001",
      "amount": "100.00",
      "date": "2026-11-18T10:30:00.000Z",
      "type": {
        "name": "Card"
      }
    },
    {
      "id": "pay-ls-1001",
      "amount": "200.00",
      "date": "2026-11-28T10:30:00.000Z",
      "type": {
        "name": "Card"
      }
    },
    {
      "id": "pay-ls-1002",
      "amount": "50.00",
      "date": "2026-11-18T10:31:00.000Z",
      "type": {
        "name": "Cash"
      }
    }
  ],
  "adjustments": [],
  "externalApplications": [],
  "attributes": [],
  "version": 1,
  "ecomCustomCharges": null,
  "totalTax": "13.64", // Total tax on the sale.
  "totalPrice": "136.36", // Total price excluding tax.
  "taxes": [ // Breakdown of taxes applied to the sale.
    {
      "id": "tax-gst-01",
      "amount": "13.64"
    }
  ],
  "receiptNumber": "R-1001", // Receipt number generated for the sale.
  "totalPriceIncl": "150.00", // total price including tax
  "totalSurcharge": "0.00" // 
}

```

| Functionality                        | API / Permission                |
| ------------------------------------ | ------------------------------- |
| Retrieve sales                       | `GET /api/{version}/sales`      |
| Retrieve individual sale             | `GET /api/{version}/sales/{id}` |
| Access sales and payment information | `sales:read`                    |
| Retrieve products                    | `GET /api/{version}/products`   |
| Read product information             | `products:read`                 |
| Receive sale updates                 | Webhooks                        |
|                                      |                                 |
## Lightspeed payment

```js
{
  "merchantId": "673c51b2e2a91d1a7a101001", // our mongoDB
  "integrationId": "673c51b2e2a91d1a7a102001", // our mongoDB
  "externalId": "pay-ls-1001", // unique payment lightspeed id
  "saleExternalId": "sale-ls-5001", // sales Id
  "customerExternalId": "customer-ls-3001", // customer Id for lightspeed
  "registerId": "register-ls-01", // lightspeed register where payment was recorded
  "registerOpenSequenceId": "session-ls-001", // Identifier of the register's opening session.
  "outletId": "outlet-ls-01", // Lightspeed outlet/store identifier
  "retailerPaymentTypeId": "retailer-payment-card-01", // Merchant-configured payment type identifier.
  "paymentTypeId": "2", // Lightspeed system payment type identifier. 1 for CASH
  "name": "Card", // Display name of the payment method. Cash, Card, Visa
  "amount": "100.00", // Amount recorded for this payment, not the total sale. could be null. 
  "paymentDate": "2026-11-18T10:30:00.000Z", // transactionDate
  "lightspeedDeletedAt": null, // Provider deletion timestamp, when present
  "externalAttributes": [],
  "externalApplications": [],
  "surcharge": null, // Payment surcharge information
  "sourceId": null, // Source reference associated with the payment.
  "billingAddressId": null, // Billing address reference.
}

```

For sales and payment there is no category, there is category for product. and category is merchant classified, one merchant can classify tshirt for clothing but other might do it for apparel. category could be missing, it might change. 

Correct, mate. **Lightspeed Retail X-Series payments do not have a native spending category** such as Groceries, Shopping, Clothing, Food, or Entertainment.

A payment contains information about **how much was paid, when it was paid, and how it was paid**, rather than what the customer purchased.

|Field|Example|Available in payment?|
|---|---|---|
|Payment ID|`payment-001`|Yes|
|Amount|`100.00`|Yes|
|Payment date|`2026-11-18`|Yes|
|Payment method|Card, Cash|Yes|
|Spending category|Clothing, Groceries|**No**|
|Product category|T-shirts, Electronics|**No**|

### **How can Nimbus get a category?**

You have three options:

1. **From Lightspeed products:** Use the parent sale's line items, retrieve their product categories, and derive a category for the payment.
    
2. **From merchant category:** Classify the merchant as Groceries, Retail, Restaurant, etc., and apply that category to purchases at that merchant.
    
3. **From Basiq:** If the payment is reliably matched to a Basiq transaction, use Basiq's available transaction categorization and map it to your Nimbus categories.
    

### **My recommendation for Nimbus**

Since you don't want to process individual products just to get categories, I would recommend **merchant-based categorization as the default**, with Basiq categorization used as an additional signal when available.

For example:

```
{
  "paymentId": "payment-001",
  "amount": "100.00",
  "paymentMethod": "Card",
  "category": "Shopping",
  "categorySource": "MERCHANT_CLASSIFICATION"
}
```

Here, `category` and `categorySource` are **Nimbus-generated fields**, not native Lightspeed payment fields.

One important rule: if a Basiq transaction is matched to a Lightspeed payment, don't automatically assume Basiq's category is correct. Keep the category source and allow for conflicting classifications.

**Bottom line:** Lightspeed payment = no spending category. You must derive the category from the sale's products, the merchant's business type, or a matched Basiq transaction.