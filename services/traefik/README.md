# Traefik

## Descripción
Proxy reverso con SSL automático (Let's Encrypt). Solo expone 80/443.

## Volúmenes
- `${DATA_DIR}/traefik/data` → `/letsencrypt` (acme.json)

## Variables
Archivo: `.env` raíz
- `TRAEFIK_DASHBOARD_USERS` (formato htpasswd)

## Endpoint
- https://traefik.${BASE_DOMAIN}

## Fuente oficial
- https://doc.traefik.io/traefik/
