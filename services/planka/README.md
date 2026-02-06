# Planka

## Descripción
Kanban open source.

## Volúmenes
- `${DATA_DIR}/planka/data/app` → `/app/data`
- `${DATA_DIR}/planka/data/db` → Postgres

## Variables
Archivo: `.env` raíz

## Endpoint
- https://planka.${BASE_DOMAIN}

## Nota
Crear admin:
`docker compose run --rm planka npm run db:create-admin-user`

## Fuente oficial
- https://docs.planka.cloud/docs/installation/docker/production-version
