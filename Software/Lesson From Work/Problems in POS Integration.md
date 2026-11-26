status: Connection State
CONNECTED	Store connected
ERROR	Connection needs attention; reconnect
DISCONNECTED	Store not connected

syncStatus: Data Sync State
SYNCING	Import running
COMPLETED	Last full sync succeeded
FAILED	Latest sync failed
null	Unknown/not started; also cleared on disconnect

Example: CONNECTED + FAILED means connection valid, sync failed → Retry Sync, not reconnect.
Check status first: ERROR means Reconnect, regardless of previous sync state.

Reconnect vs Retry
Frontend needs two separate actions:
- Reconnect: credentials invalid or authorization revoked. Repeat OAuth flow using fresh install/auth URL.
- Retry Sync: connection valid, data import failed. Call sync mutation directly. No OAuth needed.
Do not disconnect before reconnecting same store. Existing backend permits reconnecting same account in ERROR state. Disconnect first only when switching to different store/account.
1. Frontend Decision Table
Check connection status first, then syncStatus:
Backend state	Frontend display	Main action
DISCONNECTED, connection: null	Not connected	Connect
ERROR, any sync state	Connection needs attention	Reconnect
CONNECTED + FAILED	Sync failed	Retry Sync
CONNECTED + SYNCING	Syncing	Disable duplicate sync; poll status
CONNECTED + COMPLETED	Completed	Sync Now
CONNECTED + syncStatus: null	Sync status unavailable	Sync Now
Connection state takes priority. For example, ERROR + COMPLETED means previous sync succeeded, but connection broke afterward. Show Reconnect, not healthy connection.
2. Reconnect for Each POS
Yes, use squareAuthUrl, lightspeedInstallUrl, or createShopifyInstallUrl again. Always request fresh URL; do not reuse old OAuth code/state.
Provider	Start OAuth	Response	Finish connection
Shopify	Mutation createShopifyInstallUrl(input: { shop })	URL string	Backend callback handles exchange
Lightspeed	Query lightspeedInstallUrl	URL string	Backend callback handles exchange
Square	Query squareAuthUrl	{ url }	Frontend calls connectSquare(input: { code, state }) after redirect
Shopify
mutation {
  createShopifyInstallUrl(
    input: { shop: "example.myshopify.com" }
  )
}
Flow:
User clicks Reconnect
  -> Request fresh install URL
  -> Redirect browser to returned URL
  -> Merchant authorizes Shopify
  -> Shopify redirects to backend /shopify/callback
  -> Backend exchanges credentials and starts initial sync
  -> Backend redirects frontend with ?shopify=connected
  -> Frontend refetches getPosIntegration
Failure redirect: ?shopify=error.
For reconnect, use connection.accountIdentifier as Shopify shop domain.
Lightspeed
query {
  lightspeedInstallUrl
}
Flow:
User clicks Reconnect
  -> Request fresh install URL
  -> Redirect browser to returned URL
  -> Merchant authorizes Lightspeed
  -> Lightspeed redirects to backend /lightspeed/callback
  -> Backend exchanges credentials and starts initial import
  -> Backend redirects frontend with lightspeed=success
  -> Frontend refetches getPosIntegration
Failure redirect: lightspeed=error.
Square
query {
  squareAuthUrl {
    url
  }
}
Redirect browser to returned URL. Square redirects to configured SQUARE_REDIRECT_URL.
Current backend has no Square OAuth callback controller. Configured destination must support frontend receiving code and state, then calling:
mutation ConnectSquare($input: ConnectSquareInput!) {
  connectSquare(input: $input) {
    message
  }
}
Variables:
{
  "input": {
    "code": "code-from-square-redirect",
    "state": "state-from-square-redirect"
  }
}
Backend exchanges credentials and starts initial sync. Frontend then refetches getPosIntegration.
For all three: OAuth success means connected, not sync completed. Use persisted syncStatus to display progress.
3. Retry Failed Sync
When status === CONNECTED and syncStatus === FAILED, use:
Provider	Operation	Arguments
Shopify	Mutation syncShopifyOrders	shop
Lightspeed	Mutation syncLightspeed	None
Square	Mutation syncSquare	None
Shopify:
mutation {
  syncShopifyOrders(shop: "example.myshopify.com")
}
Despite name, current full-sync path syncs customers before launching bulk orders export. Returned true means submission succeeded, not bulk ingestion completed.
Lightspeed:
mutation {
  syncLightspeed {
    message
  }
}
Square:
mutation {
  syncSquare {
    message
  }
}
Lightspeed and Square currently await sync completion before returning. Frontend can poll status while request remains pending.
If request fails or times out, refetch status before deciding next action. A failed browser request does not prove backend stopped processing.
4. What Frontend Receives
Use existing GraphQL endpoint with:
query {
  getPosIntegration {
    message
    data {
      provider
      name
      description
      logoKey
      status
      actions
      connection {
        id
        accountIdentifier
        status
        syncStatus
        errorCode
        connectedAt
        lastSyncedAt
      }
    }
  }
}
Example selected fields for connected Square with failed sync:
{
  "data": {
    "getPosIntegration": {
      "data": [
        {
          "provider": "SQUARE",
          "status": "CONNECTED",
          "actions": [],
          "connection": {
            "id": "integration-id",
            "accountIdentifier": "Example Store",
            "status": "CONNECTED",
            "syncStatus": "FAILED",
            "errorCode": "SQUARE_SYNC_FAILED",
            "connectedAt": "2026-11-23T09:00:00.000Z",
            "lastSyncedAt": "2026-11-23T09:05:00.000Z"
          }
        }
      ]
    }
  }
}
Actual response includes cards for all configured POS providers.
Disconnected provider returns:
{
  "provider": "SHOPIFY",
  "status": "DISCONNECTED",
  "actions": [],
  "connection": null
}
Important details:
- actions currently always returns []. Frontend must derive buttons from states.
- errorCode is machine-readable reason, not display-ready text. Map to friendly frontend message.
- lastSyncedAt means last successful full sync, not latest attempt.
- No percentage progress or lastSyncErrorAt currently exposed.
- Tokens and Shopify internal bulkSync metadata are not exposed.
5. Suggested Frontend Logic
function getPrimaryAction(card) {
  if (card.status === "DISCONNECTED") return "CONNECT";
  if (card.status === "ERROR") return "RECONNECT";

  switch (card.connection?.syncStatus) {
    case "SYNCING":
      return "SHOW_PROGRESS";
    case "FAILED":
      return "RETRY_SYNC";
    default:
      return "SYNC_NOW";
  }
}
Recommended polling: every 3–5 seconds while SYNCING, with backoff for long imports. Stop on COMPLETED, FAILED, ERROR, or DISCONNECTED; refetch when page regains focus.
Rule to give frontend team:  
Connection broken → OAuth again. Connection healthy but sync failed → sync mutation only.
Operation definitions: Shopify resolver (apps/web-api/src/modules/shopify/shopify.resolver.ts), Lightspeed resolver (apps/web-api/src/modules/lightspeed/lightspeed.resolver.ts), Square resolver (apps/web-api/src/modules/square/square.resolver.ts). Response fields: POS response DTO (apps/web-api/src/modules/pos-integration/dto/response/pos-integration.response.ts).