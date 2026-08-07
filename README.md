# SalesBudge docs (public)

Merchant and integrator documentation. **No secrets, AWS, or internal runbooks.**

**Live:** https://docs.salesbudge.com  

Private engineer / deploy docs live in sibling repo `salesbudge-backend/docs/`  
([ENGINEER_SETUP.md](https://github.com/wahiyatkavi/salesbudge-backend/blob/main/docs/ENGINEER_SETUP.md)).

## Host on Hostinger

1. DNS: A record `docs` → Hostinger web IP  
2. Upload to `docs.salesbudge.com` docroot: `index.html`, `_sidebar.md`, and the `docs/` folder  
3. Enable SSL + Force HTTPS  

Local preview: any static server from this folder (Docsify loads markdown in the browser).

## Pages

| Page | Audience |
|------|----------|
| [docs/index.md](docs/index.md) | Overview |
| [docs/getting-started.md](docs/getting-started.md) | First integration |
| [docs/sdk/install.md](docs/sdk/install.md) | Install the browser SDK |
| [docs/sdk/identify-and-events.md](docs/sdk/identify-and-events.md) | Identify + track events |
| [docs/api/external.md](docs/api/external.md) | External HTTP API |
| [docs/concepts/events.md](docs/concepts/events.md) | Event model |
