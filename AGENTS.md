# OpenProject Docker Compose — Agent Guide

This repo is **documentation only** — a reference for deploying OpenProject via Docker Compose. It is not the OpenProject source code.

## What's here

- `docs/docs.md` — the single source file covering installation, configuration, upgrade, backup, and uninstall.

## How OpenProject is deployed here

- The stack is cloned from `https://github.com/opf/openproject-docker-compose.git` (branch `stable/17`).
- Start: `cp .env.example .env`, then `SECRET_KEY_BASE=<key> OPENPROJECT_HTTPS=false docker compose up -d --build --pull always`
- Default access: `http://localhost:8080`, user `admin` / `admin`.
- Override via `.env` or `docker-compose.override.yml`. The root `docker-compose.yml` is overwritten on pull.

## Notable quirks

- `openproject/proxy` pull warning is harmless. Use `docker compose pull --ignore-buildable` to avoid non-zero exit.
- `OPENPROJECT_HTTPS=false` is required on first start unless behind a TLS proxy.
- Port binding defaults to `0.0.0.0` (public). Set `PORT=127.0.0.1:8080` for localhost-only.
- The integrated Caddy proxy uses `X-Forwarded-*` from a front proxy — configure `trusted_proxies` for production.
- Collaboration server is enabled by default; override `COLLABORATIVE_SERVER_SECRET`.
- Upgrade: `git pull origin stable/17` → build control plane → backup → upgrade → `docker compose up -d`.
- Data lives in named volumes `compose_opdata` and `compose_pgdata`; removing them wipes all state.

## Branch strategy

- Production deployments track `stable/17`.
- `git pull origin stable/17` for upgrades.
