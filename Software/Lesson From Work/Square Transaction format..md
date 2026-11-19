## **1. What Square data matters for Nimbus?**

| Square resource | What it represents                         | Importance |
| --------------- | ------------------------------------------ | ---------- |
| **Payments**    | Actual payments processed through Square   | Critical   |
| **Orders**      | Items purchased, totals, taxes, discounts  | Critical   |
| **Customers**   | Customer identity and contact information  | Critical   |
| **Refunds**     | Money returned to customers                | Critical   |
| **Locations**   | Store/outlet associated with a transaction | High       |
| **Catalog**     | Products, categories, and product metadata | Optional   |
| **Merchants**   | Connected Square merchant information      | High       |
## Orders
```js

const squareOrder = {
  id: "DdAqB2EA809TH2vfUL6ulwMxM4ZZY", // square Id
  location_id: "LQB78P44W0KCJ", // Square location/store where the order was created. Multiple locations can belong to the same merchant
  customer_id: "6G33C2M1DC7SCWJ0BWB6STHGNM", // can be null
  state: "COMPLETED", // Possible values: OPEN, COMPLETED, CANCELED, DRAFT.
  created_at: "2026-11-09T08:30:07.621Z",  // When Square initially created the order. May differ from the actual payment timestamp.
  updated_at: "2026-11-09T08:30:08.513Z", // When Square last updated the order.
  // Useful for incremental synchronization.
  closed_at: "2026-11-09T08:30:08.526Z",   // When the order was closed. Usually present on completed orders.
  version: 3, // Order version, incremented when the order changes. Useful for detecting updates.
  source: { // Source application used to create the order.Example: Virtual Terminal, Square POS, other integrations.
    name: "Virtual Terminal"
  },
  created_by_team_member_id: "TMUMnXRAbpT5eDUr",// Square team member who created the order. Not the customer ID.
  line_items: [ //items
    {
      uid: "44bbd316-5e10-5d16-c31d-3d470133b3e6", // Unique identifier of this line within the order.
      quantity: "1",

      // Type of item.
      // CUSTOM_AMOUNT means a manually entered amount.
      // ITEM means a catalog-backed item.
      item_type: "CUSTOM_AMOUNT",

      // Unit price before line-level adjustments.
      // Square uses integer minor currency units.
      // 23100 USD cents = $231.00.
      base_price_money: {
        amount: 23100,
        currency: "USD"
      },
      // Gross amount before discounts.
      gross_sales_money: {
        amount: 23100,
        currency: "USD"
      },
      // Tax applied to this line item.
      total_tax_money: {
        amount: 0,
        currency: "USD"
      },
      // Total discount applied to this line item.
      total_discount_money: {
        amount: 0,
        currency: "USD"
      },
      // Final amount for this line item.
      total_money: {
        amount: 23100,
        currency: "USD"
      },
      // Total price associated with item variations.
      variation_total_price_money: {
        amount: 23100,
        currency: "USD"
      },
      // Service charges allocated to this line item.
      total_service_charge_money: {
        amount: 0,
        currency: "USD"
      },
      // Card surcharges allocated to this line item.
      total_card_surcharge_money: {
        amount: 0,
        currency: "USD"
      },
      // Additional item-specific notes.
      // Not reliable for determining transaction category.
      note: "this si escond one for second email",
      // Square pricing exclusion configuration.
      // Empty object in your sample.
      pricing_blocklists: {}
    }
  ],
  // Total tax charged for the entire order.
  total_tax_money: {
    amount: 0,
    currency: "USD"
  },
  // Total discount applied to the order.
  total_discount_money: {
    amount: 0,
    currency: "USD"
  },
  // Total tip associated with the order.
  total_tip_money: {
    amount: 0,
    currency: "USD"
  },
  // Total service charges applied to the order.
  total_service_charge_money: {
    amount: 0,
    currency: "USD"
  },
  // Total card surcharge charged for the order.
  total_card_surcharge_money: {
    amount: 0,
    currency: "USD"
  },
  // Total order amount, including applicable charges.
  // This is the purchase amount, not necessarily
  // the amount of any individual payment.
  total_money: {
    amount: 23100,
    currency: "USD"
  },
  // Amount still owed on this order.
  // Zero means no remaining amount is due.
  net_amount_due_money: {
    amount: 0,
    currency: "USD"
  },
  // Net financial amounts associated with the order.
  // Useful for refunds, returns, and reconciliation.
  net_amounts: {
    total_money: {
      amount: 23100,
      currency: "USD"
    },
    tax_money: {
      amount: 0,
      currency: "USD"
    },
    discount_money: {
      amount: 0,
      currency: "USD"
    },
    tip_money: {
      amount: 0,
      currency: "USD"
    },
    service_charge_money: {
      amount: 0,
      currency: "USD"
    },
    card_surcharge_money: {
      amount: 0,
      currency: "USD"
    }
  },
  // Payment tenders associated with this order.
  // One order may contain multiple tenders.
  // Important for matching Square payments with Basiq.
  tenders: [ // how order was paid
    {
      // Unique identifier for this tender.
      id: "cr9NF95KndQlOmXrzy3pqITFlRFZY",
      // Square location where payment was accepted.
      location_id: "LQB78P44W0KCJ",
      // Legacy transaction reference.
      // In your sample, it matches the order ID.
      // Do not assume this always happens.
      transaction_id: "DdAqB2EA809TH2vfUL6ulwMxM4ZZY",
      // When the tender was created.
      // May differ from order created_at or closed_at.
      created_at: "2026-11-09T08:30:08.264Z",
      // Amount paid using this tender.
      // 23100 cents = $231.00 USD.
      amount_money: {
        amount: 23100,
        currency: "USD"
      },
      // Payment method.
      // Examples: CARD, CASH, OTHER, SQUARE_GIFT_CARD.
      type: "CARD",
      // Associated Square Payment API ID.
      // Use this to fetch the full Payment object.
      payment_id: "cr9NF95KndQlOmXrzy3pqITFlRFZY",
      // Additional payment note.
      note: "this si escond one for second email",
      // Card details, present for card tenders.
      // Not available for every payment type.
      card_details: {
        // Card payment processing state.
        // CAPTURED means the card payment was captured.
        status: "CAPTURED",
        card: {
          // Card network.
          // Examples: VISA, MASTERCARD, AMERICAN_EXPRESS.
          card_brand: "VISA",
          // Last four digits of the card.
          // Useful for payment method display.
          // Not sufficient to identify a customer.
          last_4: "1111",
          // Card expiry month (1-12).
          exp_month: 11,
          // Card expiry year.
          exp_year: 2026,
          // Square-generated card fingerprint.
          // Can help identify repeated use of a card.
          // Must not be used alone to merge customers.
          fingerprint:
            "sq-1-yrrYXfcck5Z3u_yGJjRVpnoWzxwFaI2r2LexazGaamNZk9bmGh-KTVXWo3agzPxK1g",
          // Card funding type.
          // Examples: CREDIT, DEBIT, UNKNOWN.
          card_type: "CREDIT",

          // Whether the card is prepaid.
          // Examples: PREPAID, NOT_PREPAID, UNKNOWN.
          prepaid_type: "NOT_PREPAID",

          // First six digits of card number.
          // Identifies issuer range, not cardholder.
          bin: "411111"
        },

        // How the card information was entered.
        // Examples: KEYED, SWIPED, EMV, CONTACTLESS.
        entry_method: "KEYED"
      }
    }
  ],

  // Automatic pricing settings for this order.
  pricing_options: {
    // Whether eligible discounts are applied automatically.
    auto_apply_discounts: false,
    // Whether applicable taxes are applied automatically.
    auto_apply_taxes: true
  }
};

```

