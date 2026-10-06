# CodeAlpha DevOps Internship — Task 4: Web Server using Docker

## Overview
A containerized **Nginx** web server, serving a custom static page, built with Docker best practices (minimal base image, health monitoring) and demonstrating the full container lifecycle.

## Stack
- **Web server**: Nginx (`nginx:alpine` — minimal base image, ~40MB)
- **Content**: Custom static HTML page (not the default Nginx placeholder)
- **Monitoring**: Docker `HEALTHCHECK` instruction

## Dockerfile
```dockerfile
FROM nginx:alpine
COPY site/ /usr/share/nginx/html/
EXPOSE 80
HEALTHCHECK --interval=30s --timeout=3s CMD wget -q --spider http://localhost/ || exit 1
```

- `nginx:alpine` — minimal, production-style base image.
- `COPY site/ ...` — drops the custom page into Nginx's default serving directory, no extra Nginx config needed.
- `HEALTHCHECK` — Docker runs this check every 30 seconds; a failing check marks the container `unhealthy`, the same mechanism orchestrators (Kubernetes, Swarm) use to auto-restart failing containers.

## Build and Run
```bash
docker build -t task4-webserver .
docker run -d -p 8081:80 --name task4-container task4-webserver
curl http://localhost:8081
```

## Lifecycle Commands Demonstrated
```bash
docker ps                          # view running container
docker stop task4-container         # stop
docker ps -a                        # confirm "Exited" state
docker start task4-container         # restart
docker logs task4-container          # view access/error logs
```

## Health Monitoring — Verified
```
CONTAINER ID   IMAGE             STATUS                    PORTS
306a52bb1306   task4-webserver   Up 17 minutes (healthy)   0.0.0.0:8081->80/tcp
```
The `(healthy)` status confirms Docker's automated health check is actively passing, not just that the container is "up."

## Key Concepts Demonstrated
- Docker image builds using a minimal, production-style base image
- Static content deployment via Nginx's default serving path
- Full container lifecycle management (build, run, stop, start, logs)
- Container health monitoring via `HEALTHCHECK`
- Port mapping (host vs. container ports)

## Part of
CodeAlpha DevOps Internship — one of three completed tasks (alongside an Azure CI/CD pipeline and a Jenkins master/remote-agent setup).
