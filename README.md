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
| `NOCODB_PORT` | `8080` | External port mapping |
| `NC_PUBLIC_URL` | `https://crm.example.com` | Optional public URL |

Generate secrets locally:

```bash
openssl rand -base64 32
```

## Hostinger Docker Manager deploy

1. Create a new Docker/Compose app in Hostinger.
2. Use this repository URL or the raw `docker-compose.yml` URL.
3. Add environment variables from `.env.example` with real secret values.
4. Deploy.
5. Open the mapped URL/port.
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
