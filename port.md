# Port Usage Report - Plane Project

This document provides a comprehensive overview of all port usage throughout the Plane project management application.

## Table of Contents
- [Overview](#overview)
- [Port 6379 - Redis](#port-6379---redis)
- [Port 5432 - PostgreSQL](#port-5432---postgresql)
- [Port 9000 - MinIO API](#port-9000---minio-api)
- [Port 9090 - MinIO Console](#port-9090---minio-console)
- [Port 8000 - API Server](#port-8000---api-server)
- [Port 3000 - Web Application](#port-3000---web-application)
- [Port 3001 - Admin Interface](#port-3001---admin-interface)
- [Port 3002 - Space Application](#port-3002---space-application)
- [Port 3100 - Live Server](#port-3100---live-server)
- [Port Configuration Summary](#port-configuration-summary)

## Overview

Plane uses a microservices architecture with the following port allocation:

| Service | Port | Description |
|---------|------|-------------|
| Redis | 6379 | Cache and session storage |
| PostgreSQL | 5432 | Primary database |
| MinIO API | 9000 | Object storage API |
| MinIO Console | 9090 | MinIO web interface |
| API Server | 8000 | Backend REST API |
| Web App | 3000 | Main frontend application |
| Admin | 3001 | Administration interface (God-mode) |
| Space | 3002 | Public project spaces |
| Live | 3100 | Real-time collaboration server |

---

## Port 6379 - Redis

**Service**: Redis (Cache and Session Storage)  
**Container**: `plane-redis`  
**External Access**: `localhost:6379`

### Configuration Files
| File | Line | Content |
|------|------|---------|
| `docker-compose-local.yml` | 10 | `"6379:6379"` |
| `.env` | 9 | `REDIS_PORT="6379"` |
| `.env.example` | 9 | `REDIS_PORT="6379"` |
| `apps/api/.env` | 16 | `REDIS_PORT="6379"` |
| `apps/api/.env` | 17 | `REDIS_URL="redis://${REDIS_HOST}:6379/"` |
| `apps/live/.env.example` | 12 | `REDIS_PORT=6379` |
| `apps/live/.env.example` | 14 | `REDIS_URL="redis://localhost:6379/"` |

### Deployment Files
| File | Line | Content |
|------|------|---------|
| `deployments/cli/community/docker-compose.yml` | 12 | `REDIS_PORT: ${REDIS_PORT:-6379}` |
| `deployments/cli/community/docker-compose.yml` | 13 | `REDIS_URL: ${REDIS_URL:-redis://plane-redis:6379/}` |
| `deployments/cli/community/variables.env` | 33 | `REDIS_PORT=6379` |

### Documentation
| File | Line | Content |
|------|------|---------|
| `deployments/aio/community/README.md` | 73 | `REDIS_URL=redis://${MYIP}:16379` |

---

## Port 5432 - PostgreSQL

**Service**: PostgreSQL (Primary Database)  
**Container**: `plane-db`  
**External Access**: `localhost:5432`

### Configuration Files
| File | Line | Content |
|------|------|---------|
| `docker-compose-local.yml` | 64 | `"5432:5432"` |
| `apps/api/.env` | 11 | `POSTGRES_PORT=5432` |

### Backend Code
| File | Line | Content |
|------|------|---------|
| `apps/api/plane/settings/common.py` | 148 | `"PORT": os.environ.get("POSTGRES_PORT", "5432")` |
| `apps/api/plane/settings/common.py` | 166 | `"PORT": os.environ.get("POSTGRES_READ_REPLICA_PORT", "5432")` |

### Deployment Files
| File | Line | Content |
|------|------|---------|
| `deployments/cli/community/docker-compose.yml` | 7 | `POSTGRES_PORT: ${POSTGRES_PORT:-5432}` |
| `deployments/cli/community/variables.env` | 27 | `POSTGRES_PORT=5432` |

### Documentation
| File | Line | Content |
|------|------|---------|
| `deployments/aio/community/README.md` | 72 | `DATABASE_URL=postgresql://plane:plane@${MYIP}:15432/plane` |

---

## Port 9000 - MinIO API

**Service**: MinIO Object Storage API  
**Container**: `plane-minio`  
**External Access**: `localhost:9000`

### Configuration Files
| File | Line | Content |
|------|------|---------|
| `docker-compose-local.yml` | 36 | `mc alias set myminio http://localhost:9000` |
| `docker-compose-local.yml` | 48 | `"9000:9000"` |
| `.env` | 25 | `AWS_S3_ENDPOINT_URL="http://plane-minio:9000"` |
| `.env.example` | 25 | `AWS_S3_ENDPOINT_URL="http://plane-minio:9000"` |
| `apps/api/.env` | 30 | `AWS_S3_ENDPOINT_URL="http://plane-minio:9000"` |
| `apps/api/.env.example` | 30 | `AWS_S3_ENDPOINT_URL="http://localhost:9000"` |

### Proxy Configuration
| File | Line | Content |
|------|------|---------|
| `apps/proxy/Caddyfile.ce` | 16 | `reverse_proxy /{$BUCKET_NAME}/* plane-minio:9000` |

### Deployment Files
| File | Line | Content |
|------|------|---------|
| `deployments/cli/community/docker-compose.yml` | 23 | `AWS_S3_ENDPOINT_URL: ${AWS_S3_ENDPOINT_URL:-http://plane-minio:9000}` |
| `deployments/cli/community/variables.env` | 65 | `AWS_S3_ENDPOINT_URL=http://plane-minio:9000` |

### Build Scripts
| File | Line | Content |
|------|------|---------|
| `deployments/aio/community/build.sh` | 111-112 | Scripts handling `plane-minio:9000` |

### Documentation
| File | Line | Content |
|------|------|---------|
| `deployments/aio/community/README.md` | 79 | `AWS_S3_ENDPOINT_URL=http://${MYIP}:19000` |

---

## Port 9090 - MinIO Console

**Service**: MinIO Web Console  
**Container**: `plane-minio`  
**External Access**: `localhost:9090`

### Docker Configuration
| File | Line | Content |
|------|------|---------|
| `docker-compose-local.yml` | 34 | `minio server /export --console-address ':9090'` |
| `docker-compose-local.yml` | 49 | `"9090:9090"` |
| `docker-compose.yml` | 147 | `command: server /export --console-address ":9090"` |
| `deployments/cli/community/docker-compose.yml` | 206 | `command: server /export --console-address ":9090"` |

---

## Port 8000 - API Server

**Service**: Django REST API Backend  
**Container**: `api`  
**External Access**: `localhost:8000`

### Configuration Files
| File | Line | Content |
|------|------|---------|
| `docker-compose-local.yml` | 142 | `"8000:8000"` |
| `apps/web/.env.example` | 1 | `NEXT_PUBLIC_API_BASE_URL="http://localhost:8000"` |
| `apps/space/.env.example` | 1 | `NEXT_PUBLIC_API_BASE_URL="http://localhost:8000"` |
| `apps/admin/.env.example` | 1 | `NEXT_PUBLIC_API_BASE_URL="http://localhost:8000"` |
| `apps/live/.env.example` | 2 | `API_BASE_URL="http://localhost:8000"` |
| `apps/api/.env` | 45 | `WEB_URL="http://localhost:8000"` |
| `apps/api/.env.example` | 45 | `WEB_URL="http://localhost:8000"` |

### Docker Configuration
| File | Line | Content |
|------|------|---------|
| `apps/api/Dockerfile.dev` | 43 | `EXPOSE 8000` |
| `apps/api/Dockerfile.api` | 56 | `EXPOSE 8000` |

### Server Scripts
| File | Line | Content |
|------|------|---------|
| `apps/api/bin/docker-entrypoint-api.sh` | 35 | `--bind 0.0.0.0:"${PORT:-8000}"` |
| `apps/api/bin/docker-entrypoint-api-local.sh` | 34 | `python manage.py runserver 0.0.0.0:8000` |

### Proxy Configuration
| File | Line | Content |
|------|------|---------|
| `apps/proxy/Caddyfile.ce` | 12 | `reverse_proxy /api/* api:8000` |
| `apps/proxy/Caddyfile.ce` | 14 | `reverse_proxy /auth/* api:8000` |

### Backend Configuration
| File | Line | Content |
|------|------|---------|
| `apps/api/plane/settings/openapi.py` | 43 | `{"url": "http://localhost:8000", "description": "Local"}` |

### Test Files
| File | Line | Content |
|------|------|---------|
| `apps/api/plane/tests/contract/app/test_authentication.py` | 30 | `"domain": "http://localhost:8000"` |
| `apps/api/plane/tests/unit/utils/test_url.py` | 18 | `assert contains_url("http://localhost:8000")` |
| `apps/api/plane/tests/unit/utils/test_url.py` | 174 | `assert is_valid_url("http://localhost:8000")` |

### Deployment Files
| File | Line | Content |
|------|------|---------|
| `deployments/cli/community/docker-compose.yml` | 47 | `API_BASE_URL: ${API_BASE_URL:-http://api:8000}` |
| `deployments/cli/community/variables.env` | 19 | `API_BASE_URL=http://api:8000` |

### Build Scripts
| File | Line | Content |
|------|------|---------|
| `deployments/aio/community/build.sh` | 118 | `string_replace $DIST_DIR/Caddyfile "api:8000" "localhost:3004"` |
| `deployments/cli/community/install.sh` | 381 | Health check script using port 8000 |

---

## Port 3000 - Web Application

**Service**: Main Frontend Application (Next.js)  
**Container**: `web`  
**External Access**: `localhost:3000`

### Configuration Files
| File | Line | Content |
|------|------|---------|
| `apps/api/.env` | 4 | `CORS_ALLOWED_ORIGINS="http://localhost:3000,..."` |
| `apps/api/.env` | 57 | `APP_BASE_URL="http://localhost:3000"` |
| `apps/web/.env` | 3 | `NEXT_PUBLIC_WEB_BASE_URL="http://localhost:3000"` |

### Package Configuration
| File | Line | Content |
|------|------|---------|
| `apps/web/package.json` | 7 | `"dev": "next dev --port 3000"` |

### Docker Configuration
| File | Line | Content |
|------|------|---------|
| `apps/web/Dockerfile.dev` | 11 | `EXPOSE 3000` |
| `apps/web/Dockerfile.web` | 118 | `EXPOSE 3000` |

### Proxy Configuration
| File | Line | Content |
|------|------|---------|
| `apps/proxy/Caddyfile.ce` | 18 | `reverse_proxy /* web:3000` |

### Documentation
| File | Line | Content |
|------|------|---------|
| `CONTRIBUTING.md` | 80 | `Open up your browser to http://localhost:3000` |

---

## Port 3001 - Admin Interface

**Service**: Admin Interface (God-mode)  
**Container**: `admin`  
**External Access**: `localhost:3001`

### Configuration Files
| File | Line | Content |
|------|------|---------|
| `apps/api/.env` | 4 | `CORS_ALLOWED_ORIGINS="...,http://localhost:3001,..."` |
| `apps/api/.env` | 51 | `ADMIN_BASE_URL="http://localhost:3001"` |
| `apps/web/.env` | 5 | `NEXT_PUBLIC_ADMIN_BASE_URL="http://localhost:3001"` |
| `apps/admin/.env.example` | 5 | `NEXT_PUBLIC_ADMIN_BASE_URL="http://localhost:3001"` |

### Package Configuration
| File | Line | Content |
|------|------|---------|
| `apps/admin/package.json` | 8 | `"dev": "next dev --port 3001"` |

### Docker Configuration
| File | Line | Content |
|------|------|---------|
| `apps/admin/Dockerfile.dev` | 13 | `EXPOSE 3000` |
| `apps/admin/Dockerfile.admin` | 101 | `EXPOSE 3000` |

### Proxy Configuration
| File | Line | Content |
|------|------|---------|
| `apps/proxy/Caddyfile.ce` | 8 | `reverse_proxy /god-mode/* admin:3000` |

### AIO Community Setup
| File | Line | Content |
|------|------|---------|
| `deployments/aio/community/supervisor.conf` | 29 | `environment=PORT=3001,HOSTNAME=0.0.0.0` |

### Documentation
| File | Line | Content |
|------|------|---------|
| `deployments/aio/community/README.md` | 9 | Documentation about port 3001 |
| `CONTRIBUTING.md` | 79 | `Open your browser to http://localhost:3001/god-mode/` |

---

## Port 3002 - Space Application

**Service**: Public Project Spaces  
**Container**: `space`  
**External Access**: `localhost:3002`

### Configuration Files
| File | Line | Content |
|------|------|---------|
| `apps/api/.env` | 4 | `CORS_ALLOWED_ORIGINS="...,http://localhost:3002,..."` |
| `apps/api/.env` | 54 | `SPACE_BASE_URL="http://localhost:3002"` |
| `apps/web/.env` | 8 | `NEXT_PUBLIC_SPACE_BASE_URL="http://localhost:3002"` |
| `apps/space/.env.example` | 8 | `NEXT_PUBLIC_SPACE_BASE_URL="http://localhost:3002"` |

### Package Configuration
| File | Line | Content |
|------|------|---------|
| `apps/space/package.json` | 7 | `"dev": "next dev -p 3002"` |

### Docker Configuration
| File | Line | Content |
|------|------|---------|
| `apps/space/Dockerfile.dev` | 13 | `EXPOSE 3002` |
| `apps/space/Dockerfile.space` | 101 | `EXPOSE 3000` |

### Proxy Configuration
| File | Line | Content |
|------|------|---------|
| `apps/proxy/Caddyfile.ce` | 6 | `reverse_proxy /spaces/* space:3000` |

### AIO Community Setup
| File | Line | Content |
|------|------|---------|
| `deployments/aio/community/supervisor.conf` | 41 | `environment=PORT=3002,HOSTNAME=0.0.0.0` |

### Documentation
| File | Line | Content |
|------|------|---------|
| `deployments/aio/community/README.md` | 10 | Documentation about port 3002 |

### Build Scripts
| File | Line | Content |
|------|------|---------|
| `deployments/aio/community/build.sh` | 116 | Port replacement configuration |

---

## Port 3100 - Live Server

**Service**: Real-time Collaboration Server  
**Container**: `live`  
**External Access**: `localhost:3100`

### Configuration Files
| File | Line | Content |
|------|------|---------|
| `apps/api/.env` | 4 | `CORS_ALLOWED_ORIGINS="...,http://localhost:3100"` |
| `apps/api/.env` | 60 | `LIVE_BASE_URL="http://localhost:3100"` |
| `apps/web/.env` | 11 | `NEXT_PUBLIC_LIVE_BASE_URL="http://localhost:3100"` |

### .env.example Files
| File | Line | Content |
|------|------|---------|
| `apps/admin/.env.example` | 11 | `NEXT_PUBLIC_LIVE_BASE_URL="http://localhost:3100"` |
| `apps/space/.env.example` | 11 | `NEXT_PUBLIC_LIVE_BASE_URL="http://localhost:3100"` |
| `apps/live/.env.example` | 6 | `LIVE_BASE_URL="http://localhost:3100"` |

### Server Configuration
| File | Line | Content |
|------|------|---------|
| `apps/live/src/server.ts` | 24 | `this.app.set("port", process.env.PORT || 3000)` |

### Docker Configuration
| File | Line | Content |
|------|------|---------|
| `apps/live/Dockerfile.live` | 62 | `EXPOSE 3000` |

### Proxy Configuration
| File | Line | Content |
|------|------|---------|
| `apps/proxy/Caddyfile.ce` | 10 | `reverse_proxy /live/* live:3000` |

---

## Port Configuration Summary

### Critical Configuration Files

When changing ports, these files **MUST** be updated:

#### Backend Services
1. **docker-compose-local.yml** - All service port mappings
2. **.env / .env.example** - Root environment configuration
3. **apps/api/.env / .env.example** - API service configuration
4. **apps/api/plane/settings/common.py** - Django database settings
5. **apps/proxy/Caddyfile.ce** - Reverse proxy routing

#### Frontend Services
1. **apps/*/package.json** - Development server ports
2. **apps/*/.env.example** - Frontend environment configuration
3. **apps/api/.env** - CORS_ALLOWED_ORIGINS configuration

#### Deployment and Build
1. **deployments/cli/community/docker-compose.yml** - Community deployment
2. **deployments/cli/community/variables.env** - Community environment variables
3. **deployments/aio/community/supervisor.conf** - AIO deployment configuration
4. **deployments/aio/community/build.sh** - Build scripts

#### Test Files
1. **apps/api/plane/tests/*** - Various test files with hardcoded URLs
2. **apps/api/plane/settings/openapi.py** - API documentation

### Port Dependencies

- **Redis (6379)**: Used by API, Live server
- **PostgreSQL (5432)**: Used by API server, migrations, workers
- **MinIO (9000/9090)**: Used by API for file storage
- **API (8000)**: Used by all frontend applications
- **Frontend ports (3000-3100)**: Cross-referenced in CORS settings and inter-app communication

### Development vs Production

- **Development**: Uses localhost URLs for inter-service communication
- **Production**: Uses Docker service names (e.g., `plane-redis:6379` instead of `localhost:6379`)
- **AIO Community**: Uses different port mappings for single-container deployment

---

## Port Change Checklist

When modifying any port, ensure all related files are updated:

- [ ] Docker compose files
- [ ] Environment configuration files
- [ ] Frontend package.json scripts
- [ ] Proxy/routing configuration
- [ ] Test files
- [ ] Documentation
- [ ] Build and deployment scripts
- [ ] Cross-service URL references

**⚠️ Warning**: Changing ports requires coordinated updates across multiple files to maintain system functionality.

---

*Generated on: $(date)*  
*Last updated: Manual review required when ports are modified*