## Payment
```js

const squarePayment = {
  id: "cr9NF95KndQlOmXrzy3pqITFlRFZY",
  created_at: "2026-11-09T08:30:08.264Z",
  // Changes may occur after authorization, capture, or refund.
  updated_at: "2026-11-09T08:30:09.312Z",
  // Original payment amount, in the currency's minor units.
  // USD 23100 cents = $231.00.
  // This excludes any separately recorded tip.
  amount_money: {
    amount: 23100,
    currency: "USD"
  },
  // Current payment lifecycle status.
  // Possible: APPROVED, PENDING, COMPLETED, CANCELED, FAILED.
  // COMPLETED means the payment has completed processing.
  // Nimbus: Use to classify completed payments.
  status: "COMPLETED",
  // Duration Square allows before delayed capture handling.
  // ISO 8601 duration: PT168H = 168 hours (7 days).
  // Relevant primarily to delayed-capture payments.
  // Not the actual payment processing time.
  delay_duration: "PT168H",
  // Source used to fund the payment.
  // Examples: CARD, CASH, EXTERNAL, WALLET, BANK_ACCOUNT.
  // Nimbus: Payment method, not spending category.
  source_type: "CARD",
  // Card-specific payment information.
  // May be absent for non-card payment methods.
  card_details: {
    // Card payment processing status.
    // CAPTURED indicates a captured card payment.
    // Other card states can occur during authorization.
    // Not a replacement for the top-level payment status.
    status: "CAPTURED",
    // Card metadata; does not contain the full card number.
    card: {

      // Payment card network.
      // Examples: VISA, MASTERCARD, AMERICAN_EXPRESS,
      // DISCOVER, JCB, UNIONPAY, OTHER_BRAND.
      card_brand: "VISA",

      // Last four digits of the payment card.
      // Suitable for display: Visa •••• 1111.
      // Not a unique customer or payment identifier.
      last_4: "1111",

      // Card expiration month (1 through 12).
      exp_month: 11,

      // Card expiration year.
      exp_year: 2026,

      // Square-generated fingerprint for this card.
      // Can indicate repeated use of the same card
      // within Square's applicable fingerprinting scope.
      // Nimbus: Do not merge customers or transactions
      // using this fingerprint alone.
      fingerprint:
        "sq-1-yrrYXfcck5Z3u_yGJjRVpnoWzxwFaI2r2LexazGaamNZk9bmGh-KTVXWo3agzPxK1g",

      // Card funding classification.
      // Examples: CREDIT, DEBIT, UNKNOWN.
      card_type: "CREDIT",

      // Whether the card is a prepaid card.
      // Examples: PREPAID, NOT_PREPAID, UNKNOWN.
      prepaid_type: "NOT_PREPAID",

      // Bank Identification Number (BIN).
      // Identifies the card issuer/range.
      // Not the customer's bank account number.
      // Nimbus: Usually unnecessary for transaction display.
      bin: "411111"
    },
    // How card details were captured.
    // KEYED = manually entered.
    // Other examples: SWIPED, EMV, CONTACTLESS,
    // ON_FILE, ECOMMERCE, depending on payment flow.
    entry_method: "KEYED",
    // Card Verification Value check result.
    // CVV_ACCEPTED means the CVV verification passed.
    // Other outcomes include rejected or unavailable.
    cvv_status: "CVV_ACCEPTED",
    // Address Verification Service check result.
    // AVS_ACCEPTED means the address verification passed.
    // Other outcomes include rejected or unavailable.
    avs_status: "AVS_ACCEPTED",
    // Description that may appear on the cardholder's
    // bank statement.
    // Important for merchant-name matching with Basiq.
    // Not guaranteed to match Basiq's final description.
    statement_description: "SQ *DEFAULT TEST ACCOUNT",
    // Card authorization and capture timestamps.
    card_payment_timeline: {
      // When the payment was authorized.
      // Authorization is not necessarily final settlement.
      authorized_at: "2026-11-09T08:30:08.391Z",
      // When Square captured the authorized payment.
      // Useful for payment timing and reconciliation.
      captured_at: "2026-11-09T08:30:08.499Z"
    }
  },
  // Square location where this payment was processed.
  // One merchant can have multiple Square locations.
  // Nimbus: Map to the correct merchant/outlet.
  location_id: "LQB78P44W0KCJ",
  // Associated Square order identifier.
  // Connects the payment to the order and its items.
  // Multiple payments may reference the same order.
  // Can be absent for some payment scenarios.
  // Nimbus: Essential for purchase-level grouping.
  order_id: "DdAqB2EA809TH2vfUL6ulwMxM4ZZY",
  // Square's payment risk assessment.
  // Primarily relevant to fraud/risk processing.
  // Usually unnecessary for customer-facing history.
  risk_evaluation: {
    // When the risk evaluation was generated.
    created_at: "2026-11-09T08:30:08.391Z",
    // Risk classification assigned by Square.
    // Examples: NORMAL, MODERATE, HIGH.
    // Do not interpret as proof of fraud.
    risk_level: "NORMAL"
  }
  // Processing fees charged by Square to the merchant.
  // These are NOT extra amounts paid by the customer.
  // Can contain multiple fee entries.
  processing_fee: [
    {
      // When this fee became effective.
      effective_at: "2026-11-09T10:30:09.000Z",

      // Fee classification.
      // INITIAL = initial processing fee.
      // Other fee types may be returned.
      type: "INITIAL",

      // Fee amount in minor currency units.
      // 824 cents = $8.24 USD.
      // Nimbus: Do not add to customer transaction amount.
      amount_money: {
        amount: 824,
        currency: "USD"
      }
    }
  ],
  // Merchant-entered payment note.
  // Free text; may be empty or absent.
  // Not a reliable identifier or spending category.
  note: "this si escond one for second email",
  // Square customer associated with the payment.
  // Can be absent.
  // Not necessarily the cardholder or Nimbus user.
  // Nimbus: Requires verified customer association.
  customer_id: "6G33C2M1DC7SCWJ0BWB6STHGNM",
  // Total amount associated with this payment,
  // including any applicable tip.
  // Here it equals amount_money because no tip exists.
  // Nimbus: Useful when matching the bank debit amount.
  total_money: {
    amount: 23100,
    currency: "USD"
  },
  // Amount approved for this payment.
  // Can differ from the final captured/total amount
  // in payment flows involving adjustments.
  approved_money: {
    amount: 23100,
    currency: "USD"
  },
  // Square employee associated with the payment.
  // May be absent in other payment flows.
  // Not a customer identifier.
  employee_id: "TMUMnXRAbpT5eDUr",
  // Short receipt reference generated by Square.
  // Useful for displaying or searching receipts.
  // Not a replacement for the unique payment ID.
  receipt_number: "cr9N",
  // URL of the Square-hosted receipt.
  // This sample points to Square Sandbox.
  // Nimbus: Optional receipt-viewing functionality.
  receipt_url:
    "https://squareupsandbox.com/receipt/preview/cr9NF95KndQlOmXrzy3pqITFlRFZY",
  // Action Square takes when delayed capture expires.
  // Example: CANCEL.
  // Relevant mainly to authorization/capture flows.
  // Does not mean this completed payment was canceled.
  delay_action: "CANCEL",
  // Deadline for delayed capture.
  // Not the transaction date or settlement date.
  // Can remain populated on a completed payment.
  delayed_until: "2026-11-16T08:30:08.264Z",
  // Team member associated with the payment.
  // May match employee_id, as it does in this sample.
  team_member_id: "TMUMnXRAbpT5eDUr",
  // Information about the application that created
  // or processed the payment.
  application_details: {

    // Square product responsible for the payment.
    // VIRTUAL_TERMINAL = Square Virtual Terminal.
    // Other Square products/integrations may appear.
    square_product: "VIRTUAL_TERMINAL",

    // Square application identifier.
    // This example is a sandbox application.
    // Not a customer, merchant, or transaction ID.
    application_id:
      "sandbox-sq0idb-BbZvlaIkgSYnVUI4rpSedg"
  },
  // Opaque Square version token for this payment.
  // Used for supported version-aware update operations.
  // Not a timestamp or monetary value.
  // Nimbus: Preserve if needed; don't parse its contents.
  version_token:
    "sDwhN94GH1j44ySL2w6S66xeEFjUzvwXiTIxEnGDmlP6o"
};

```

| Method                         | Category source                           | Reliability                         |
| ------------------------------ | ----------------------------------------- | ----------------------------------- |
| Square Catalog                 | Categories assigned to purchased products | High when available                 |
| Square merchant classification | Merchant's business type                  | Broad classification                |
| Basiq                          | Category of a matched bank transaction    | Useful, but requires reconciliation |

Then, if Nimbus later matches that payment with a Basiq transaction that has a category, you can use the Basiq category as an enrichment source.

Alternatively, you can classify the merchant using verified business information.

**My recommendation:** Don't require product-category lookups for every Square payment. Use a category fallback strategy:

**Square Catalog → verified merchant category → matched Basiq category → Uncategorized.**

The ordering between merchant and Basiq categories can be adjusted based on reliability, but never silently treat an inferred category as one supplied directly by Square.