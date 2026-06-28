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
| `CRM_DOMAIN` | `crm.dulunduztec.com.br` | Domain routed by Traefik |
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

The compose uses the same Hostinger/Traefik pattern as the working `dulunduztec.com.br` app:

```yaml
expose:
  - "80"
labels:
  - "traefik.enable=true"
  - "traefik.http.routers.salvador-lead-crm.rule=Host(`${CRM_DOMAIN}`)"
  - "traefik.http.routers.salvador-lead-crm.entrypoints=websecure"
  - "traefik.http.routers.salvador-lead-crm.tls=true"
  - "traefik.http.routers.salvador-lead-crm.tls.certresolver=letsencrypt"
  - "traefik.http.services.salvador-lead-crm.loadbalancer.server.port=80"
```

## Baserow API token

After login:

1. Open Baserow.
2. Create a workspace/database.
3. Go to user settings or database settings.
4. Create a database/API token.
5. Grant read/write permissions to the leads table.
6. Give Neil the token through a safer channel or rotate after setup.

Baserow API base URL:

```text
https://crm.dulunduztec.com.br/api/
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
