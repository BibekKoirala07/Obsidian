

Here is the actual business logic you need.

|Scenario|What should happen|
|---|---|
|1. Customer exists in POS but not Nimbus|Show in merchant Customers list as imported. Allow invitation.|
|2. Customer exists in Nimbus but not this merchant's POS|Merchant can invite by email. Create a pending relationship, without exposing the existing account.|
|3. Customer exists in both POS and Nimbus|Associate after verified invitation acceptance or another authorized identity-linking flow.|
|4. Customer doesn't exist anywhere|Create a pending invitation, not a Nimbus user.|
|5. Customer is already linked to this merchant|Do not create another relationship.|
|6. Customer is linked to Merchant A but Merchant B invites them|Allow a separate relationship with Merchant B.|
|7. Merchant invites same email twice|Reuse the existing active invitation or resend under controlled limits.|
|8. Customer registers after receiving an invitation|Verify the email, complete registration, then claim the invitation.|
|9. Customer registers before accepting invitation|Permit invitation acceptance after authentication and email verification.|
|10. Invitation expires|Mark expired and allow a new invitation.|
|11. Customer declines invitation|Mark declined/revoked and do not link.|
|12. Merchant revokes invitation|Invalidate token immediately.|
|13. POS customer has no email|Keep imported profile; don't permit an email invitation until an address is supplied.|
|14. POS customer email differs from Nimbus email|Do not link automatically; require account ownership verification.|
|15. Two POS customers have same email|Flag for identity resolution rather than blindly merging source profiles.|
|16. POS connection is removed|Stop synchronization and apply a defined retention and access policy.|

better one. 

Here are the scenarios I would implement.

| Scenario                                                               | Recommended action                                                                                      |
| ---------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| Customer imported from Shopify, not registered in Nimbus               | Show as imported; merchant can invite.                                                                  |
| Customer imported from Square and Shopify under same merchant          | Consolidate into one customer row when identity matching is reliable, keeping both POS source records.  |
| Customer imported from Lightspeed and already registered in Nimbus     | Allow invitation; don't link merely because the emails match.                                           |
| Merchant manually invites a completely new email                       | Create a merchant-customer entry and pending invitation.                                                |
| Merchant manually invites an existing Nimbus customer                  | Send invitation; link after verified acceptance.                                                        |
| Merchant A already linked John; Merchant B invites John                | Create a separate Merchant B relationship after acceptance.                                             |
| Merchant A invites John twice                                          | Do not create duplicate relationships or active invitations.                                            |
| John is already linked to Merchant A and is imported again from Square | Attach the Square source to the existing Merchant A relationship once verified, without inviting again. |
| John declines Merchant B's invitation                                  | Keep Merchant A's relationship unchanged.                                                               |
| John accepts invitations from A and B                                  | Link the same Nimbus `customerId` to both merchants.                                                    |
| Invitation email has changed                                           | Treat the new address as a separate identity until verified.                                            |
| Imported POS customer has no email                                     | Display the imported customer; require an email to send an invitation.                                  |


## **9. The exact rules I would adopt for Nimbus**

| Event                                              | Create Nimbus User?           | Create/Update Merchant Relationship?                          |
| -------------------------------------------------- | ----------------------------- | ------------------------------------------------------------- |
| Merchant connects Shopify and imports John         | No                            | Create/import merchant-owned POS customer record.             |
| Merchant connects Square and imports the same John | No                            | Link another POS identity if confidently resolved.            |
| John already has Nimbus account                    | No                            | Associate only after authorized identity resolution.          |
| John registers 60 days after POS import            | Yes, during registration      | Resolve retained POS identities independently of invitations. |
| Merchant manually invites David                    | No                            | Record pending invitation.                                    |
| David accepts invitation within 30 days            | If needed during registration | Establish merchant relationship.                              |
| David registers after invitation expires           | Yes                           | Don't activate expired manual invitation automatically.       |
| Merchant B also invites David                      | No duplicate user             | Separate Merchant B relationship after acceptance.            |


Yes. **Three new schemas are enough for the customer relationship and invitation functionality**, assuming your POS integration and transaction schemas already exist.

1. `PosCustomer` — Customers imported through Shopify, Square, or Lightspeed.
    
2. `CustomerInvitation` — Customers manually invited by a merchant.
    
3. `MerchantCustomer` — The relationship between a registered Nimbus customer and a merchant.
    

And your existing `User` schema remains unchanged.

The most important design decision I recommend is:

**When a merchant manually adds a customer, create only a** `CustomerInvitation` **record initially. When the invitation is accepted, create the** `MerchantCustomer` **relationship.**

Likewise, POS imports create `PosCustomer` records, but they don't necessarily create `MerchantCustomer` records until the imported customer is securely associated with a registered Nimbus user.

## **1. What each schema should contain**

|Schema|When created|Purpose|
|---|---|---|
|`User` (existing)|Customer registers on Nimbus|Stores registered customer accounts.|
|`PosCustomer`|Merchant imports a customer through POS|Stores customers from external commerce platforms.|
|`CustomerInvitation`|Merchant manually sends an invitation|Tracks invitation, expiration, acceptance.|
|`MerchantCustomer`|Registered user is linked to merchant|Stores the many-to-many relationship.|

This is the separation I would use.

## **2. Manual Add New Customer**

Suppose Merchant A manually invites `john@gmail.com`.

Initially:

**CustomerInvitation**

Created

```
{
  "merchantId": "MERCHANT_A",
  "email": "john@gmail.com",
  "status": "PENDING",
  "expiresAt": "2026-12-25T00:00:00Z"
}
```

No new `User`

No `MerchantCustomer` relationship yet

When John accepts after authenticating and verifying ownership of the invited email, you create:

```
{
  "merchantId": "MERCHANT_A",
  "customerId": "JOHN_USER_ID",
  "status": "CONNECTED"
}
```

And update the invitation to `ACCEPTED`.

If John doesn't already have a Nimbus account, he registers first. If he already has one, you reuse his existing `User._id`.

**Never create a** `User` **directly merely because the merchant manually entered an email.**

## **3. When a merchant imports POS customers**

Suppose Merchant A connects Shopify and imports:

- John — `john@gmail.com`
    
- Sarah — `sarah@gmail.com`
    
- David — `david@gmail.com`
    

You create three `PosCustomer` records.

