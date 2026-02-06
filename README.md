# Resistencia en la nube  
## Infra Stack self-hosted (Traefik + Apps)

> No es odio al cloud.  
> Es amor propio.

Este repositorio contiene el **stack de autonomía** usado en la demo de la charla  
**“Resistencia en la nube: self-hosted sobre Azure”**.

La idea es simple:  
usar la nube **como infraestructura**, no como destino.  
Máquinas, red y discos… el resto es tuyo.

---

## ¿Qué es este repo?

Un **stack modular self-hosted**, desplegado con **Docker Compose v2** y `include:`, pensado para:

- Reducir *vendor lock-in*
- Entender cada pieza crítica del sistema
- Poder mover el stack con fricción razonable
- Sustituir SaaS por alternativas abiertas y portables

Azure aquí es **hardware con Wi‑Fi**.  
Nada más, nada menos.

---

## Diagrama del stack

```text
                       Internet
                          |
                    DNS (A/AAAA)
                          |
                    80/443 (FW)
                          |
                   [ Public IP / VM ]
                          |
                +---------+----------+
                |   Azure (infra)    |
                |  VM + VNet + Disk  |
                +---------+----------+
                          |
                     Docker Engine
                          |
                 Docker Compose v2
              (root + include modules)
                          |
                 +--------+--------+
                 |                 |
              Traefik          (Internal net)
        (TLS/ACME + Routing)         |
                 |                   |
      +----------+----------+        |
      |          |          |        |
 immich.*   n8n.*    docmost.*       |
 stirling.* vaultwarden.* kuma.*     |
 zulip.*    pingvin.* linkwarden.*   |
 paperless.* planka.* nextcloud.*    |
 homer.*                              
      |
  Volúmenes persistentes
 (${DATA_DIR}/<service>/data|config)
      |
 Backups / Restore (pg_dump, tar, etc.)
```

---

## Filosofía

- Self-hosted ≠ on-prem ≠ anti-cloud  
- No se reimplementa Azure, se **evita depender de él**
- Todo corre en contenedores estándar
- Todo se configura desde un único `.env`
- Si mañana cambias de proveedor, **no reescribes el sistema**

---

## Requisitos

Infra mínima, cero magia:

- Docker Engine
- Docker Compose v2 (con soporte `include:`)
- DNS público apuntando al servidor
- Puertos **80 / 443** abiertos
- Una VM Linux (Azure, Hetzner, tu casa… da igual)

---

## Estructura del repositorio

```
.
├── .env.example
├── README.md
├── docker-compose.yml
└── services
    ├── traefik
    ├── immich
    ├── n8n
    ├── docmost
    ├── stirling-pdf
    ├── vaultwarden
    ├── uptime-kuma
    ├── zulip
    ├── pingvin-share
    ├── linkwarden
    ├── paperless-ngx
    ├── planka
    ├── nextcloud
    └── homer
```

Cada servicio es **autónomo, desacoplado y opcional**.

---

## DNS (subdominios)

Cada app vive en su propio subdominio.  
Traefik se encarga del resto.

Crea registros `A` / `AAAA` apuntando a la IP del servidor:

- `traefik.${BASE_DOMAIN}`
- `immich.${BASE_DOMAIN}`
- `n8n.${BASE_DOMAIN}`
- `docmost.${BASE_DOMAIN}`
- `stirling.${BASE_DOMAIN}`
- `vaultwarden.${BASE_DOMAIN}`
- `kuma.${BASE_DOMAIN}`
- `zulip.${BASE_DOMAIN}`
- `pingvin.${BASE_DOMAIN}`
- `linkwarden.${BASE_DOMAIN}`
- `paperless.${BASE_DOMAIN}`
- `planka.${BASE_DOMAIN}`
- `nextcloud.${BASE_DOMAIN}`
- `homer.${BASE_DOMAIN}`

---

## Activar / desactivar servicios

Aquí no hay Helm, ni magia negra.

Solo comenta o descomenta líneas `include:`  
en el `docker-compose.yml` raíz.

```yaml
include:
  - services/traefik/docker-compose.yml
  # - services/immich/docker-compose.yml
  # - services/n8n/docker-compose.yml
```

---

## Configuración inicial

1. Copia el archivo de entorno:
   ```bash
   cp .env.example .env
   ```

2. Ajusta dominios, rutas y credenciales
3. **Todos los servicios usan el mismo `.env`**
4. Homer usa un `config.yml` estático en `services/homer/config.yml`.

---

## Levantar el stack

```bash
docker compose up -d
```

Nada más.  
Traefik gestiona TLS, routing y certificados automáticamente.

---

## Backups (porque la libertad también es poder irte)

### Bases de datos (PostgreSQL)
```bash
docker exec -t <db> pg_dump -U <user> <db> > backup.sql
```

### SQLite (Pingvin Share)
Respaldar el volumen de datos.

### Volúmenes
```bash
tar -czf backup-<service>.tgz ${DATA_DIR}/<service>/data
tar -czf backup-<service>-config.tgz ${DATA_DIR}/<service>/config
```

Si puedes hacer backup, puedes migrar.  
Si puedes migrar, no hay lock-in.

---

## Troubleshooting

- **ACME rate limits**  
  Usa Let’s Encrypt staging si estás probando.  
  Borra `acme.json` antes de reintentar.

- **Permisos**  
  Verifica ownership y permisos de escritura en `${DATA_DIR}`.

- **Proxy headers**  
  Traefik añade `X-Forwarded-*` automáticamente.

- **Nextcloud**  
  Ajusta `TRUSTED_PROXIES` y `OVERWRITE*` si ves problemas de URL o IP.

---

## SaaS vs Stack (la demo)

Este stack sustituye servicios como:

- Google Photos → **Immich**
- Zapier / Make → **n8n**
- Notion → **Docmost**
- Adobe Acrobat → **Stirling-PDF**
- 1Password → **Vaultwarden**
- UptimeRobot → **Uptime Kuma**
- Slack → **Zulip**
- WeTransfer → **Pingvin Share**
- Linktree → **Linkwarden**
- Trello → **Planka**
- Dropbox → **Nextcloud**

Mismo resultado.  
Más control.

---

## Fuentes oficiales

Nada inventado, todo estándar:

- Traefik: https://doc.traefik.io/traefik/
- Immich: https://immich.app/docs/install/docker-compose
- n8n: https://docs.n8n.io/hosting/
- Docmost: https://docmost.com/docs/installation/
- Stirling-PDF: https://docs.stirlingpdf.com/
- Vaultwarden: https://github.com/dani-garcia/vaultwarden/wiki
- Uptime Kuma: https://hub.docker.com/r/louislam/uptime-kuma/
- Zulip: https://github.com/zulip/docker-zulip
- Pingvin Share: https://stonith404.github.io/pingvin-share/
- Linkwarden: https://docs.linkwarden.app/
- Paperless-ngx: https://docs.paperless-ngx.com/
- Planka: https://docs.planka.cloud/
- Nextcloud: https://hub.docker.com/_/nextcloud/
- Homer: https://github.com/bastienwirtz/homer
