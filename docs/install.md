# OpenProject com Docker Compose — Guia rápido

## 1. Pré-requisitos

- Docker e Docker Compose instalados
- `git` instalado

---

## 2. Clonar o repositório

```bash
git clone https://github.com/opf/openproject-docker-compose.git --depth=1 --branch=stable/17 openproject
cd openproject
```

---

## 3. Configurar ambiente

```bash
cp .env.example .env
```

Edite o `.env` com suas preferências. Pelo menos **gere um SECRET_KEY_BASE** único:

```bash
# Linux/Mac
echo "SECRET_KEY_BASE=$(openssl rand -hex 64)" >> .env
```

Se for usar o diretório padrão de dados (`OPDATA=/var/openproject/assets`):

```bash
sudo mkdir -p /var/openproject/assets
sudo chown 1000:1000 -R /var/openproject/assets
```

---

## 4. Iniciar os containers

```bash
SECRET_KEY_BASE=$(openssl rand -hex 64) OPENPROJECT_HTTPS=false docker compose up -d --build --pull always
```

> **`OPENPROJECT_HTTPS=false` é obrigatório na primeira execução** (a menos que você já tenha um proxy TLS na frente). Sem essa flag o OpenProject assume HTTPS e você terá erro `ERR_SSL_PROTOCOL_ERROR`.

---

## 5. Acessar

- URL: `http://localhost:8080`
- Usuário: `admin`
- Senha: `admin`

---

## 6. Configurações comuns

| O que fazer | Como |
|---|---|
| **Mudar porta** | `PORT=127.0.0.1:8080` no `.env` (localhost apenas) ou `PORT=4000` |
| **Desabilitar HTTPS** | `OPENPROJECT_HTTPS=false` no `.env` |
| **Usar imagem específica** | `TAG=my-tag` no `.env` |
| **Customizações persistentes** | Use `docker-compose.override.yml` — o `docker-compose.yml` é sobrescrito no `git pull` |
| **Collaboration server** | Defina `COLLABORATIVE_SERVER_SECRET` no `.env` (segredo padrão inseguro) |

---

## 7. Parar / Remover

```bash
# Parar sem remover dados
docker compose stop

# Remover containers (dados persistem em volumes)
docker compose down

# Remover TUDO (inclusive dados)
docker volume rm compose_opdata compose_pgdata
```

---

## 8. Atualizar (upgrade)

```bash
git pull origin stable/17
docker compose -f docker-compose.yml -f docker-compose.control.yml build
docker compose -f docker-compose.yml -f docker-compose.control.yml run backup
docker compose -f docker-compose.yml -f docker-compose.control.yml run upgrade
docker compose up -d --build --pull always
```

---

## 9. Dicas e problemas conhecidos

- **Aviso `openproject/proxy`**: ignore. Se usar `docker compose pull`, adicione `--ignore-buildable` para evitar exit code diferente de zero.
- **Logs**: `docker compose logs -n 1000`
- **SMTP "Network is unreachable"**: configure DNS no worker:
  ```yaml
  worker:
    dns:
      - "8.8.8.8"
  ```
- **Caddy reverse proxy**: o proxy integrado encaminha `X-Forwarded-*`. Configure `trusted_proxies` no Caddyfile para produção.
- **Redes**: problemas de timeout? Remova as redes `frontend` e `backend` separadas (pode ocorrer com Podman).
