# Salvador Lead CRM

Self-hosted Baserow CRM for Salvador-BA B2B lead tracking.

## Hostinger deploy

Use this repository in Hostinger Docker Manager:

```text
https://github.com/ravidulundu/salvador-lead-crm
```

## Required environment variables

```env
CRM_DOMAIN=crm.dulunduztec.com.br
BASEROW_SECRET_KEY=CHANGE...
```

Generate secret:

```bash
openssl rand -base64 48
```

## Current compose

```yaml
services:
  baserow:
    image: baserow/baserow:latest
    restart: unless-stopped
    expose:
      - "80"
    environment:
      BASEROW_PUBLIC_URL: "https://${CRM_DOMAIN:?set CRM_DOMAIN}"
      SECRET_KEY: "${BASEROW_SECRET_KEY:?set BASEROW_SECRET_KEY}"
      BASEROW_EXTRA_ALLOWED_HOSTS: "${CRM_DOMAIN:?set CRM_DOMAIN}"
    volumes:
      - baserow_data:/baserow/data
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.salvador-lead-crm.rule=Host(`${CRM_DOMAIN:?set CRM_DOMAIN}`)"
      - "traefik.http.routers.salvador-lead-crm.entrypoints=websecure"
      - "traefik.http.routers.salvador-lead-crm.tls=true"
      - "traefik.http.routers.salvador-lead-crm.tls.certresolver=letsencrypt"
      - "traefik.http.routers.salvador-lead-crm.service=salvador-lead-crm"
      - "traefik.http.services.salvador-lead-crm.loadbalancer.server.port=80"

volumes:
  baserow_data:
```

## Data files

- `data/salvador_b2b_leads_50.csv`
- `data/salvador_A18_outreach_pack.csv`

## Note about healthcheck

No Docker healthcheck is configured. On Hostinger, a failing container healthcheck can remove the service from Traefik routing and produce the plain `404 page not found` screen even when the container has been running. Baserow first boot and onboarding can make root-page healthchecks unreliable, so routing is left to Traefik labels only.
