# Gemini Project Context: HomeLab Environment

## Project Overview

This project is a comprehensive HomeLab environment managed via Docker Compose. It aims to centralize various services for home automation, network management, and communication. The entire stack is orchestrated using Docker, making it portable and easy to maintain.

The core services include:

*   **AdGuard Home**: A network-wide ad and tracker blocker.
*   **Caddy V2**: A reverse proxy and web server that manages internal traffic and SSL certificates.
*   **Evolution API**: An API for WhatsApp integration.
*   **n8n**: A workflow automation tool that connects various services.
*   **PostgreSQL**: A relational database for the Evolution API.
*   **Redis**: An in-memory data store for caching and session management.
*   **Portainer**: A web-based UI for managing Docker containers.

## Building and Running

The project is designed to be run with Docker Compose.

**Prerequisites:**

*   Docker and Docker Compose must be installed.
*   The `macvlan shim` network needs to be configured as described in the `README.md`.

**Environment Variables:**

Before running the project, you need to create a `.env` file in the root directory. You can use the `.env-example` as a template. This file contains crucial configuration variables for the services, including:

*   Paths for data storage (`PORTAINER_PATH`, `MEDIA_PATH`, `DOWNLOAD_PATH`)
*   System settings (`TIMEZONE`, `PUID`, `PGID`)
*   Network configuration (`CADDY_DOMAIN`, `SERVERIP`, `ADGUARD_PORT`)
*   Resource limits (`CPU_LIMIT_APP`, `MEM_LIMIT_APP`)
*   Database credentials (`PG_ROOT_PASSWORD`, `EVOLUTION_DB_USER`, `EVOLUTION_DB_PASSWORD`, etc.)
*   API keys (`EVOLUTION_AUTHENTICATION_API_KEY`)

**Commands:**

To start the services, use the following command:

```bash
docker compose up -d
```

To view the logs of a specific service, you can use:

```bash
docker logs -f <service_name>
```

For example, to view the logs of the Evolution API:

```bash
docker logs -f evolution-api
```

## Development Conventions

*   **Configuration as Code**: The entire stack is defined in the `docker-compose.yml` file, making it easy to version control and reproduce.
*   **Service Separation**: Each service runs in its own Docker container, isolating dependencies and improving security.
*   **Centralized Routing**: Caddy is used as a reverse proxy to route traffic to the appropriate services based on subdomains. The routing is defined in `docker/caddy/Caddyfile`.
*   **Data Persistence**: Docker volumes are used to persist data for services like PostgreSQL, Redis, and n8n.
