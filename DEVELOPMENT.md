# Development Setup

This guide explains how to set up the AnineePlus API for development using Docker Compose.

## Prerequisites

- Docker and Docker Compose
- Bun runtime (for local development)
- Git (for submodule management)

## Quick Start

1. **Validate Environment**:
   ```bash
   bun run validate-dev
   ```
   This will check if all required tools are installed and the configuration is correct.

2. **Initialize Git Submodules** (if not already done):
   ```bash
   git submodule update --init --recursive
   ```

3. **Create Environment File**:
   ```bash
   cp example.env .env
   ```
   Edit `.env` as needed for your development environment.

4. **Start Development Environment**:
   ```bash
   # Build and start all services
   bun run docker:dev:build
   bun run docker:dev:up
   
   # Or use the shorthand
   bun run dev
   ```

## Available Services

The development environment includes:

- **API Gateway** - Port 3000 (HTTP/GraphQL)
- **User Service** - Port 50051 (gRPC)
- **Payment Service** - Port 50052 (gRPC)
- **PostgreSQL Database** - Port 5432
- **Redis Cache** - Port 6379

## Development Features

### Hot Reload

All application services are configured with volume mounts for automatic code reloading:

- Source code changes are reflected immediately
- No need to rebuild containers for code changes
- Node modules are cached in Docker volumes for faster startup

### Useful Commands

```bash
# Validate development environment
bun run validate-dev

# Start services in foreground (with logs)
bun run docker:dev:up

# Start services in background
bun run docker:dev

# View logs from all services
bun run docker:dev:logs

# Restart a specific service
docker compose -f docker-compose-dev.yaml restart api-gateway

# Stop all services
bun run docker:dev:down

# Rebuild and restart
bun run docker:dev:build && bun run docker:dev:up
```

### Health Checks

All services include health checks:
- API Gateway: HTTP endpoint `/healthz`
- Microservices: gRPC health probe
- Database: PostgreSQL ready check
- Cache: Redis ping

### Networking

Services communicate through Docker networks:
- `frontend`: External facing services (API Gateway)
- `backend`: Internal service communication

## Troubleshooting

### Submodules Not Initialized
If microservice directories are empty, initialize the submodules:
```bash
git submodule update --init --recursive
```

### Port Conflicts
If ports are already in use, modify the port mappings in `docker-compose-dev.yaml`.

### Permission Issues
Ensure Docker has permission to access the project directory and mount volumes.

### Container Build Issues
Clean rebuild all containers:
```bash
docker compose -f docker-compose-dev.yaml down
docker system prune -f
bun run docker:dev:build --no-cache
```

## File Structure

```
├── api-gateway/
│   ├── Dockerfile.dev          # Development Dockerfile
│   └── src/                    # Source code (mounted as volume)
├── microservices/
│   ├── user-service/
│   │   ├── Dockerfile.dev      # Development Dockerfile
│   │   └── src/                # Source code (mounted as volume)
│   └── payment-service/
│       ├── Dockerfile.dev      # Development Dockerfile
│       └── src/                # Source code (mounted as volume)
├── docker-compose-dev.yaml     # Development compose file
└── .env                        # Environment variables
```