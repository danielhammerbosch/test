# Repository Overview

This is a Docker Compose configuration repository for setting up Traefik reverse proxy with enhanced security using Docker Socket Proxy.

## Technology Stack

- **Docker Compose**: Container orchestration
- **Traefik v3.4**: Reverse proxy and load balancer
- **Docker Socket Proxy**: Secures Docker API access
- **Whoami**: Test service for verifying Traefik routing

## Repository Structure

- `docker-compose.yml`: Main Docker Compose configuration with three services (docker-socket-proxy, traefik, whoami)
- `.env.example`: Template for environment variables (primarily Traefik dashboard credentials)
- `README.md`: Comprehensive user guide with setup, troubleshooting, and usage instructions

## Key Concepts

### Security Architecture
- Docker Socket Proxy restricts Traefik's access to Docker API
- Only necessary endpoints (CONTAINERS, NETWORKS, SERVICES, TASKS) are exposed
- Traefik connects via TCP (socket proxy) instead of direct socket mount
- Basic authentication required for Traefik dashboard

### Network Configuration
- All services use the `proxy` network
- Port 80: HTTP entry point
- Port 8080: Traefik dashboard

## Development Guidelines

### Environment Configuration
- Always use `.env.example` as template for new `.env` files
- Escape dollar signs in passwords by doubling them (`$` becomes `$$`)
- Default credentials are for local development only

### Docker Compose
- Use `docker compose` (v2 syntax) not `docker-compose`
- Services should use `restart: unless-stopped` for reliability
- Apply `no-new-privileges:true` security option to all services

### Adding New Services
When adding services behind Traefik:
1. Connect to the `proxy` network
2. Add `traefik.enable=true` label
3. Configure router rule with `Host()` matcher
4. Specify appropriate entrypoint (web for HTTP)

### Documentation
- Keep README.md updated with any configuration changes
- Include troubleshooting steps for common issues
- Provide examples for adding new services

## Testing

### Manual Testing
- Verify services start: `docker compose ps`
- Check logs: `docker compose logs -f`
- Test Traefik dashboard: `http://traefik.docker.localhost:8080`
- Test whoami service: `http://whoami.docker.localhost`

### Validation Checklist
- [ ] All services show "Up" status
- [ ] Dashboard accessible with credentials
- [ ] Whoami service responds
- [ ] No port conflicts (80, 8080)

## Code Review Standards

- Respond in English and Japanese when performing code reviews
- Focus on readability and avoid nested ternary operators
- Ensure YAML syntax is correct and indentation is consistent
- Verify security configurations are maintained
- Check that documentation is updated alongside code changes

## Common Pitfalls

- Forgetting to escape `$` in `.env` file passwords
- Using outdated `docker-compose` command instead of `docker compose`
- Exposing Docker socket directly to Traefik (bypassing socket proxy)
- Not specifying explicit network for new services
- Hardcoding credentials in `docker-compose.yml`
