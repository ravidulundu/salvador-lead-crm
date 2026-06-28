# Salvador Lead CRM

Self-hosted CRM for Salvador-BA B2B lead tracking using NocoDB + Postgres.

## Stack

- NocoDB: Airtable-like self-hosted database/CRM
- Postgres: persistent database
- Docker Compose: Hostinger Docker Manager compatible
- Traefik routing: same style as `dulunduztec.com.br` compose

## Security

`docker-compose.yml` contains **no hard-coded passwords or tokens**. Configure all secrets in Hostinger environment variables.

Required variables:

| Variable | Example | Notes |
|---|---|---|
| `POSTGRES_DB` | `nocodb` | Database name |
| `POSTGRES_USER` | `nocodb` | Database user |
| `POSTGRES_PASSWORD` | long random string | Required secret |
| `NC_AUTH_JWT_SECRET` | long random string | Required secret for NocoDB auth |
| `CRM_DOMAIN` | `crm.dulunduztec.com.br` | Domain routed by Traefik |
| `NC_PUBLIC_URL` | `https://crm.dulunduztec.com.br` | Public NocoDB URL |

Generate secrets locally:

```bash
openssl rand -base64 32
```

## Hostinger Docker Manager deploy

1. Create DNS record in Cloudflare/Hostinger for the CRM subdomain, e.g. `crm.dulunduztec.com.br`.
2. In Hostinger Docker Manager, create a Compose app from this repository.
3. Add environment variables from `.env.example` with real secret values.
4. Deploy.
5. Open `https://crm.dulunduztec.com.br`.
6. Create the first NocoDB admin user.
7. Import CSV files from `data/` into a `Leads` table.

## Traefik labels

The compose uses the same Hostinger/Traefik pattern as the working `dulunduztec.com.br` app:

```yaml
expose:
  - "8080"
labels:
  - "traefik.enable=true"
  - "traefik.http.routers.salvador-lead-crm.rule=Host(`${CRM_DOMAIN}`)"
  - "traefik.http.routers.salvador-lead-crm.entrypoints=websecure"
  - "traefik.http.routers.salvador-lead-crm.tls=true"
  - "traefik.http.routers.salvador-lead-crm.tls.certresolver=letsencrypt"
  - "traefik.http.services.salvador-lead-crm.loadbalancer.server.port=8080"
```

No external Traefik network is declared because your working Hostinger example does not declare one.

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
