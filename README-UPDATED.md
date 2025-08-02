# 🚀 Kanban Application - Production Ready

A modern, secure implementation of a Kanban Board built with Docker best practices. This application helps visualize and manage work using the proven Kanban methodology originally developed by Toyota.

## 🏗️ Architecture

The application consists of three containerized services:

- **PostgreSQL 15** - Secure database with health checks
- **Spring Boot** - Java backend API with security enhancements  
- **Angular** - Modern frontend with optimized builds

![Kanban Demo](./assets/kanban.gif)

---

## 🚀 Quick Start

### Prerequisites

- **Docker** (v20.10+) - [Install Guide](https://docs.docker.com/get-docker/)
- **Docker Compose** (v2.0+) - [Install Guide](https://docs.docker.com/compose/install/)

### Development Environment

```bash
# Clone and start the application
git clone <repository-url>
cd kanban-docker

# Start all services (detached mode)
docker-compose up -d

# View application logs
docker-compose logs -f

# Access the application
# Frontend: http://localhost:4200
# Backend API: http://localhost:8080
# Swagger UI: http://localhost:8080/swagger-ui.html

# Stop services
docker-compose down
```

### Production Environment

```bash
# Use production configuration
docker-compose -f docker-compose.prod.yml up -d

# Stop production services
docker-compose -f docker-compose.prod.yml down
```

---

## 🔧 Configuration

### Environment Variables

The application uses environment variables for configuration. See `.env` file:

```bash
# Database Configuration
POSTGRES_DB=kanban
POSTGRES_USER=kanban
POSTGRES_PASSWORD=kanban  # ⚠️ Change in production!

# Application Ports
POSTGRES_PORT=5432
BACKEND_PORT=8080
FRONTEND_PORT=4200

# Security
JWT_SECRET=your-secret-key  # ⚠️ Change in production!
```

### Production Security

For production deployment:

1. Change default passwords in `.env`
2. Use secrets management (Docker Secrets, K8s Secrets)
3. Enable HTTPS with reverse proxy
4. Configure firewall rules
5. Use private networks for inter-service communication

---

## 🔒 Security Features

✅ **Container Security**
- Non-root user containers
- Multi-stage builds (reduced attack surface)
- Updated base images with security patches
- `.dockerignore` files to exclude sensitive data

✅ **Network Security**
- Network isolation between services
- Database not exposed in production
- Health checks for service monitoring

✅ **Application Security**
- Environment-based configuration
- Secrets via environment variables
- Error message sanitization
- Actuator endpoints secured

---

## 📦 Service Details

### 🗄️ kanban-postgres (Database)

- **Image**: `postgres:15-alpine`
- **Internal Port**: 5432
- **Health Check**: `pg_isready` command
- **Volume**: Persistent data storage
- **Security**: Non-root user, updated packages

**Database Connection:**
- Host: `localhost` (development) / `kanban-postgres` (internal)
- Database: `kanban`
- User: `kanban`
- Password: `kanban` (⚠️ change in production)

### ⚙️ kanban-app (Backend API)

- **Image**: Custom Spring Boot application
- **Port**: 8080
- **Health Check**: `/actuator/health` endpoint
- **Security**: Non-root user, JVM optimizations

**API Endpoints:**
- REST API: `http://localhost:8080/api/`
- Swagger UI: `http://localhost:8080/swagger-ui.html`
- Health Check: `http://localhost:8080/actuator/health`

### 🎨 kanban-ui (Frontend)

- **Image**: Custom Angular application with Nginx
- **Port**: 80 (internal), 4200 (external)
- **Security**: Non-root nginx user
- **Optimization**: Production build, compressed assets

**Access:**
- Application: `http://localhost:4200`

---

## 🛠️ Development

### Building Images

```bash
# Build all images
docker-compose build

# Build specific service
docker-compose build kanban-app

# Build with no cache
docker-compose build --no-cache
```

### Debugging

```bash
# View logs for all services
docker-compose logs

# View logs for specific service
docker-compose logs kanban-app

# Follow logs in real-time
docker-compose logs -f

# Execute commands in running container
docker-compose exec kanban-app bash
```

### Database Management

```bash
# Access PostgreSQL CLI
docker-compose exec kanban-postgres psql -U kanban -d kanban

# Backup database
docker-compose exec kanban-postgres pg_dump -U kanban kanban > backup.sql

# Restore database
docker-compose exec -T kanban-postgres psql -U kanban -d kanban < backup.sql
```

---

## 🚀 Deployment

### Production Checklist

- [ ] Update default passwords
- [ ] Configure HTTPS/SSL
- [ ] Set up reverse proxy (Nginx/Traefik)
- [ ] Configure monitoring and logging
- [ ] Set up backup strategy
- [ ] Configure resource limits
- [ ] Enable container restart policies
- [ ] Set up health monitoring

### Docker Swarm / Kubernetes

The application is ready for orchestration platforms:

- Resource limits defined
- Health checks implemented
- Secrets management ready
- Network policies configured

---

## 📈 Monitoring

### Health Checks

All services include health checks:

```bash
# Check service health
docker-compose ps

# Manual health check
curl http://localhost:8080/actuator/health
curl http://localhost:4200
```

### Metrics

- **Backend**: Spring Boot Actuator metrics
- **Database**: PostgreSQL stats
- **Frontend**: Nginx access logs

---

## 🔄 Updates & Maintenance

### Updating Dependencies

```bash
# Update base images
docker-compose pull
docker-compose up -d

# Rebuild with latest packages
docker-compose build --no-cache
```

### Security Patches

Regular maintenance includes:
- Base image updates
- Dependency updates  
- Security scanning
- Log monitoring

---

## 📚 Additional Resources

- [Original Blog Post](https://medium.com/@wkrzywiec/how-to-run-database-backend-and-frontend-in-a-single-click-with-docker-compose-4bcda66f6de)
- [Docker Best Practices](https://docs.docker.com/develop/best-practices/)
- [Spring Boot Docker Guide](https://spring.io/guides/topicals/spring-boot-docker/)
- [Angular Docker Guide](https://angular.io/guide/deployment#docker)

---

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Make changes following security best practices
4. Test with both development and production configurations
5. Submit a pull request

---

## 📄 License

This project is licensed under the MIT License - see the LICENSE file for details.
