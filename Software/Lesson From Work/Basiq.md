enrich access should be enabled by Basiq. it isn't enabled by default at all. 

Here it is enrich transacton with merchant name. https://api.basiq.io/v2.1/reference/enrich-api-1?utm_source=chatgpt.com
## Institution
Basiq supports different kinds of financial services like banks, Retirement Fund, Credit Union. 
```js
{
  "type": "institution", // could be credit union. 
  "id": "AU01001",
  "name": "National Australia Bank Limited",
  "shortName": "NAB",
  "institutionType": "Bank",
  "country": "Australia",
  "serviceName": "NAB Connect",
  "serviceType": "Personal Banking", // could be Card Access, Business Banking
  "authorization": "user",
  "stage": "live", // beta, alpha
  "status": "operational"
}
```
How the user will be connected to customer will not be decided by basiq but that financial institution.

There are two different way of obtaining financial data. 
- Web connector DDC approach
- Open Banking

Now imagine Bank provides an official API that allows authorised third-party applications to access customer-approved financial information..Open Banking means basiq is able to communicate with bank's official API. In Australia, Open Banking operates under the Consumer Data Right (CDR) framework.
## Authentication
## Users
## Consents
## Connections
## Jobs
## Accounts
## Transactions
```js
{
  "type": "list",
  "count": 100, // total in this list 
  "size": 500, // max allowed per page
  "data": [
    {
      "type": "transaction", // transactions
      "id": "d3de1ca1", // id of transactions
      "status": "posted", // pending, it becomes pending to posted
      "description": "string", // suplied by financial institution
      "reference": "string", // not guranteed to identify the merchants
      "amount": "123.12",
      "currency": "AUD",
      "balance": "123.12", // acount balance after transaction is applied
      "direction": "debit", // credit
      "class": "payment", // payment, transfer, cash-withdrawl, bank-fee, loan interest, direct-credit, refund, means what kinds of financial activity it was
      |Class|Meaning|Example|
|`payment`|A payment for goods or services|Paying at a restaurant|
|`transfer`|Money moved between accounts|Transferring $500 to savings|
|`cash-withdrawal`|Cash withdrawn|ATM withdrawal|
|`bank-fee`|Fee charged by the bank|Monthly account fee|
|`loan-interest`|Interest charged on a loan|Monthly loan interest|
|`direct-credit`|Money deposited directly into an account|Salary payment|
|`refund`|Money returned from a previous purchase|Refund from Amazon|

Debit
Money going out
Money leaving the account
`bank-fee`
`payment`
`cash-withdrawal`
`transfer` 
`loan-interest`
    

Credit

Money coming in

Money entering the account

- `refund`
    
- `direct-credit`
    
- `interest`
    
- `transfer`
    
- `loan-repayment`
	  "account": "d3de1ca1", // it is particular bank account, could be saving also.
      "institution": "AU00000", // bank
      "connection": "d3de1ca1", // a linked banking connection
      "transactionDate": null, // when customer recorded the transactions, could be null
      "paymentDetails": { // how payment was done. 
        "bpay": {
          "billerCode": "string", // identified the company
          "billerName": "string", // company name
          "crn": "string" // Customer reference number
        },
        "apcaNumber": "string"
      },
      "postDate": "2018-11-02T00:00:00Z", // when bank recorded the transaction was done
      "subClass": { // it is more detailed view of class, can be null, only for payment
        "code": 722,
        "title": "Cafes and Restaurants"
      },
      "enrich": {
        "location": { // not always available.
          "country": "Australia",
          "formattedAddress": "1/39 E Esplanade, Manly NSW 2095",
          "geometry": {
            "lat": "-33.79988520000001",
            "lng": "151.2858021"
          },
          "postalCode": "2095",
          "route": "E Esplanade",
          "routeNo": "29",
          "state": "NSW",
          "suburb": "Manly"
        },
        "cleanDescription": "string", // clean version of description above. Bank descriptions are often messy: Unnecessary spaces Abbreviated names. Terminal identifiers. Transaction references. Location codes.
        "tags": [
          "conduct:dishonour" // tag_name:tag_value
          // tag_name could be card, conduct, governmentPayment, income, liability, mortage and even third party tags such as apple pay, google, paypal and so on
        ],
        "merchant": {
          "id": "ae4a051c-4791-11e8-8750-0a87c0279fe8", // identifier for the merchant
          "businessName": "Garfish Manly",
          "ABN": "90065628864",
          "logoMaster": "https://enrich-enrichmerchantslogobucket-x62p53eh5ld9.s3-ap-southeast-2.amazonaws.com/officeworks-master.png", // merchant main logo
          "logoThumb": "https://enrich-enrichmerchantslogobucket-x62p53eh5ld9.s3-ap-southeast-2.amazonaws.com/officeworks-thumb.png", // smaller logo image
          "phoneNumber": {
            "international": "+61 2 9977 0707",
            "local": "(02) 9977 0707"
          },
          "website": "http://garfish.com.au/garfish-manly/"
        },
        "category": {
          "matchType": "merchantfull",
          "matchScore": "1.0",
          "mcc": {
            "code": "txMccCode-25028",
            "title": ""
          },
          "anzsic": {
            "division": {
              "title": "Accommodation and Food Services",
              "code": "H"
            },
            "subdivision": {
              "code": "45",
              "title": "Food and Beverage Services"
            },
            "group": {
              "code": "451",
              "title": "Cafes, Restaurants and Takeaway Food Services"
            },
            "class": {
              "title": "Cafes and Restaurants",
              "code": "4511"
            },
            "subclass": {
              "title": "Cafes and Restaurants",
              "code": "451100"
            }
          }
        }
      },
      "links": {
        "account": "https://au-api.basiq.io/users/6a52015e/accounts/31eb30a0",
        "institution": "https://au-api.basiq.io/institutions/AU00000",
        "self": "https://au-api.basiq.io/users/6a52015e/transactions/2082c765"
      }
    }
  ],
  "links": { // navigation link including paginations
    "self": "https://au-api.basiq.io/users/ea3a81/transactions",
    "next": "https://au-api.basiq.io/users/6a52015e/transactions?next=bf1ec9d4"
  }
}
```

