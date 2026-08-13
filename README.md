# SalesBudge docs (public)

Merchant and integrator documentation. **No secrets, AWS, or internal runbooks.**

**Live:** https://docs.salesbudge.com  

Private engineer / deploy docs live in sibling repo `salesbudge-backend/docs/`  
([ENGINEER_SETUP.md](https://github.com/wahiyatkavi/salesbudge-backend/blob/main/docs/ENGINEER_SETUP.md)).

## Deploy

Merges to `main` run `.github/workflows/deploy.yml` (S3 + CloudFront invalidation).

Local preview: any static server from this folder (Docsify loads markdown in the browser).

```bash
npx --yes serve .
# open http://localhost:3000
```

## Pages

| Area | Contents |
|------|----------|
| Getting started | Account, API key, ping |
| Guides | Website, server, CRM lead CSV upload |
| SDK | Install, method reference, identify, delivery |
| External API | Auth, idempotency, customers, events, devices, products, errors |
| Concepts | Events, custom events, identity model |
