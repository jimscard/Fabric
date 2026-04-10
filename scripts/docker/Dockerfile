# syntax=docker/dockerfile:1

FROM golang:1.23-alpine3.21 AS builder

WORKDIR /src

# Install build dependencies
RUN apk add --no-cache git

COPY go.mod go.sum ./
RUN go mod download

COPY . .

RUN CGO_ENABLED=0 GOOS=linux go build -ldflags="-s -w" -o /fabric ./cmd/fabric

FROM alpine:3.21

LABEL org.opencontainers.image.description="A Docker image for running the Fabric CLI. See https://github.com/danielmiessler/Fabric/tree/main/scripts/docker for details."

RUN apk add --no-cache ca-certificates yt-dlp \
    && mkdir -p /root/.config/fabric \
    && apk upgrade --no-cache

COPY --from=builder /fabric /usr/local/bin/fabric

EXPOSE 8080

# Run as non-root user
RUN adduser -D -u 1001 fabric
USER fabric

ENTRYPOINT ["fabric"]
