# CRM lead bulk upload

Upload many walk-in / list leads from a CSV in the Sales CRM (not the external API).

## Who can upload

Managers / owners (roles that can create manual leads). Individual salespeople cannot bulk upload.

## Limits

| Limit | Value |
|-------|-------|
| **Max data rows per file** | **500** |
| Format | CSV (UTF-8), header row required |
| File | Non-empty `.csv` |

Rows after 500 are rejected with `Row limit exceeded (max 500)`.

## Template columns

Download **lead-upload-template.csv** from the Leads page, or use:

```csv
title,email,phone,firstName,lastName
Walk-in lead,jane@example.com,+15551234567,Jane,Doe
```

### Header rules

- Headers are case-insensitive; punctuation is ignored (`First Name` → `firstname`).  
- File **must** include an `email` and/or `phone` column.  

### Field conditions

| Field | Required | Max (via API validation) | Behavior |
|-------|----------|--------------------------|----------|
| `email` | **email or phone required** | 320 | Valid email format |
| `phone` | **email or phone required** | 50 | |
| `title` | No | 200 | If blank → built from name, else email, else phone |
| `firstName` | No | 100 | Used in default title |
| `lastName` | No | 100 | Used in default title |

Blank data lines are skipped. A row with neither email nor phone → **failed** (`email or phone is required`).

## What happens per row

1. Upsert a **customer** from email/phone (+ names).  
2. If an **open** lead already exists for that customer (`NEW` / `CONTACTED` / `QUALIFIED`):  
   - Match it (title may update) → counted as **skipped** (not a new MANUAL create).  
3. Otherwise create a new lead with status `NEW`, source `MANUAL` → **created**.  

## Result counts

| Count | Meaning |
|-------|---------|
| `created` | New MANUAL lead created |
| `skipped` | Matched an existing open lead |
| `failed` | Row error (listed with row number) |

## Example good rows

```csv
title,email,phone,firstName,lastName
Acme intro,ops@acme.com,,Ops,Acme
Phone only lead,,+919876543210,Ravi,Kumar
Full,jane@example.com,+15551234567,Jane,Doe
```

## Example failing rows

```csv
title,email,phone,firstName,lastName
Missing contact,,,No,Contact
```

→ failed: `email or phone is required`

## Related

- Manual single lead uses the same field rules as one CSV row.  
- Website/SDK lead capture: [Identify & events](../sdk/identify-and-events.md).
