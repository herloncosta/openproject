# OpenProject — Docker Compose (fork de referência)

Este repositório é um fork de referência do [openproject-docker-compose](https://github.com/opf/openproject-docker-compose) (branch `stable/17`), mantido para fins de estudo, documentação e deploy da ferramenta de gerenciamento de projetos [OpenProject](https://www.openproject.org/).

> **Nota:** Este repositório contém apenas a configuração de deploy via Docker Compose. Não é o código-fonte do OpenProject.

---

## Estrutura

```
.
├── AGENTS.md               # Instruções para agentes de IA (opencode)
├── docs/
│   ├── docs.md             # Documentação completa (instalação, configuração, upgrade, backup, uninstall)
│   └── install.md          # Guia rápido de instalação (português)
├── openproject/            # Stack Docker Compose clonada do upstream
│   ├── .env                # Configuração de ambiente (com valores gerados)
│   ├── .env.example        # Exemplo de configuração
│   ├── docker-compose.yml  # Definição dos serviços
│   ├── docker-compose.control.yml
│   ├── proxy/              # Configuração do proxy Caddy
│   ├── control/            # Scripts de controle (backup, upgrade)
│   └── README.md           # README original do upstream
└── README.md               # Este arquivo
```

---

## .env compartilhado

O arquivo `openproject/.env` foi versionado intencionalmente neste repositório. A chave `SECRET_KEY_BASE` presente nele foi gerada aleatoriamente apenas para fins de ambiente local/desenvolvimento e **não representa risco de segurança**. Em ambientes de produção, gere uma nova chave com:

```bash
openssl rand -hex 64
```

e mantenha o `.env` fora do versionamento (conforme o `.gitignore` original recomenda).

---

## Uso rápido

```bash
cd openproject
cp .env.example .env
# Edite .env conforme necessário
SECRET_KEY_BASE=$(openssl rand -hex 64) OPENPROJECT_HTTPS=false docker compose up -d --build --pull always
```

Acesse em `http://localhost:8080` (usuário: `admin`, senha: `admin`).

---

## Documentação

- [docs/docs.md](docs/docs.md) — documentação completa em português
- [docs/install.md](docs/install.md) — guia rápido de instalação
- [openproject/README.md](openproject/README.md) — documentação original do upstream
- [Documentação oficial do OpenProject](https://www.openproject.org/docs/installation-and-operations/)