You don't automatically create three Nimbus `User` records.

Now there are several possibilities.

|Existing state|What POS import should do|
|---|---|
|No invitation, no merchant relationship|Create `PosCustomer` only.|
|Pending manual invitation for same email|Create `PosCustomer`; leave invitation pending.|
|Accepted manual invitation and existing relationship|Create `PosCustomer`; keep existing relationship.|
|Registered Nimbus customer but no relationship|Create `PosCustomer`; identify possible match for verified linking.|
|Existing POS customer from same integration|Update/upsert existing `PosCustomer`, don't duplicate.|
|Existing POS customer from another integration|Create separate source record and associate with the same person only when reliable.|
|Merchant already connected to customer|Reuse existing `MerchantCustomer`; do not create another.|
|Another merchant has the same customer|Keep a separate merchant-specific POS record and relationship.|

**The presence of a manual invitation should never prevent POS customer import.** They are separate processes.

The POS customer can also be manually invited, but that's not a requirement for import or eventual POS identity linking.

## **4. What happens when both a manual invitation and POS customer exist?**

Let's say Merchant A manually invites John first.

**Day 1 — Manual invitation**

`CustomerInvitation`: John, Merchant A, PENDING

**Day 5 — Shopify integration**

`PosCustomer`: John, Merchant A, Shopify

Invitation stays PENDING.

**Day 7 — John accepts invitation**

`MerchantCustomer`: John + Merchant A, CONNECTED

`CustomerInvitation`: ACCEPTED

**Final result**

One Nimbus user, one merchant relationship, one invitation history, and one Shopify customer record.

There is no need to delete the manual invitation or POS record when the relationship is created.

Each retains its own purpose.

However, accepting the manual invitation does not by itself prove that every POS profile using John's email belongs to him. Any POS identity association still needs a reliable matching or verification process.

## **5. The most important part:** `getAllCustomers()`

You said:

> The merchant should only see customers imported from POS or manually invited.

Correct.

That means your merchant's customer list is the **union of** `PosCustomer` **and** `CustomerInvitation`, with existing `MerchantCustomer` and `User` information joined where relevant.

**PosCustomer**

Merchant-owned POS imports

**CustomerInvitation**

Merchant-created invitations

**Merchant Customers List**

Filter by authenticated merchant ID, consolidate verified matches, enrich linked accounts

**MerchantCustomer + User**

Optional relationship and registered account details

For example, Merchant A has:

**POS records**

|Name|Email|Source|
|---|---|---|
|John|john@gmail.com|Shopify|
|John|john@gmail.com|Square|
|Sarah|sarah@gmail.com|Lightspeed|
|David|david@gmail.com|Shopify|

**Manual invitations**

|Email|Status|
|---|---|
|john@gmail.com|ACCEPTED|
|michael@gmail.com|PENDING|
|emma@gmail.com|EXPIRED|

The merchant list should look like this, assuming John's two POS identities have been confidently matched:

|Customer|Source|Connection status|
|---|---|---|
|John|Shopify, Square, Manual|Connected|
|Sarah|Lightspeed|POS imported|
|David|Shopify|POS imported|
|Michael|Manual|Invitation pending|
|Emma|Manual|Invitation expired|

John appears once because the records have been associated with the same customer identity, not simply because their email strings are equal.

### **Your query should no longer use** `UserDocument[]`

I would introduce a dedicated response type.

```
type MerchantCustomerListItem = {
  id: string;

  userId: string | null;

  name: string | null;
  email: string | null;

  sources: Array<
    "SHOPIFY" | "SQUARE" | "LIGHTSPEED" | "MANUAL"
  >;

  invitationStatus:
    | "NOT_INVITED"
    | "PENDING"
    | "ACCEPTED"
    | "EXPIRED"
    | "REVOKED";

  isConnected: boolean;
};
```

Then your service becomes conceptually:

```
async getAllCustomers(
  user: CurrentUserType,
  input: GetAllCustomersInput,
): Promise<{
  message: string;
  data: MerchantCustomerListItem[];
  pagination: PaginationResponse;
}> {
  const merchantId = user._id;

  const result =
    await this.customerDirectoryService.getMerchantCustomers({
      merchantId,
      searchText: input.searchText,
      status: input.status,
      skip: input.skip ?? 0,
      limit: input.limit ?? 10,
      orderBy: input.orderBy ?? "createdAt",
      order: input.order ?? "DESC",
    });

  return {
    message: this.i18nService.t("users.customer_list"),
    data: result.data,
    pagination: result.pagination,
  };
}
```

`customerDirectoryService` would implement a merchant-scoped combined query, normally through MongoDB aggregation or a maintained read model.

Deduplication, search, sorting, and pagination must happen over the consolidated customer records. If you separately paginate POS customers and invitations and then merge them, you'll get inconsistent results and incorrect total counts.

## **6. What if you need to add something to your existing User schema?**

You don't need to add any merchant-specific relationship field to `User`.

In particular, don't do this:

```
@Prop({
  type: mongoose.Schema.Types.ObjectId,
  ref: "User",
})
merchantId: mongoose.Types.ObjectId;
```

That only handles one merchant per customer.

You _could_ add an array of merchant IDs, but I wouldn't recommend it either. It duplicates relationship information and can become difficult to synchronize.

Your existing model:

```
{
  _id: customerId,
  role: UserRole.CUSTOMER,
  email: "john@gmail.com"
}
```

is enough for the account itself.

The `MerchantCustomer` collection handles associations:

```
[
  {
    merchantId: merchantAId,
    customerId: johnUserId,
    status: "CONNECTED",
  },
  {
    merchantId: merchantBId,
    customerId: johnUserId,
    status: "CONNECTED",
  },
]
```

Use a unique partial index on `(merchantId, customerId)` where `customerId` is an ObjectId to prevent duplicate registered-customer relationships.

For POS records, use a unique key such as `(posConnectionId, externalCustomerId)`. For invitations, enforce one active pending invitation per merchant and normalized email, while preserving historical attempts.

## **7. My final recommendation**

Keep these rules:

|Event|PosCustomer|CustomerInvitation|MerchantCustomer|User|
|---|---|---|---|---|
|Merchant manually invites new email|—|Create|—|—|
|Merchant manually invites registered user|—|Create|After acceptance|Reuse|
|Merchant imports POS customer|Create/upsert|Unchanged|Link only if identity authorized|Reuse if applicable|
|POS customer also has pending invitation|Create/upsert|Remains pending|Depends on verified linkage|Unchanged|
|Invited customer accepts|Unchanged|ACCEPTED|Create/upsert|Create on registration or reuse|
|Customer registers 60 days after expired invitation|Unchanged|EXPIRED|No automatic link from expired invitation|Create|
|Previously imported POS customer registers after 60 days|Unchanged|Unchanged|Create after verified POS identity linking|Create|
|Merchant B imports customer already associated with Merchant A|Create under B|Unchanged|Separate B relationship after authorization|Reuse|

One final implementation detail: a `MerchantCustomer` record should be created when an **actual verified relationship** is established, whether through manual invitation acceptance or through an independently verified POS identity link. A POS-only record or pending manual invitation can remain without a relationship.

That gives you the three schemas you wanted, with clear ownership and no need to modify the existing `User` collection.

**The one thing I would decide before coding:** Should a POS customer become connected to a Nimbus account automatically when the POS email matches the account's verified email, or must the customer explicitly confirm that POS relationship? I recommend requiring confirmation or equivalent trustworthy identity proof, because that choice controls when `MerchantCustomer` is created for POS imports.


Yes. For Nimbus, **the merchant's invitation email should contain a deep link**. When the customer clicks it, Nimbus should determine whether the app is installed and whether the customer already has an account.

But one important thing: **If the app is already installed, the customer should not automatically go to Forgot Password.**

The destination depends on their authentication and registration status.

## **1. Recommended invitation flow for Nimbus**

**Merchant manually invites customer**

john@gmail.com

**Invitation email with secure link**

**App installed**

Open Nimbus App

**App not installed**

Landing page → App Store / Play Store

**Resolve invitation**

Validate token, account and invitation status

New Nimbus user

Sign Up → Verify Email → Accept Invitation

Existing Nimbus user

Login → Verify Invited Email → Accept Invitation

**MerchantCustomer relationship created**

Invitation marked ACCEPTED

## **2. Where should the customer be redirected?**

|Situation|Destination|
|---|---|
|App installed, customer not registered|**Sign Up** screen|
|App installed, customer already registered but logged out|**Login** screen|
|App installed, customer already logged in with invited email|**Invitation Acceptance** screen|
|App installed, logged in with another email|Account mismatch screen with option to switch account|
|App not installed|App Store / Play Store through landing page|
|Customer forgot their password|Forgot Password, only when they select it|
|Invitation expired|Invitation Expired screen with instructions to request a new invitation|
|Invitation already accepted|Connected or Already Accepted screen|

I recommend implementing a dedicated **Accept Invitation** screen.

It should show something like:

## **You're invited to Nimbus!**

ABC Clothing has invited you to connect with them on Nimbus Receipts.

Invited email

**john@gmail.com**

Accept InvitationExample screen layout. Acceptance becomes available after the customer is authenticated and the invitation is validated.

## **3. How do you make sure the same email is used?**

This is the most important part.

Suppose Merchant A invites:

`john@gmail.com`

But John opens the invitation and tries to register using:

`peter@gmail.com`

**Nimbus must not allow Peter's account to accept John's invitation.**

Here's how to implement it.

### **Step 1 — Store the invited email in the database**

Your `CustomerInvitation` schema should store:

```
{
  merchantId: merchantAId,
  emailNormalized: "john@gmail.com",
  tokenHash: "...",
  status: "PENDING",
  expiresAt: Date,
  acceptedByUserId: null
}
```

Normalize emails consistently (trim whitespace and apply your chosen case-normalization policy). Do not perform provider-specific transformations such as removing dots from every email address.

### **Step 2 — Generate a deep link**

For example, using an illustrative Nimbus domain:

```
https://links.nimbusreceipt.com.au/invite?token=SECURE_RANDOM_TOKEN
```

Use the token to identify the invitation securely. You don't need to include the email address in the URL.

### **Step 3 — Pre-fill the email on Sign Up**

When the new user opens the invitation, Nimbus retrieves the invited email from the backend.

## **Create your account**

You've been invited to join Nimbus Receipts.

Email address

john@gmail.com

This email is linked to your invitation and cannot be changed during invitation registration.

Password

Enter your password

Continue

Already have an account? Sign in

I recommend making the email field **read-only during invitation registration**.

If they want to use a different email, they can leave the invitation process and register normally. That different account must not be connected through the original invitation.

### **Step 4 — Verify the email**

Even though the invitation arrived in John's mailbox, Nimbus should ensure the account's email is verified before linking.

If your existing sign-up flow already verifies the email using an OTP, reuse it.

For users registering through Google, Apple, or Facebook, verify that the provider supplies a trustworthy verified email matching the invitation. If the provider hides the address, such as Apple Hide My Email, don't assume the relay email matches the invited address. Provide a separate verification flow for the invited email.

### **Step 5 — Check again in the backend**

Do not trust the read-only field in Flutter. The backend must enforce the email check.

```
const invitation =
  await invitationModel.findOne({
    tokenHash: hashToken(input.token),
    status: "PENDING",
    expiresAt: { $gt: new Date() },
  });

if (!invitation) {
  throw new BadRequestException(
    "Invitation is invalid or expired",
  );
}

const user = await userModel.findById(
  currentUser._id,
);

if (
  !user ||
  user.role !== UserRole.CUSTOMER ||
  !user.email
) {
  throw new ForbiddenException(
    "A customer account is required",
  );
}

if (
  normalizeEmail(user.email) !==
  invitation.emailNormalized
) {
  throw new ForbiddenException(
    "Please sign in with the invited email address",
  );
}

// Also confirm the account's current email is verified.
// Then atomically accept the invitation and upsert
// the merchant-customer relationship.
```

Your existing `User` schema contains both `email` and `contacts.emailDetail`. Make sure your backend has one authoritative verified-email check and does not assume `email` alone is verified.

For acceptance, use a MongoDB transaction or equivalent atomic strategy so two simultaneous requests cannot produce inconsistent invitation and relationship records.

## **4. What if the user has already registered?**

Suppose John has a Nimbus account and has forgotten that he registered.

He clicks the invitation.

You should not force him into Sign Up.

Instead, the app should let him sign in, then validate the invited email.

If the user is already logged in as John, show Accept Invitation directly after backend checks.

