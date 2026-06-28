# Salvador Lead CRM

Self-hosted CRM for Salvador-BA B2B lead tracking using **Baserow**.

This replaced the earlier NocoDB version because Baserow has clearer API token support, so Neil can manage leads programmatically.

## Stack

- Baserow all-in-one Docker image
- Persistent Docker volume
- Hostinger Docker Manager
- Traefik routing in the same style as the working `dulunduztec.com.br` app

## Security

`docker-compose.yml` contains **no hard-coded passwords or tokens**. Configure secrets in Hostinger environment variables.

Required variables:

| Variable | Example | Notes |
|---|---|---|
| `CRM_DOMAIN` | `crm.dulunduztec.com.br` | Kept for Baserow allowed hosts; Traefik label is hardcoded to avoid Hostinger label interpolation issues |
| `BASEROW_PUBLIC_URL` | `https://crm.dulunduztec.com.br` | Public Baserow URL |
| `BASEROW_SECRET_KEY` | long random string | Required secret |

Generate secret locally:

```bash
openssl rand -base64 48
```

## Hostinger Docker Manager deploy

1. Create/verify DNS record for `crm.dulunduztec.com.br`.
2. In Hostinger Docker Manager, create a Compose app from this repository.
3. Add environment variables from `.env.example` with real values.
4. Deploy.
5. Open `https://crm.dulunduztec.com.br`.
6. Create the first Baserow admin/user account.
7. Create a workspace/database for Salvador leads.
8. Create a database token so Neil can access the API.

## Traefik labels

The compose now mirrors your working Hostinger example as closely as possible:

- Service name is `web`
- Uses `expose`, not `ports`
- Router name ends with `-web`
- Cert resolver is `letsencrypt`
- Service load balancer points to internal port `80`

```yaml
services:
  web:
    image: baserow/baserow:latest
    restart: unless-stopped
    expose:
      - "80"
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.salvador-lead-crm-web.rule=Host(`crm.dulunduztec.com.br`)"
      - "traefik.http.routers.salvador-lead-crm-web.entrypoints=websecure"
      - "traefik.http.routers.salvador-lead-crm-web.tls=true"
      - "traefik.http.routers.salvador-lead-crm-web.tls.certresolver=letsencrypt"
      - "traefik.http.services.salvador-lead-crm-web.loadbalancer.server.port=80"
```

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
