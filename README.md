# Salvador Lead CRM

Self-hosted Baserow CRM for Salvador-BA B2B lead tracking.

## Hostinger deploy

Use this repository in Hostinger Docker Manager:

```text
https://github.com/ravidulundu/salvador-lead-crm
```

The compose follows the provided working Hostinger/Traefik pattern for Baserow.

## Required `.env` / environment variables

```env
BASEROW_VERSION=2.2.2
BASEROW_DOMAIN=crm.dulunduztec.com.br
TRAEFIK_ROUTER_NAME=salvador-lead-crm
TRAEFIK_SERVICE_NAME=salvador-lead-crm
SECRET_KEY=CHANGE_ME_LONG_RANDOM_SECRET
```

Generate `SECRET_KEY`:

```bash
openssl rand -base64 48
```

## Compose

```yaml
services:
  baserow:
    image: baserow/baserow:${BASEROW_VERSION:-2.2.2}
    restart: unless-stopped
    expose:
      - "80"
    env_file:
      - .env
    environment:
      BASEROW_PUBLIC_URL: "https://${BASEROW_DOMAIN:?set BASEROW_DOMAIN}"
    volumes:
      - baserow-data:/baserow/data
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.${TRAEFIK_ROUTER_NAME:?set TRAEFIK_ROUTER_NAME}.rule=Host(`${BASEROW_DOMAIN:?set BASEROW_DOMAIN}`)"
      - "traefik.http.routers.${TRAEFIK_ROUTER_NAME}.entrypoints=websecure"
      - "traefik.http.routers.${TRAEFIK_ROUTER_NAME}.tls=true"
      - "traefik.http.routers.${TRAEFIK_ROUTER_NAME}.tls.certresolver=letsencrypt"
      - "traefik.http.services.${TRAEFIK_SERVICE_NAME:?set TRAEFIK_SERVICE_NAME}.loadbalancer.server.port=80"
    healthcheck:
      test: ["CMD", "curl", "-f", "http://127.0.0.1/"]
      interval: 30s
      timeout: 10s
      retries: 5
      start_period: 60s

volumes:
  baserow-data:
```

## Data files

- `data/salvador_b2b_leads_50.csv`
- `data/salvador_A18_outreach_pack.csv`
