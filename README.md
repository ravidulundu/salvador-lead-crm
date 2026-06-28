# Salvador Lead CRM

Self-hosted CRM for Salvador-BA B2B lead tracking using NocoDB + Postgres.

## Stack

- NocoDB: Airtable-like self-hosted database/CRM
- Postgres: persistent database
- Docker Compose: Hostinger Docker Manager compatible

## Security

`docker-compose.yml` contains **no hard-coded passwords or tokens**. Configure all secrets in Hostinger environment variables.

Required variables:

| Variable | Example | Notes |
|---|---|---|
| `POSTGRES_DB` | `nocodb` | Database name |
| `POSTGRES_USER` | `nocodb` | Database user |
| `POSTGRES_PASSWORD` | long random string | Required secret |
| `NC_AUTH_JWT_SECRET` | long random string | Required secret for NocoDB auth |
| `CRM_DOMAIN` | `crm.example.com` | Domain routed by Traefik/Cloudflare |
| `NC_PUBLIC_URL` | `https://crm.example.com` | Public NocoDB URL |
| `TRAEFIK_ENABLE` | `true` | Enables Traefik labels |
| `TRAEFIK_NETWORK` | `traefik` | External Docker network used by Traefik |
| `TRAEFIK_ENTRYPOINT` | `websecure` | HTTPS entrypoint |
| `TRAEFIK_CERTRESOLVER` | `cloudflare` | Lets Traefik request certs via Cloudflare resolver |
| `NOCODB_PORT` | `8080` | Local 127.0.0.1 debug binding only |

Generate secrets locally:

```bash
openssl rand -base64 32
```


## Traefik + Cloudflare

This compose is prepared for traffic through Traefik and Cloudflare:

- NocoDB joins an external Docker network defined by `TRAEFIK_NETWORK`.
- Traefik labels route `Host(`${CRM_DOMAIN}`)` to NocoDB's internal port `8080`.
- TLS is enabled through `TRAEFIK_CERTRESOLVER`, default `cloudflare`.
- No real domain, token, password, or Cloudflare secret is committed.

If your Hostinger Traefik network is not named `traefik`, set `TRAEFIK_NETWORK` to the exact network name from Hostinger.

If Hostinger manages TLS/proxy without Traefik labels, set `TRAEFIK_ENABLE=false` and use Hostinger's domain mapping UI instead.

## Hostinger Docker Manager deploy

1. Create a new Docker/Compose app in Hostinger.
2. Use this repository URL or the raw `docker-compose.yml` URL.
3. Add environment variables from `.env.example` with real secret values.
4. Ensure the external Traefik network name matches `TRAEFIK_NETWORK` (default: `traefik`).
5. In Cloudflare DNS, point `CRM_DOMAIN` to the Hostinger server/proxy as your Traefik setup expects.
6. Deploy.
7. Open `NC_PUBLIC_URL`.
6. Create the first NocoDB admin user.
7. Import CSV files from `data/` into a `Leads` table.

## Suggested Leads table fields

- Business Name
- Category
- Neighborhood
- Phone / WhatsApp
- WhatsApp Link
- Website
- Instagram
- Google Maps
- Visible Problem
- Offer
- Quality
- Priority
- Status
- First Message PT-BR
- First Contact Date
- Follow-up Date
- Last Response
- Notes

## Suggested statuses

- New
- Verify
- Verified
- Contacted
- Replied
- Follow-up 1
- Follow-up 2
- Meeting
- Proposal
- Won
- Lost
- Not Fit

## Data files

- `data/salvador_b2b_leads_50.csv` — first 50 raw leads
- `data/salvador_A18_outreach_pack.csv` — A-quality outreach pack
