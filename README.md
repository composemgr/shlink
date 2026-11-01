## 👋 Welcome to shlink 🚀

Self-hosted URL shortener with visit analytics

## 📋 Description

Shlink is a self-hosted URL shortener. Each short code records its own visit
analytics: referrer, country, city, browser and operating system. This stack
runs the official `shlinkio/shlink` image with a MariaDB backend, published on
the host docker bridge so the bundled nginx proxy can reach it.

## 🚀 Services

- **app**: shlinkio/shlink:latest (container port 8080)

### Infrastructure Components

- **db**: mariadb:latest - MariaDB database

## 📦 Installation

### Option 1: Quick Install
```bash
curl -q -LSsf "https://raw.githubusercontent.com/composemgr/shlink/main/docker-compose.yaml" -o compose.yml
```

### Option 2: Git Clone
```bash
git clone "https://github.com/composemgr/shlink" ~/.local/srv/docker/shlink
cd ~/.local/srv/docker/shlink
cp default.env .env
docker compose --env-file .env up -d
```

### Option 3: Using composemgr
```bash
composemgr install shlink
```

## 🔧 Configuration

### Environment Variables

```shell
TZ=America/New_York
APP_ORG_NAME=Shlink
DB_USER_NAME=shlink
DB_USER_PASS=replace-with-a-random-password
APP_API_TOKEN=replace-with-a-random-32-char-string
GEOLITE_LICENSE_KEY=
SHLINK_SHORT_CODES_LENGTH=6
SHLINK_REDIRECT_STATUS_CODE=301
```

See `docker-compose.yaml` for complete list of configurable options.

### Environment Files

`default.env.sample` holds the full set of variables and is copied to `.env`
before starting the stack:

```bash
cp default.env .env
docker compose --env-file .env up -d
```

`app.env.sample` holds the app-specific overrides and is copied to `app.env`:

```bash
cp app.env.sample app.env
```

`composemgr up` applies `app.env` and `default.env` automatically. Raw
`docker compose` commands need `--env-file .env` to pick up the same values.

### Reverse Proxy

Three standalone compose files ship in this repo:

| File | Use |
|------|-----|
| `docker-compose.yaml` | Default, published on `172.17.0.1:64343` |
| `docker-compose.traefik.yaml` | Behind an existing external `traefik` network |
| `docker-compose.tunnel.yaml` | Behind an existing external `cloudflare` tunnel network |

```bash
docker compose -f docker-compose.traefik.yaml --env-file .env up -d
```

Only the `app` service carries the proxy labels and joins the external
network. The database stays on the internal project network. Set
`IS_HTTPS_ENABLED` to `true` in the proxy variants when short URLs should be
generated with an `https` scheme.

## 🌐 Access

- **Web Interface**: http://172.17.0.1:64343

The admin UI creates the first account, short URLs are then created from the
UI or over the REST API using the key from `APP_API_TOKEN`.

## 📂 Volumes

- `./volumes/db/mariadb/shlink` - Database data

## 🔐 Security

- Set `APP_API_TOKEN` to a random 32 character value before the first start,
  Shlink only issues that key on an empty database
- Change `DB_USER_PASS` and `DB_ADMIN_PASS` before deploying
- `GEOLITE_LICENSE_KEY` is a MaxMind license key, treat it as a secret
- The database is reachable only on the internal project network, never
  publish it
- Use HTTPS via the reverse proxy in production
- Regularly update Docker images for security patches
- Backup your data regularly

## 🔍 Logging

```shell
docker compose --env-file .env logs -f app
```

## 🛠️ Management

```bash
# Start services
docker compose --env-file .env up -d

# Stop services
docker compose --env-file .env down

# Update to latest images
docker compose --env-file .env pull && docker compose --env-file .env up -d

# View logs
docker compose --env-file .env logs -f

# Restart services
docker compose --env-file .env restart
```

## 📋 Requirements

- Docker Engine 20.10+
- Docker Compose V2+

## 🤝 Author

🤖 casjay: [Github](https://github.com/casjay) 🤖  
🦄 composemgr: [Github](https://github.com/composemgr) 🦄
