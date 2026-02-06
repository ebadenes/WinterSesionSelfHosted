# Zulip

## Descripción
Chat colaborativo con stack completo (Postgres, Redis, RabbitMQ, Memcached).

## Volúmenes
- `${DATA_DIR}/zulip/data` → `/data`
- `${DATA_DIR}/zulip/data/db` → Postgres
- `${DATA_DIR}/zulip/data/redis` → Redis
- `${DATA_DIR}/zulip/data/rabbitmq` → RabbitMQ

## Variables
Archivo: `.env` raíz

## Endpoint
- https://zulip.${BASE_DOMAIN}

## Notas
Zulip necesita `LOADBALANCER_IPS` para confiar en el proxy.

## Fuente oficial
- https://github.com/zulip/docker-zulip