If the user is logged in as Sarah, display:

> This invitation was sent to a different email address. Please switch to the invited account to continue.

Provide a **Switch Account** action.

If John forgot his password, then he can use the normal Forgot Password flow, return to Login, and continue accepting the saved invitation.

The invitation should survive these authentication screen transitions without being marked accepted prematurely.

## **5. What if the app is not installed?**

One thing to clarify: normal deep links do not automatically send someone to the App Store or Play Store.

With iOS Universal Links or verified Android App Links, the link opens the app when supported and installed. Otherwise, it opens your website. Your website can then offer installation links.

Apple Developer Documentation+1

For Nimbus, I'd implement:

1. Customer clicks invitation URL.
    
2. If the app is installed and the link association works, open the invitation flow in the app.
    
3. Otherwise, show a Nimbus invitation landing page.
    
4. Landing page offers App Store and Google Play installation buttons.
    
5. After installation, customer returns to the invitation email and taps the link again, or uses a supported deferred deep-link mechanism to restore the invitation.
    

**Important:** Simply installing an app does not guarantee the original invitation token will be passed into the first app launch. This requires a separate deferred-deep-link solution and careful platform testing, particularly on iOS.

For your first implementation, having the customer reopen the email link after installing is simpler and reliable.

## **6. What if the user registers after 30 days?**

Your manual invitation expires after 30 days.

I would enforce these rules:

|Scenario|Result|
|---|---|
|Registers on day 10 and accepts|Connected to merchant.|
|Registers on day 29 and accepts|Connected to merchant.|
|Registers on day 29 but accepts on day 31|Invitation expired; connection not created.|
|Registers on day 60|Can register normally, but expired invitation cannot be accepted.|
|Merchant resends after expiry|Issue a new valid invitation.|
|User has already accepted|Cannot reuse the invitation token to connect a different account.|

The expiry should be validated when the invitation is accepted, not merely when the link was opened.

Also, don't rely solely on MongoDB TTL deletion. TTL cleanup may occur after the expiration time and might remove records needed for auditing. Always check `expiresAt` explicitly, and preferably retain invitation history with an `EXPIRED` status.

## **7. Recommended backend operations**

For your NestJS GraphQL backend, I'd keep it simple:

|Operation|Purpose|
|---|---|
|`inviteCustomer`|Merchant generates a manual invitation.|
|`getInvitationDetails`|Safely resolves invitation details for the recipient without consuming the token.|
|`acceptCustomerInvitation`|Validates authenticated user, verified email, and invitation before linking.|
|`resendCustomerInvitation`|Merchant issues or resends an invitation within your policy.|

Your `acceptCustomerInvitation` mutation could look like:

```
mutation AcceptCustomerInvitation(
  $input: AcceptCustomerInvitationInput!
) {
  acceptCustomerInvitation(input: $input) {
    message
  }
}
```

With input:

```
{
  "input": {
    "token": "SECURE_RANDOM_TOKEN"
  }
}
```

Notice there's no `email` or `customerId` in the input. The backend already knows the authenticated customer and reads the invited email from the invitation record.

## **8. My recommended Nimbus flow**

The overall flow should be:

**Manual Add New Customer → Invitation Email → Deep Link → Sign Up or Login → Verify Email → Accept Invitation → Create MerchantCustomer Relationship.**

Your backend should never link users based solely on whatever email they enter in an acceptance request.

You also don't need to change your existing `User` schema or create a Nimbus user when the merchant sends an invitation.

And this entire manual invitation flow can remain completely separate from Shopify, Square, and Lightspeed integrations.

The two rules I'd enforce from the beginning are:

- **Registration:** The email used for an invitation must be the same verified email stored in the invitation.
    
- **Acceptance:** The invitation must be valid, unexpired, and accepted by the authenticated customer who controls that email.
    

That gives you a secure process for both new and existing Nimbus customers.

# **Nimbus Receipts — Three MongoDB Schemas**

Based on your existing `User` schema and NestJS + Mongoose architecture, I recommend these three schemas:

1. `PosCustomer` — Stores customers imported from Shopify, Square, and Lightspeed.
    
2. `CustomerInvitation` — Handles the merchant's manual **Add New Customer** functionality, including the 30-day invitation validity.
    
3. `MerchantCustomer` — Connects a registered Nimbus customer to one or more Nimbus merchants.
    

Your existing `User` schema will remain unchanged.

## **1. PosCustomer Schema**

File: `pos-customer.schema.ts`

This schema is populated whenever a merchant synchronizes customers from an integrated POS platform.

A merchant can have multiple POS connections, and each imported customer is uniquely identified within the originating connection.

```

import { Prop, Schema, SchemaFactory } from "@nestjs/mongoose";
import mongoose, { Document } from "mongoose";

export enum PosProvider {
  SHOPIFY = "SHOPIFY",
  SQUARE = "SQUARE",
  LIGHTSPEED = "LIGHTSPEED",
}

@Schema({ timestamps: true })
export class PosCustomer {
  // Nimbus merchant who owns this imported record
  @Prop({
    type: mongoose.Schema.Types.ObjectId,
    ref: "User",
    required: true,
    index: true,
  })
  merchantId: mongoose.Types.ObjectId;

  // Reference to existing POS connection
  @Prop({
    type: mongoose.Schema.Types.ObjectId,
    required: true,
  })
  posConnectionId: mongoose.Types.ObjectId;

  @Prop({
    type: String,
    enum: Object.values(PosProvider),
    required: true,
  })
  provider: PosProvider;

  // Customer ID from external POS
  @Prop({ type: String, required: true })
  externalCustomerId: string;

  @Prop({ type: String, default: null })
  firstName?: string | null;

  @Prop({ type: String, default: null })
  lastName?: string | null;

  @Prop({ type: String, default: null })
  email?: string | null;

  @Prop({ type: String, default: null })
  emailNormalized?: string | null;

  @Prop({ type: String, default: null })
  phone?: string | null;

  // Set only after trusted identity resolution
  @Prop({
    type: mongoose.Schema.Types.ObjectId,
    ref: "MerchantCustomer",
    default: null,
  })
  merchantCustomerId?: mongoose.Types.ObjectId | null;

  @Prop({ type: Date, default: null })
  lastSyncedAt?: Date | null;

  @Prop({ type: Date, default: null })
  linkedAt?: Date | null;

  createdAt: Date;
  updatedAt: Date;
}

export type PosCustomerDocument = PosCustomer & Document;

export const PosCustomerSchema =
  SchemaFactory.createForClass(PosCustomer);

// Prevent duplicate customers within same POS connection
PosCustomerSchema.index(
  { posConnectionId: 1, externalCustomerId: 1 },
  { unique: true },
);

// Merchant customer listing
PosCustomerSchema.index({
  merchantId: 1,
  emailNormalized: 1,
});

// Find source profiles linked to a relationship
PosCustomerSchema.index({
  merchantCustomerId: 1,
});
```

