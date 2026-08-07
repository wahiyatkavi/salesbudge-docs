# Getting started

1. Create a merchant account in the SalesBudge CRM and open **Org → API Keys**.
2. Copy a `cp_live_…` key (Integration/SDK scope is enough for website tracking).
3. Install the [browser SDK](sdk/install.md) on your site with that key.
4. Fire page/product events and call `identify()` on lead forms — see [identify and events](sdk/identify-and-events.md).
5. Confirm visitors, events, and leads in the CRM Marketing and Sales workspaces.

**Audiences:** Marketing **Visitors** = anonymous SDK sessions; **Identified** = shared contact info; Sales **Customers** = converted buyers (lead WON).

Production API base: `https://api.salesbudge.com`  
CDN SDK: `https://cdn.salesbudge.com/sdk/customer-platform.min.js`  
Local API (developers): `http://localhost:8080`

Sign up at `https://app.salesbudge.com` creates a **new** organization. Joining an existing org is **invitation-only** (Org → Team). After signup, open your tenant subdomain (`https://{slug}.salesbudge.com`), not `app`.
