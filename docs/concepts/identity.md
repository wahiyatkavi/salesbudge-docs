# Visitors, leads & customers

How identity objects relate in SalesBudge.

## One person, one customer id

| Term | Meaning |
|------|---------|
| **Customer** | Core identity record (UUID). Holds anonymous/identified flag, identifiers, devices, events. |
| **Visitor** | Marketing view of a customer — anonymous or identified. |
| **Lead** | Sales work item that **points at** a `customerId`. Not a second person. |

When a visitor “becomes a lead”, SalesBudge does **not** create a duplicate profile. The same customer id is used; `anonymous` becomes `false` after identify, and a lead row may be created by automation.

## Lifecycle

```text
Anonymous customer (SDK device register)
        │
        │  browse events (PRODUCT_VIEWED, …)
        ▼
Identify (email / phone / externalId)
        │
        ▼
Identified customer  ──► optional Sales lead (automation / PII rules)
        │
        ▼
Lead WON  ──► appears in Sales “Customers” (converted buyers)
```

## CRM lists

| List | Who appears |
|------|-------------|
| Marketing → Visitors (anonymous) | `anonymous = true`, not merged |
| Marketing → Identified | `anonymous = false` |
| Sales → Leads | Lead rows linked to customers |
| Sales → Customers | Typically converted (lead `WON`), permission-scoped |

## Anonymize

CRM **Anonymize** (from lead or sales customer screens) wipes PII on the **customer**:

- Deletes email/phone identifiers and name traits  
- Sets `status = ANONYMIZED`  
- Cannot be undone  
- Detail APIs return forbidden for that id; anonymized rows are hidden from marketing visitor lists  

There is no separate “anonymize visitor” button — use the customer/lead anonymize action (same id).

## Related

- [Customers API](../api/customers.md)  
- [Identify & events](../sdk/identify-and-events.md)  
- [Lead bulk upload](../guides/lead-bulk-upload.md)
