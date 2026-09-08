# Docker Node.js & NGINX Development Environment

A lightweight, containerized development environment featuring a **Node.js (v24-slim)** application container fronted by an **NGINX (alpine)** reverse proxy, configured using Docker Compose, a Makefile, and custom directory structures.

## Architecture & Services

- **`app1`**: Runs a Node.js 24 environment (`node:24-slim`) mapping port `3000` internally. It executes a basic HTTP server located in `./www/app1/server.mjs`.
- **`web`**: Runs an NGINX reverse proxy (`nginx:alpine`) mapped to host port `80`. It routes incoming traffic to `app1` using dynamic DNS resolution (`127.0.0.11`) to prevent container startup race conditions.
- **Network**: Both services communicate through a custom bridge network named `nodejs-network`.

---

## Directory Structure

```text
.
├── docker-compose.yml
├── Makefile
├── nginx
│   └── conf.d
│       ├── app1.conf
│       └── README.md
├── README.md
└── www
    ├── app1
    │   └── server.mjs
    └── README.md
```

---

## Prerequisites

- [Docker](https://docs.docker.com/get-docker/)
- [Docker Compose](https://docs.docker.com/compose/install/)
- `make` (optional, for convenience commands)

---

## Getting Started

### 1. Clone or Set Up the Project
Ensure your project directory matches the structure above.

### 2. Start the Environment
You can start the containers in detached mode using the provided Makefile or Docker Compose directly:

Using `make`:
```bash
make up
```

Using Docker Compose:
```bash
docker compose up -d
```

### 3. Stop the Environment
To stop and remove the containers:

Using `make`:
```bash
make down
```

Using Docker Compose:
```bash
docker compose down
```

---

## Accessing the Application

1. Add a local host entry to your `/etc/hosts` file (or equivalent) to resolve `app1.localhost` to `127.0.0.1`:
   ```text
   127.0.0.1 app1.localhost
   ```
2. Open your browser or run a `curl` request:
   ```bash
   curl http://app1.localhost
   ```
   *Expected response:* `Hello World!`

---

## Configuration Details

### NGINX Reverse Proxy (`nginx/conf.d/app1.conf`)
The NGINX configuration uses Docker's embedded DNS resolver (`127.0.0.11`) with variable-based proxying (`set $upstream_app1 http://app1:3000;`). This ensures NGINX gracefully handles situations where the Node.js container restarts or starts up after NGINX.

### Node.js App (`www/app1/server.mjs`)
A minimal standalone ECMAScript module (`.mjs`) running a native Node.js HTTP server on port `3000`.