**Example:** Merchant A imports John from Shopify and Square.

```
[
  {
    "merchantId": "MERCHANT_A",
    "posConnectionId": "SHOPIFY_CONNECTION_1",
    "provider": "SHOPIFY",
    "externalCustomerId": "SH_100",
    "email": "john@gmail.com",
    "merchantCustomerId": null
  },
  {
    "merchantId": "MERCHANT_A",
    "posConnectionId": "SQUARE_CONNECTION_1",
    "provider": "SQUARE",
    "externalCustomerId": "SQ_500",
    "email": "john@gmail.com",
    "merchantCustomerId": null
  }
]
```

Both imported records exist independently. They can eventually point to the same `MerchantCustomer` relationship after reliable identity verification.

## **2. CustomerInvitation Schema**

File: `customer-invitation.schema.ts`

This schema is **only for manual invitations**, completely independent of POS integration.

When the merchant manually adds a customer using an email address, create an invitation document.

Do not create a `User` or `MerchantCustomer` immediately.

```

import { Prop, Schema, SchemaFactory } from "@nestjs/mongoose";
import mongoose, { Document } from "mongoose";

export enum CustomerInvitationStatus {
  PENDING = "PENDING",
  ACCEPTED = "ACCEPTED",
  EXPIRED = "EXPIRED",
  REVOKED = "REVOKED",
}

@Schema({ timestamps: true })
export class CustomerInvitation {
  // Merchant who sent the invitation
  @Prop({
    type: mongoose.Schema.Types.ObjectId,
    ref: "User",
    required: true,
    index: true,
  })
  merchantId: mongoose.Types.ObjectId;

  // Email manually entered by merchant
  @Prop({ type: String, required: true })
  email: string;

  @Prop({ type: String, required: true })
  emailNormalized: string;

  // Hash of secure random invitation token
  @Prop({
    type: String,
    required: true,
    unique: true,
    select: false,
  })
  tokenHash: string;

  @Prop({
    type: String,
    enum: Object.values(CustomerInvitationStatus),
    default: CustomerInvitationStatus.PENDING,
  })
  status: CustomerInvitationStatus;

  // 30 days after invitation creation
  @Prop({ type: Date, required: true })
  expiresAt: Date;

  // Set after customer accepts invitation
  @Prop({
    type: mongoose.Schema.Types.ObjectId,
    ref: "User",
    default: null,
  })
  acceptedByUserId?: mongoose.Types.ObjectId | null;

  @Prop({ type: Date, default: null })
  acceptedAt?: Date | null;

  @Prop({ type: Date, default: null })
  revokedAt?: Date | null;

  @Prop({ type: Date, default: null })
  lastSentAt?: Date | null;

  @Prop({ type: Number, default: 1 })
  sendCount: number;

  createdAt: Date;
  updatedAt: Date;
}

export type CustomerInvitationDocument =
  CustomerInvitation & Document;

export const CustomerInvitationSchema =
  SchemaFactory.createForClass(CustomerInvitation);

// Only one pending invitation per merchant/email.
// Expired PENDING records must be marked EXPIRED
// before creating a replacement.
CustomerInvitationSchema.index(
  {
    merchantId: 1,
    emailNormalized: 1,
  },
  {
    unique: true,
    partialFilterExpression: {
      status: CustomerInvitationStatus.PENDING,
    },
  },
);

// Find invitation history
CustomerInvitationSchema.index({
  merchantId: 1,
  createdAt: -1,
});

// Find expired invitations for scheduled cleanup
CustomerInvitationSchema.index({
  status: 1,
  expiresAt: 1,
});
```

### **Invitation expiry: 30 days**

When creating an invitation:

```
const INVITATION_VALIDITY_DAYS = 30;

const expiresAt = new Date(
  Date.now() +
    INVITATION_VALIDITY_DAYS * 24 * 60 * 60 * 1000,
);
```

**Do not use MongoDB TTL to delete the invitation after 30 days.** Keep invitation history and change its status to `EXPIRED`. A background job can mark expired records, but validation must always reject an invitation once `expiresAt` has passed.

A resend may have its own policy. For example, issue a new token and 30-day validity window after invalidating or expiring the previous invitation. Rate-limit sends.

An example document:

```
{
  "merchantId": "MERCHANT_A",
  "email": "john@gmail.com",
  "emailNormalized": "john@gmail.com",
  "tokenHash": "HASHED_TOKEN",
  "status": "PENDING",
  "expiresAt": "2026-12-25T08:00:00.000Z",
  "acceptedByUserId": null,
  "acceptedAt": null,
  "sendCount": 1
}
```

When John registers or logs in, verifies the invited email, and accepts the invitation:

- Mark the invitation `ACCEPTED`.
    
- Set `acceptedByUserId`.
    
- Set `acceptedAt`.
    
- Create or reactivate the corresponding `MerchantCustomer` record.
    

These changes should be performed atomically.

## **3. MerchantCustomer Schema**

File: `merchant-customer.schema.ts`

This is your many-to-many relationship collection.

One Nimbus customer can have relationships with multiple merchants. A merchant can have relationships with multiple registered customers.

