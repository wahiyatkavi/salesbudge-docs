# CRM lead bulk upload

Upload many walk-in / list leads from a CSV in the Sales CRM (not the external API).

## Who can upload

Managers / owners (roles that can create manual leads). Individual salespeople cannot bulk upload.

## Limits

| Limit | Value |
|-------|-------|
| **Max lead (data) rows** | **500** |
| Header row | **Not counted** toward the 500 |
| So a valid file can be | **1 header + up to 500 lead rows** (= 501 lines total) |
| Format | CSV (UTF-8), header row required |
| File | Non-empty `.csv` |

If you upload 501 lead rows (plus header), row 502 in the file fails with:  
`Data row limit exceeded (max 500 lead rows; the header row is not counted)`.

> There is no 600-row limit. The product limit is **500 data rows**.

## Template columns

Download **CSV template** from the Leads page, or use:

```csv
title,email,phone,firstName,lastName
Walk-in lead,jane@example.com,+15551234567,Jane,Doe
Local phone only,,9876543210,Ravi,Kumar
```

### Header rules

- Headers are case-insensitive; punctuation is ignored (`First Name` → `firstname`).  
- File **must** include an `email` and/or `phone` column.  

### Field conditions

| Field | Required | Max | Behavior |
|-------|----------|-----|----------|
| `email` | **email or phone required** | 320 | Must look like an email (`@` required) |
| `phone` | **email or phone required** | 50 chars / 7–15 digits | See phone rules below |
| `title` | No | 200 | If blank → built from name, else email, else phone |
| `firstName` | No | 100 | Used in default title |
| `lastName` | No | 100 | Used in default title |

Blank data lines are skipped.

## Phone / country code

**Country calling code is optional.**

| Input | Accepted? |
|-------|-----------|
| `+919876543210` | Yes (E.164-style with `+`) |
| `9876543210` | Yes (local digits, no country code) |
| `(555) 123-4567` | Yes (formatting stripped to digits) |
| `+` only / fewer than 7 digits | No — clear validation error |

After stripping formatting, phones need **7–15 digits**.

## What happens per row

1. Upsert a **customer** from email/phone (+ names).  
2. If an **open** lead already exists for that customer (`NEW` / `CONTACTED` / `QUALIFIED`):  
   - Match it (title may update) → counted as **skipped**.  
3. Otherwise create a new lead with status `NEW`, source `MANUAL` → **created**.  

## Result counts & error CSV

| Count | Meaning |
|-------|---------|
| `created` | New MANUAL lead created |
| `skipped` | Matched an existing open lead |
| `failed` | Row error |

When any rows fail, the CRM shows an **error table** and **Download errors CSV** with columns:

`row,error,title,email,phone,firstName,lastName`

Fix those rows (or re-upload only the failed ones) and upload again.

## Related

- Manual single lead uses the same contact rules.  
- Website/SDK lead capture: [Identify & events](../sdk/identify-and-events.md).
