# Deploying

---

{% note info Go Implementation %}

This documentation has been updated to reflect the Go implementation of Moleculer. All examples are now in Go and show the actual API available in the Go version.
{% endnote %}

## Dockerfile to run Moleculer services

```docker
FROM golang:1.21-alpine AS builder

WORKDIR /app
COPY go.mod go.sum ./
RUN go mod download

COPY . .
RUN go build -o moleculer-service .

FROM alpine:latest
RUN apk --no-cache add ca-certificates
WORKDIR /root/

COPY --from=builder /app/moleculer-service .

CMD ["./moleculer-service"]
```

## Docker Compose file to run Moleculer services with NATS & Traefik (load balancing the API Gateway)

```yaml
version: "3.0"

services:
    api:
        build:
            context: .
        image: moleculer-demo
        env_file: docker-compose.env
        environment:
            SERVICES: api
            PORT: 3000
        links:
            - nats
        depends_on:
            - nats
        labels:
            - "traefik.enable=true"
            - "traefik.backend=api"
            - "traefik.port=3000"
            - "traefik.frontend.entryPoints=http"
            - "traefik.frontend.rule=PathPrefix:/"

    greeter:
        build:
            context: .
        image: moleculer-demo
        env_file: docker-compose.env
        environment:
            SERVICES: greeter
        links:
            - nats
        depends_on:
            - nats

    nats:
        image: nats

    traefik:
        image: traefik
        command: --web --docker --docker.domain=docker.localhost --logLevel=INFO --docker.exposedbydefault=false
        ports:
            - "3000:80"
            - "3001:8080"
        volumes:
            - /var/run/docker.sock:/var/run/docker.sock
            - /dev/null:/traefik.toml
```

**Start containers**

```bash
$ docker-compose up -d
```

Access your app on `http://<docker-host>:3000/`. Traefik dashboard UI on `http://<docker-host>:3001/`
