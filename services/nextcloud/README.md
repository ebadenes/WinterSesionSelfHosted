# Nextcloud

## Descripción
Nube privada con Postgres y Redis.

## Volúmenes
- `${DATA_DIR}/nextcloud/data/html` → `/var/www/html`
- `${DATA_DIR}/nextcloud/data/db` → Postgres
- `${DATA_DIR}/nextcloud/data/redis` → Redis

## Variables
Archivo: `.env` raíz

## Endpoint
- https://nextcloud.${BASE_DOMAIN}

## Fuente oficial
- https://hub.docker.com/_/nextcloud/