`direction`
Is money coming in or going out?
`class`
What financial activity happened?
`subClass`
What more specific transaction type is this?
`category`
What industry or spending category does it belong to?
`merchant`
Which business received or sent the money?

before learning category, we need to understand MCC and ANZSIC.
MCC is used in card payment system. It ia about classification of business into industries based on type of goods or service they provide. 
like 5411 = Gorcery
5812 = Restaurants. 

Suppose a customer purchases something from McDonalds. Payment network may classify merchant as a restaurant using MCC. So a standard. but not every transaction comes with MCC. A bank purchase might not have MCC or bank transfer might not or what else right. 

ANZSIC is industry classification used in Australia, It provides a hierachy.

Basiq's Enrich documentation explains that the service first classifies the financial transaction, then derives merchant, location and industry categorization where applicable.

So never make category mandatory in table. 
## Enrich
## Webhooks
## Affordability
## Production
|                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |     |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --- |
| Hi Bibek<br><br>Could you please [get in touch](https://www.cuscal.com/contact-us/) with our sales team, who will be happy to assist you.  <br>﻿Please do keep in mind that to enable access to live Open Banking data, there are several onboarding steps that must be completed. The sales team can run you through these steps.<br><br>  <br>﻿To enable access to live Open Banking data, there are several onboarding steps that must be completed. Many of these steps can run **in parallel**, allowing your team to begin building and integrating while contractual and security requirements are completed. We appreciate some businesses may have completed steps 1 and 2.<br><br>  <br><br>Below is an overview of the process.<br><br>  <br><br>![✅](https://fonts.gstatic.com/s/e/notoemoji/17.0/2705/32.png) **How the Basiq Onboarding Process Works**<br><br>While the **MSA and CDR Security Assessment** are underway, your team can begin the following:<br><br>**1. Build & Integrate with the Basiq API**<br><br>- Access the Basiq sandbox environment.<br><br>- Begin integrating your application with the Basiq API.<br><br>- Typical timeframe: **~3–4 weeks**.<br><br>**2. Develop & Test**<br><br>- Develop your use case and test functionality in the sandbox.<br><br>- Basiq provides documentation, implementation guides, and technical support.<br><br>![✅](https://fonts.gstatic.com/s/e/notoemoji/17.0/2705/32.png) These activities can run in parallel and **do not require production approval**.<br><br>  <br><br>![🔐](https://fonts.gstatic.com/s/e/notoemoji/17.0/1f510/32.png) **Required Steps for Live Data Access**<br><br>**3. CDR Security Assessment**<br><br>- A mandatory assessment conducted with Basiq.<br><br>- Confirms your systems meet Consumer Data Right (CDR) security requirements.<br><br>- Requires internal DevSecOps or Cloud expertise.<br><br>- Typical timeframe: **~1 week**.<br><br>**4. Master Services Agreement (MSA)**<br><br>- Execution of the Basiq Master Services Agreement.<br><br>- Establishes the commercial and legal relationship.<br><br>- Must be completed before go‑live.<br><br>**5. CDR Setup**<br><br>- Following the security assessment, Basiq works with the **ACCC** to establish your CDR access.<br><br>- Timing is dependent on ACCC review and approvals.<br><br>- Typical timeframe: **up to 2 weeks**.<br><br>**6. Enable Institutions for Open Banking**<br><br>- Open Banking institutions are enabled via the Basiq dashboard.<br><br>- This unlocks access to live Open Banking data once approvals are complete.<br><br>![🚀](https://fonts.gstatic.com/s/e/notoemoji/17.0/1f680/32.png) **Go Live**<br><br>- Production (live) data access is enabled once:<br><br>- ![✅](https://fonts.gstatic.com/s/e/notoemoji/17.0/2705/32.png) Security assessment is completed<br><br>- ![✅](https://fonts.gstatic.com/s/e/notoemoji/17.0/2705/32.png) MSA is signed<br><br>- ![✅](https://fonts.gstatic.com/s/e/notoemoji/17.0/2705/32.png) CDR setup and institution enablement are complete<br><br>**(Optional) Migration Support**<br><br>- If you are migrating from an existing provider, Basiq can assist.<br><br>- This is optional and scoped separately, as outlined in the MSA.<br><br>![📝](https://fonts.gstatic.com/s/e/notoemoji/17.0/1f4dd/32.png) **Key Notes**<br><br>- **Live Open Banking data access is only enabled after all compliance steps are completed**.<br><br>- Building and integration can begin early to reduce time to go‑live.<br><br>• Timelines are indicative and may vary due to complexity and third‑party dependencies (e.g. ACCC).<br><br>  <br>﻿Hope this makes sense ![😊](https://fonts.gstatic.com/s/e/notoemoji/17.0/1f60a/32.png)  Please let me know if you have any questions! |     |
| \|   \|<br>\|---\|<br>\|Kind regards,<br><br>**Faranak Milani**\|<br>\|![](https://ci3.googleusercontent.com/meips/ADKq_NYxqh0CUAEjdKZ0h1dEZlMlhQIbPbXaFhSzOtMyKHeQWRAzHJ7vjVY5Qh6OQf1A28CrO6DCzGH3hRh3JV2BRZhv9mks3WnquUW89vWuSiE4l1MQDCmBy10mLaz40Ok0BhwJ8pH9xMGQwXiYg5jfc1D0wyCHsbblVuWtgx47JJ8DJ2xFXxH-OJ2EfXzAVkAmXQFTj5WuxUK23-taWpyA0Zu6H9acbdcsPI8pNlJuR5TNOhNIVKNY2nfHUmk5whB-5IwXvW65fNYOs_al-j_Nsao3eYw5xq6WNE1woimLWine468XPi-r-H0fw3_zMaV0fSi0rtZraURQRUYsac7XuLljqLBapgAN56uvS7QgtwQcmJfYHQxYhZTC3RdqJwK6nlYCKvfIxz0IekMvlWLGIZyGqRdT97GRr6eMcw0=s0-d-e1-ft#https://downloads.intercomcdn.com/i/o/p9ife7zc/1439325030/03944ad827368cb376e2dcb6cec4/basiq+logo+blue.png?expires=1806635502&signature=3ec33cdba407c9c4ae823664e40274d0fac13d4c5c82eb1f59a87305dec17b53&req=dSQkH8p8mIFcWfMW2XS%2Fic%2B%2BfWEN2TfCMfujm4KZNNyvsM7dm6BRKMQZDHRg%0AZMMPn%2FebjbM%3D%0A)\|                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |     |
CDR is Australia's regulatory framework that allows consumers to securely share their financial data with accredited or authorized organizations.

For example:
1. A customer wants to connect their Commonwealth Bank account to your application.
2. Your application uses Basiq to initiate the connection.
3. Basiq facilitates access to the customer's banking data.
4. The data is retrieved under Australia's CDR requirements.

But because this involves sensitive financial information, your organization's infrastructure and security practices must satisfy the applicable requirements.

The security assessment examines things like:
- How you store sensitive customer information.
- Encryption at rest and in transit.
- Authentication and authorization.
- API security.
- Access controls.
- Logging and monitoring.
- Incident response procedures.
- Infrastructure and cloud security.

Think of MSA as the formal contract between your organization and Basiq.

It establishes things like:

- Commercial terms.
- Responsibilities of each party.
- Data handling obligations.
- Liability.
- Service availability.
- Legal obligations.

You can think of it this way:

CDR Security Assessment = Are your systems sufficiently secure?

MSA = Are both organizations legally and commercially agreeing to work together?

#### 3. CDR Setup

This is where regulatory approval comes into play.

Basiq works with the ACCC (Australian Competition and Consumer Commission) to establish your CDR access.

The important distinction:

Basiq being integrated into your application does not automatically mean your organization is authorized to access live CDR data.

There are regulatory requirements that must be satisfied.

The ACCC is involved in Australia's CDR regulatory framework, including accreditation and authorization arrangements.

Don't assume that completing Basiq's security assessment automatically makes your company a CDR-accredited organization.

My recommendation: Since you're implementing Basiq as a backend developer, ask your team whether the MSA, security assessment, and CDR setup are already underway. Meanwhile, continue building and testing your transaction synchronization pipeline using sandbox data.
## Pricing
I'll read that as: what does it mean for you, in plain terms. One caution first: I can't verify that text. Some of it matches what I found on Basiq's pricing pages (the $0.50 and $0.25 per user, the $250 startup plan, billing per user, not per connection). Other parts I didn't find, namely the sandbox caps (500 connections, 100 enrichment requests a month), the $2,000 to $3,500 platform fee, and the 12-month contract. Treat those as unconfirmed until Basiq says so.

**In plain terms**

| Stage                              | What you pay                                                                                                                                                          |
| ---------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Building and testing (sandbox)** | $0. Per that text, there's a free tier that even includes 100 Enrich requests a month.                                                                                |
| **Startup program**                | About $250/month for up to 6 months, if you qualify (small, new company, under 250 users)                                                                             |
| **Production**                     | A fixed platform fee (the text says it could be $2,000+/month, but it's "quoted on contact"), plus $0.50 per user/month, plus $0.25 per user/month if you want Enrich |

For example, with 200 users on Enrich in production, usage is 200 × $0.75 = $150/month. The platform fee would be most of your bill, so ask what it is before you plan around Basiq.

**What it means for you right now**

1. You're in sandbox, which is free. You don't need to pay anything to test.
2. If that text is right and the free sandbox includes 100 Enrich requests a month, that's good news, because Basiq may be willing to enable Enrich on your sandbox apps at no cost. That's why your request to them is worth sending.
3. You don't need to decide anything about production pricing today.

**Add this to your message to Basiq**

> Does the sandbox plan include Enrich requests (I've seen 100/month mentioned)? If so, please enable it on my sandbox applications. Also, what is the platform access fee and the minimum contract term for production?

That one message gets you Enrich on sandbox and the real production numbers, so you're not relying on my summaries or on that pasted text.

**Meanwhile**

If you meant something different by "make it understand me" (for example, writing the message to Basiq in your own words), tell me and I'll do that instead.

So for 50 customers who each connect a bank, production would be 50 users × $0.50 = $25/month, plus $0.25 each ($12.50) with Enrich.