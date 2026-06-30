# CLAUDE.md — Docker Portainer

Project-specific instructions. Inherits the global CLAUDE.md; on conflict, this file wins.
Document only the **non-obvious** — facts the model cannot infer by reading the repo.

## 1. What this is

Deploy reproduzível e seguro do **Portainer CE** (UI de gerenciamento de Docker)
via Docker Compose. Não é uma aplicação com código próprio — é a orquestração de
uma **imagem oficial pronta**, parametrizada por `.env`, rodando igual no Docker
Desktop (Windows) e em servidor Linux.

## 2. Stack & versões

- **Docker Compose v2** (schema sem `version:`, já é o default atual).
- **Portainer CE 2.39.4 LTS** — fixado em `PORTAINER_VERSION` no `.env`. Use sempre
  a linha **LTS** ([releases](https://github.com/portainer/portainer/releases)); a
  linha edge (ex.: 2.43.x) existe mas não é o alvo deste projeto.
- Sem linguagem/runtime de app, sem build step, sem gerenciador de pacotes.

## 3. Commands / workflow

- Subir: `docker compose up -d`. Logs: `docker compose logs -f`. Status: `docker compose ps`.
- Parar mantendo dados: `docker compose down`. Atualizar: editar `PORTAINER_VERSION`
  no `.env` → `docker compose pull && docker compose up -d`.
- Local precisa de `.env` (gitignored) — copie de `.env.example`
  (`Copy-Item .env.example .env` no PowerShell).
- Acesso: `https://localhost:${PORTAINER_HTTPS_PORT}` (padrão 9443), certificado
  autoassinado.

## 4. Architecture / layout

Repositório de configuração, sem `src/`:

- **[docker-compose.yml](docker-compose.yml)** — única peça de orquestração: serviço
  `portainer`, volume nomeado `*_data`, network bridge `*_network`. Todos os valores
  variáveis vêm do `.env` com fallback inline (`${VAR:-default}`).
- **[.env.example](.env.example)** — fonte de verdade das variáveis; `.env` é a cópia local.
- **[README.md](README.md)** — instruções de uso, backup/restore e atualização.

## 5. Conventions

- **Tudo orientado por `.env`** — nada de hardcode de porta/versão/limite no compose;
  adicionar config nova = nova variável em `.env.example` + `${VAR:-default}` no compose.
- Recursos nomeados com prefixo `${COMPOSE_PROJECT_NAME}` (container, volume, network).
- Comentários em PT-BR.

## 6. Gotchas

- **Não há `Dockerfile` nem `.dockerignore` — e isso é intencional.** Imagem oficial
  pronta, sem build context. Não crie um `Dockerfile` passthrough.
- **Persistência**: dados ficam no volume nomeado `${COMPOSE_PROJECT_NAME}_data`
  (`/data`). Sobrevive a restart de container e do host. **`docker compose down -v`
  apaga os dados** — nunca use `-v` em manutenção rotineira.
- `/var/run/docker.sock` é montado para o Portainer gerenciar o host; funciona no
  Docker Desktop Windows. É um privilégio sensível — não exponha o serviço fora da rede local.
- Só a porta **HTTPS (9443)** é exposta; a HTTP (9000) fica fora de propósito.
- No **primeiro acesso** o admin precisa ser criado em poucos minutos, senão o
  Portainer trava o setup por segurança (reinicie o container para reabrir a janela).

## 7. Scope / do-not-touch

- Não commitar `.env` nem o conteúdo de `backups/` (ambos no `.gitignore`).
- Não introduzir build/Dockerfile sem necessidade real (ver §6).

## 8. Project skills

Nenhuma skill específica deste projeto. Use as globais apenas quando a tarefa
realmente pedir.

## 9. Important

Completely ignore anything related to .agents or AGENTS.md, since you already use .claude and CLAUDE.md.
