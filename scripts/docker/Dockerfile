# syntax=docker/dockerfile:1

FROM golang:1.25-alpine AS builder

WORKDIR /src

# Install build dependencies
RUN apk add --no-cache git

COPY go.mod go.sum ./
RUN go mod download

COPY . .

RUN CGO_ENABLED=0 GOOS=linux go build -ldflags="-s -w" -o /fabric ./cmd/fabric

FROM alpine:3.21.5

LABEL org.opencontainers.image.description="A Docker image for running the Fabric CLI. See https://github.com/danielmiessler/Fabric/tree/main/scripts/docker for details."

ENV HOME=/home/fabric \
    XDG_CONFIG_HOME=/home/fabric/.config \
    XDG_CACHE_HOME=/home/fabric/.cache

RUN apk upgrade --no-cache \
    && apk add --no-cache --upgrade ca-certificates yt-dlp \
    && addgroup -S fabric \
    && adduser -S -G fabric -h /home/fabric fabric \
    && mkdir -p /home/fabric/.config/fabric /home/fabric/.cache \
    && chown -R fabric:fabric /home/fabric

COPY --from=builder /fabric /usr/local/bin/fabric

EXPOSE 8080

USER fabric
WORKDIR /home/fabric

ENTRYPOINT ["fabric"]