```

import { Prop, Schema, SchemaFactory } from "@nestjs/mongoose";
import mongoose, { Document } from "mongoose";

export enum MerchantCustomerStatus {
  ACTIVE = "ACTIVE",
  REVOKED = "REVOKED",
}

export enum MerchantCustomerLinkSource {
  MANUAL_INVITATION = "MANUAL_INVITATION",
  POS_VERIFIED = "POS_VERIFIED",
}

@Schema({ timestamps: true })
export class MerchantCustomer {
  // Existing Nimbus merchant User ID
  @Prop({
    type: mongoose.Schema.Types.ObjectId,
    ref: "User",
    required: true,
  })
  merchantId: mongoose.Types.ObjectId;

  // Existing registered Nimbus customer User ID
  @Prop({
    type: mongoose.Schema.Types.ObjectId,
    ref: "User",
    required: true,
  })
  customerId: mongoose.Types.ObjectId;

  @Prop({
    type: String,
    enum: Object.values(MerchantCustomerStatus),
    default: MerchantCustomerStatus.ACTIVE,
  })
  status: MerchantCustomerStatus;

  @Prop({
    type: String,
    enum: Object.values(MerchantCustomerLinkSource),
    required: true,
  })
  linkSource: MerchantCustomerLinkSource;

  // Optional: invitation that established relationship
  @Prop({
    type: mongoose.Schema.Types.ObjectId,
    ref: "CustomerInvitation",
    default: null,
  })
  invitationId?: mongoose.Types.ObjectId | null;

  @Prop({ type: Date, default: Date.now })
  linkedAt: Date;

  @Prop({ type: Date, default: null })
  revokedAt?: Date | null;

  createdAt: Date;
  updatedAt: Date;
}

export type MerchantCustomerDocument =
  MerchantCustomer & Document;

export const MerchantCustomerSchema =
  SchemaFactory.createForClass(MerchantCustomer);

// Exactly one relationship per merchant/customer pair
MerchantCustomerSchema.index(
  {
    merchantId: 1,
    customerId: 1,
  },
  { unique: true },
);

// Find all merchants linked to one customer
MerchantCustomerSchema.index({
  customerId: 1,
  status: 1,
});
```

An example:

```
[
  {
    "merchantId": "MERCHANT_A",
    "customerId": "JOHN_USER_ID",
    "status": "ACTIVE",
    "linkSource": "MANUAL_INVITATION",
    "invitationId": "INVITATION_001"
  },
  {
    "merchantId": "MERCHANT_B",
    "customerId": "JOHN_USER_ID",
    "status": "ACTIVE",
    "linkSource": "POS_VERIFIED",
    "invitationId": null
  }
]
```

A few important design choices:

- `customerId` is required because the relationship represents a **registered Nimbus customer**, not an unregistered POS customer.
    
- A customer is linked to a merchant only after an authorized linking flow.
    
- The unique index prevents duplicate relationships.
    
- If a revoked relationship is later re-established, update the existing record to `ACTIVE` rather than inserting a duplicate.
    
- `linkSource` records how the relationship was first established. If you need to track multiple independent linking methods over time, retain that history separately rather than overwriting it.
    

## **4. How the three schemas work together**

**User (MERCHANT)**

Existing collection

**POS Integration**

Imports external customers

PosCustomer

**Add New Customer**

Manual email invitation

CustomerInvitation

Verified link or accepted invitation

**MerchantCustomer**

One relationship per merchant–customer pair

**User (CUSTOMER)**

Existing registered Nimbus account

## **5. What happens in every possible scenario?**

|Scenario|PosCustomer|CustomerInvitation|MerchantCustomer|
|---|---|---|---|
|Merchant manually invites an unregistered email|No change|Create PENDING|No change|
|Merchant manually invites a registered customer|No change|Create PENDING|No change until accepted|
|Customer accepts manual invitation|No change|ACCEPTED|Create/activate|
|Merchant integrates Shopify|Upsert Shopify customers|No change|No change unless separately verified|
|Merchant integrates Square|Upsert Square customers|No change|No change unless separately verified|
|Same POS customer imported again|Update existing record|No change|No duplicate|
|Imported customer has an existing pending invitation|Upsert imported record|Remains PENDING|No change until authorized linking|
|POS customer is verified against existing Nimbus user|Update source link|No change|Create/activate|
|Customer registers after 60 days following POS import|Eligible source may be linked after verification|No change|Create/activate after verification|
|Customer registers after 60 days following manual invitation|No change|EXPIRED|No automatic relationship|
|Merchant B imports a customer linked to Merchant A|Create Merchant B's POS record|No change|Separate Merchant B relationship after verification|

## **6. Your** `getAllCustomers()` **response**

For the merchant's customer list, I would not return `UserDocument[]` anymore.

The data originates from two independent collections:

- `PosCustomer` — imported records.
    
- `CustomerInvitation` — manually added records.
    

`MerchantCustomer` and `User` are used to enrich the results when a registered customer relationship exists.

One customer can appear in both source collections. Consolidate only reliably matched identities into one displayed row.

A possible DTO:

```
export interface MerchantCustomerListItem {
  id: string;

  customerId: string | null;

  firstName: string | null;
  lastName: string | null;
  email: string | null;

  sources: Array<
    "SHOPIFY" |
    "SQUARE" |
    "LIGHTSPEED" |
    "MANUAL"
  >;

  invitationStatus:
    | "NOT_INVITED"
    | "PENDING"
    | "ACCEPTED"
    | "EXPIRED"
    | "REVOKED";

  relationshipStatus:
    | "ACTIVE"
    | "REVOKED"
    | "NOT_LINKED";
}
```

You would filter both source collections by the **authenticated merchant's ID**, deduplicate confirmed identities, then paginate the consolidated result.

Note that for unlinked POS records, `customerId` should remain `null`, even when the imported email happens to match an existing Nimbus user.

## **7. Final schema summary**

|Field|PosCustomer|CustomerInvitation|MerchantCustomer|
|---|---|---|---|
|merchantId|Required|Required|Required|
|customerId (Nimbus User ID)|Via relationship after linking|acceptedByUserId after acceptance|Required|
|email|Optional|Required|Not needed|
|posConnectionId|Required|—|—|
|provider|Required|—|—|
|externalCustomerId|Required|—|—|
|tokenHash|—|Required|—|
|expiresAt|—|Required|—|
|status|—|Invitation status|Relationship status|
|invitationId|—|—|Optional|
|linkedAt|Optional|—|Required|
|timestamps|Yes|Yes|Yes|

**My recommendation:** Implement these three schemas without changing your existing `User` model. The manual invitation will remain independent of POS integrations, while `MerchantCustomer` acts as the common registered-customer relationship.

The only thing I would adjust if your POS data model already has customer records is to **reuse those records rather than creating duplicate** `PosCustomer` **documents**. The invitation and relationship schemas can stay the same.


Your understanding is **mostly correct**, but there are two important corrections:

