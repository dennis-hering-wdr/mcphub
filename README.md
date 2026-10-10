# mcphub

Run [MCPHub](https://github.com/samanhappy/mcphub) with Docker Compose,
using file storage instead of PostgreSQL.

## Setup

Requires Docker Engine and Docker Compose v2.

1. Copy the environment template:
   ```sh
   cp .env.example .env
   ```
2. Edit `.env` and set a strong `ADMIN_PASSWORD` and a random `JWT_SECRET`.
   Generate each value separately with `openssl rand -hex 32`. Keep `.env`
   private; it is excluded from Git. `MCPHUB_PORT` optionally changes the host port.
3. Validate and start:
   ```sh
   docker compose config --quiet
   docker compose up -d
   ```
4. Open http://localhost:3000 (or your configured port) and sign in as `admin`
   with your chosen password. `ADMIN_PASSWORD` applies only when creating the
   initial account; changing it does not reset an existing account.

The dashboard and MCP endpoints are bound to `127.0.0.1` by default. For remote
access, use an SSH tunnel or an authenticated HTTPS reverse proxy; do not expose
the service directly to the public internet.

## Operations

```sh
docker compose logs -f mcphub
docker compose restart mcphub
docker compose down
```

Settings, users, uploaded packages, and credential encryption files persist in
the `mcphub-data` Docker volume across container recreation. Back up the entire
volume while the service is stopped, and keep the same `JWT_SECRET` across
restarts. Backups can contain sensitive credentials and must be kept private.
**`docker compose down -v` deletes the stored data.**

The image is pinned to `samanhappy/mcphub:1.1.2`. To upgrade, back up the data,
update the image tag in `compose.yaml`, then run:

```sh
docker compose pull
docker compose up -d
```

Database-dependent features such as Smart Routing and Better Auth/social login
are not available in this PostgreSQL-free setup.