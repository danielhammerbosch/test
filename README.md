# Docker Compose Setup Guide

This guide shows you how to set up Traefik reverse proxy with Docker Compose.

## Prerequisites

Install the following before starting:
- Docker (version 20.10 or higher)
- Docker Compose (version 2.0 or higher)

## Quick Start

### 1. Clone the Repository

```bash
git clone <repository-url>
cd <repository-name>
```

### 2. Configure Environment Variables

Copy the example environment file:

```bash
cp .env.example .env
```

The default credentials are `admin` / `admin` for local development.

### 3. Generate Custom Dashboard Credentials (Recommended)

For production or enhanced security, generate your own password:

```bash
htpasswd -nb admin yourpassword
```

Copy the output and replace the value of `TRAEFIK_DASHBOARD_USERS` in the `.env` file.

**Note**: When pasting in `.env`, escape dollar signs by doubling them (`$` becomes `$$`).

### 4. Start the Services

Launch all containers:

```bash
docker compose up -d
```

This starts three services:
- **docker-socket-proxy**: Secures Docker API access
- **traefik**: Reverse proxy and load balancer
- **whoami**: Test service

### 5. Verify Services are Running

Check the status:

```bash
docker compose ps
```

All services should show `Up` status.

## Access the Services

### Traefik Dashboard

Open your browser and navigate to:

```
http://traefik.docker.localhost:8080
```

Login with your credentials (default: `admin` / `admin`).

The dashboard shows:
- Active routers and services
- Middleware configurations
- Health status

### Test Service (Whoami)

Access the test service at:

```
http://whoami.docker.localhost
```

This displays HTTP request information.

## Managing Services

### View Logs

View logs for all services:

```bash
docker compose logs -f
```

View logs for a specific service:

```bash
docker compose logs -f traefik
```

### Stop Services

Stop all containers:

```bash
docker compose down
```

### Restart Services

Restart after configuration changes:

```bash
docker compose restart
```

## Troubleshooting

### Cannot Access Services

**Issue**: `whoami.docker.localhost` or `traefik.docker.localhost` don't work.

**Solution**: Add entries to your hosts file.

On Linux/Mac, edit `/etc/hosts`:

```
127.0.0.1 whoami.docker.localhost
127.0.0.1 traefik.docker.localhost
```

On Windows, edit `C:\Windows\System32\drivers\etc\hosts` with administrator privileges.

### Dashboard Shows 401 Unauthorized

**Issue**: Cannot log into Traefik dashboard.

**Solution**: Verify credentials in `.env` file.
- Check `TRAEFIK_DASHBOARD_USERS` is set correctly
- Ensure dollar signs are escaped (`$$`)
- Restart Traefik: `docker compose restart traefik`

### Services Won't Start

**Issue**: Containers fail to start.

**Solution**: Check for port conflicts.

```bash
docker compose logs
```

Ensure ports 80 and 8080 are not in use by other applications.

## Security Notes

### Docker Socket Proxy

This setup uses Docker Socket Proxy for security.
- Restricts Traefik's access to Docker API
- Prevents root-level host access
- Only exposes necessary endpoints

### Production Deployment

For production environments:
1. Generate strong dashboard credentials
2. Use HTTPS with valid SSL certificates
3. Implement additional middleware (rate limiting, IP whitelist)
4. Regularly update Docker images

## Adding New Services

To add a service behind Traefik:

1. Add the service to `docker-compose.yml`
2. Connect it to the `proxy` network
3. Add Traefik labels for routing

Example:

```yaml
my-service:
  image: my-app:latest
  networks:
    - proxy
  labels:
    - "traefik.enable=true"
    - "traefik.http.routers.myapp.rule=Host(`myapp.docker.localhost`)"
    - "traefik.http.routers.myapp.entrypoints=web"
```

## Additional Resources

- [Traefik Documentation](https://doc.traefik.io/traefik/)
- [Docker Compose Documentation](https://docs.docker.com/compose/)
- [Docker Socket Proxy](https://github.com/Tecnativa/docker-socket-proxy)