1. **Manual invitation:** Even if the customer already exists in Nimbus, the invitation should initially be `PENDING`, not `ACCEPTED`. It becomes `ACCEPTED` only when the customer accepts it.
    
2. **POS import:** Importing a customer does not always mean a `MerchantCustomer` record should be created. That depends on whether the imported customer has been reliably linked to a registered Nimbus account.
    

Let me explain using your four scenarios.

## **1. What happens in each scenario?**

|Scenario|`PosCustomer`|`CustomerInvitation`|`MerchantCustomer`|`User`|
|---|---|---|---|---|
|**1. Existing Nimbus customer is manually invited**|No change|Create `PENDING`; change to `ACCEPTED` after acceptance|Create after acceptance|Reuse existing|
|**2. Existing Nimbus customer imported from POS**|Create/update|No change|Create after verified identity linking|Reuse existing|
|**3. Unregistered customer is manually invited**|No change|Create `PENDING`|Create after registration and invitation acceptance|Create upon registration|
|**4. Unregistered customer imported from POS**|Create/update|No change|**Not yet**; create after registration and identity linking|Create upon registration|

The important distinction is that `MerchantCustomer` represents a relationship with an actual registered Nimbus customer.

Since its `customerId` references `User._id` and is required, you cannot create that relationship when the customer doesn't have a Nimbus account.

## **2. Scenario 1: Customer already exists and merchant manually invites them**

Suppose John already uses Nimbus.

Merchant A invites `john@gmail.com`.

**Immediately after the invitation:**

```
// CustomerInvitation
{
  "merchantId": "MERCHANT_A",
  "email": "john@gmail.com",
  "status": "PENDING"
}
```

No `MerchantCustomer` record yet.

After John logs into Nimbus and explicitly accepts:

```
// CustomerInvitation
{
  "merchantId": "MERCHANT_A",
  "email": "john@gmail.com",
  "status": "ACCEPTED",
  "acceptedByUserId": "JOHN_USER_ID"
}
```

```
// MerchantCustomer
{
  "merchantId": "MERCHANT_A",
  "customerId": "JOHN_USER_ID",
  "status": "ACTIVE",
  "linkSource": "MANUAL_INVITATION"
}
```

`PosCustomer` remains unchanged.

## **3. Scenario 2: Existing Nimbus customer imported from POS**

Suppose John already uses Nimbus, and Merchant B connects Shopify.

Shopify imports John:

```
// PosCustomer
{
  "merchantId": "MERCHANT_B",
  "provider": "SHOPIFY",
  "externalCustomerId": "SH_123",
  "email": "john@gmail.com",
  "merchantCustomerId": null
}
```

If Nimbus can securely establish that this POS customer belongs to John's existing account, create:

```
// MerchantCustomer
{
  "merchantId": "MERCHANT_B",
  "customerId": "JOHN_USER_ID",
  "status": "ACTIVE",
  "linkSource": "POS_VERIFIED"
}
```

Then update the POS customer with the corresponding relationship ID.

`CustomerInvitation` remains untouched.

**You don't need manual invitation acceptance for POS imports**, but you still need a trustworthy way to establish that the imported POS profile belongs to that Nimbus customer.

## **4. Scenario 3: Customer does not exist and is manually invited**

Suppose Merchant A invites David, who hasn't registered.

Initially:

```
// CustomerInvitation
{
  "merchantId": "MERCHANT_A",
  "email": "david@gmail.com",
  "status": "PENDING",
  "expiresAt": "2026-12-25T00:00:00.000Z"
}
```

No `User` or `MerchantCustomer` record is created at this point.

David opens the invitation link, installs Nimbus if needed, registers using the invited email, verifies it, and accepts the invitation.

Then you create the `MerchantCustomer` relationship and change the invitation to `ACCEPTED`.

If David registers after the invitation has expired, he can still create a Nimbus account, but that expired invitation cannot automatically establish the merchant relationship.

## **5. Scenario 4: Customer does not exist and is imported from POS**

Suppose Merchant B connects Square, and Square imports Emma.

Emma doesn't have a Nimbus account.

Initially:

```
// PosCustomer
{
  "merchantId": "MERCHANT_B",
  "provider": "SQUARE",
  "externalCustomerId": "SQ_456",
  "email": "emma@gmail.com",
  "merchantCustomerId": null
}
```

No `User`, `CustomerInvitation`, or `MerchantCustomer` record is created.

When Emma registers later, Nimbus can look for matching POS records. After establishing that those records belong to Emma, create the relationship and update the corresponding POS customer records.

This should work regardless of whether she registered 10 days or 60 days after the POS import, provided the imported data is still retained.

## **6. Can** `CustomerInvitation` **handle customer acceptance?**

**Yes. That is exactly what I recommend.**

You do not need a fourth invitation-acceptance schema.

Your existing proposed `CustomerInvitation` model already contains everything needed to track acceptance:

```
export enum CustomerInvitationStatus {
  PENDING = "PENDING",
  ACCEPTED = "ACCEPTED",
  EXPIRED = "EXPIRED",
  REVOKED = "REVOKED",
}
```

And these fields:

```
@Prop({
  type: String,
  enum: Object.values(CustomerInvitationStatus),
  default: CustomerInvitationStatus.PENDING,
})
status: CustomerInvitationStatus;

@Prop({
  type: mongoose.Schema.Types.ObjectId,
  ref: "User",
  default: null,
})
acceptedByUserId?: mongoose.Types.ObjectId | null;

@Prop({ type: Date, default: null })
acceptedAt?: Date | null;
```

The lifecycle would be:

**Merchant sends invitation**

**CustomerInvitation: PENDING**

**Customer signs in/registers and verifies email**

**Customer explicitly clicks Accept**

**CustomerInvitation: ACCEPTED**

acceptedByUserId and acceptedAt populated

**MerchantCustomer: ACTIVE**

Create or reactivate relationship

Both final database changes should happen in the same MongoDB transaction.

You could implement it through a GraphQL mutation:

```
mutation AcceptCustomerInvitation(
  $input: AcceptCustomerInvitationInput!
) {
  acceptCustomerInvitation(input: $input) {
    message
  }
}
```

The backend should:

1. Validate the invitation token and 30-day expiry.
    
2. Confirm the authenticated user has the `CUSTOMER` role.
    
3. Confirm the user's verified email matches the invitation's email.
    
4. Ensure the invitation hasn't already been accepted or revoked.
    
5. Create or reactivate the `MerchantCustomer` relationship.
    
6. Update `CustomerInvitation` to `ACCEPTED`.
    
7. Complete both updates atomically.
    

## **7. One more scenario: customer already linked through POS, then manually invited**

Suppose John is already linked to Merchant A through a verified POS customer identity.

Later, Merchant A manually enters John's email in Add New Customer.

**Don't create another** `MerchantCustomer` **record.**

The backend should detect the existing active relationship and return a suitable message, such as:

> This customer is already connected to your merchant account.

There is no reason to send another invitation for a relationship that already exists.

Similarly, if John has a pending invitation and a POS import occurs, neither process should automatically mark that invitation accepted. The POS import is independent.

## **Final decision**

Your architecture should follow this principle:

`PosCustomer` **records imported customers.** `CustomerInvitation` **records manual invitations and their acceptance.** `MerchantCustomer` **records verified relationships between registered Nimbus customers and merchants.**

The main correction to your understanding is that `MerchantCustomer` should not automatically receive a new record for every POS import. It receives a record when the imported customer is actually linked to a registered Nimbus customer.

And yes, the `CustomerInvitation` schema can fully handle the invitation acceptance feature, including the pending, accepted, expired, and revoked states.


For the Nimbus Mobile App, I would design the experience around one simple concept:

**A customer can receive invitations from multiple merchants, accept or decline them, and see all their connected merchants in one place.**

The customer should not need to understand Shopify, Square, Lightspeed, or how Nimbus stores the data. Those are backend details.

Let me show you how the mobile app could work.

# **1. Overall Nimbus Mobile App flow**

**Merchant sends manual invitation**

john@gmail.com

**John receives email with invitation link**

**App installed**

Open invitation in Nimbus

**App not installed**

Install Nimbus, then open invitation

**Sign Up / Login / Already authenticated**

**Merchant Invitation screen**

Accept or Decline

**Merchant appears in Connected Merchants**

# **2. How I would design the Mobile App screens**

Below is a small interactive prototype of how the customer experience could look. You can select different scenarios and move through the screens.

Try a customer scenario

John already has a Nimbus account

John has never registered on Nimbus

John is already logged in to Nimbus

9:41

# **You're invited!**

ABC Clothing has invited you to connect through Nimbus Receipts.

To

john@gmail.com

Invitation valid for 30 days

Open invitation

Demo invitation: PENDING

Restart

Conceptual UI prototype only. The buttons simulate navigation and do not authenticate users or update Nimbus records.

## **3. What about POS-imported customers on Mobile?**

This is the other part of your application.

Suppose John is using Nimbus and Merchant B connects Square. Square imports John's customer profile.

John does **not** need a manual invitation just because Merchant B imported him.

From the mobile customer's perspective, I would provide a screen such as **Available Purchases** or **Find My Purchases** for POS records that have been identified as possible matches.

For example:

**Available Purchases**

Discover your purchase history

**ABC Clothing**

Potential matching purchases

Verify and Connect

**Coffee House**

Potential matching purchases

Verify and Connect

Illustrative design. Show merchant and purchase information only after suitable identity verification; an email match alone must not expose private records.

You can implement this separately from manual invitations.

Also, if your existing transaction system already handles customer matching and transaction visibility, there's no need to redesign that for this feature.

## **4. What if one customer receives invitations from three merchants?**

Suppose John receives invitations from:

- Merchant A — ABC Clothing
    
- Merchant B — XYZ Grocery
    
- Merchant C — Coffee House
    

All invitations can exist independently.

Your mobile app could have a **Merchant Invitations** screen under Profile or Settings.

**Merchant Invitations**

3 pending

ABC Clothing

Expires in 12 days

Accept

Decline

XYZ Grocery

Expires in 20 days

Accept

Decline

Coffee House

Expires in 28 days

Accept

Decline

Example invitation inbox layout. Each invitation is specific to one merchant.

The backend can list pending invitations associated with the customer's **verified email**, without automatically accepting them.

## **5. Backend effects of mobile actions**

|Mobile action|CustomerInvitation|MerchantCustomer|User|
|---|---|---|---|
|Open invitation link|Remains PENDING|No change|No change|
|Create Nimbus account|Remains PENDING|No change|Create|
|Verify invited email|Remains PENDING|No change|Update verification|
|Click Accept|Changes to ACCEPTED|Create/activate|Reuse|
|Click Decline|Changes to DECLINED|No change|No change|
|Invitation expires|Becomes EXPIRED|No change|No change|
|Customer signs in with wrong account|Remains PENDING|No change|No change|
|Customer opens already accepted invitation|Remains ACCEPTED|No duplicate|No change|

Because I've included a Decline action in the proposed mobile UI, I would add `DECLINED` to your `CustomerInvitationStatus` enum. It is not included in the earlier four-state enum.

## **6. Which screens are actually necessary?**

For the first version of Nimbus, you don't need to build every possible screen.

I would prioritize:

|Screen|Required?|Purpose|
|---|---|---|
|Invitation deep-link handling|Yes|Open correct invitation flow.|
|Existing Sign Up|Reuse|New users register.|
|Existing Login|Reuse|Existing users sign in.|
|Existing Email Verification|Reuse|Verify invited email ownership.|
|Accept Invitation|Yes — new|Explicit Accept/Decline actions.|
|Invitation Success|Yes — can be a modal|Confirmation after connection.|
|Invitation Expired/Error|Yes — can be a modal|Handle invalid, expired or revoked invitations.|
|Merchant Invitations list|Recommended|Manage multiple pending invitations.|
|Connected Merchants list|Recommended|Show active merchant relationships.|
|Available Purchases|Optional|Only if POS transaction linking isn't already handled elsewhere.|

**My recommendation for Nimbus:** Build the invitation deep-link handler and the Accept Invitation screen first, then reuse your existing authentication flow.

The experience should be:

**Invitation email → Open Nimbus → Login/Sign Up if required → Accept Invitation → Connection success → Existing Nimbus Home.**

For multiple merchant invitations, a Merchant Invitations screen is useful, but you can introduce it after the basic single-invitation flow works.

The key is that the customer must explicitly accept **manual invitations**, while POS-imported customer matching continues to operate independently.