# Portainer com Docker Compose

## Overview

Este repositório define uma stack Docker Compose para executar o Portainer
Community Edition (CE). Não há código de aplicação, gerenciador de pacotes ou
processo de build.

## Stack

- Docker Compose.
- Imagem `portainer/portainer-ce`, com versão padrão `2.39.2`.
- Docker Desktop e acesso a `/var/run/docker.sock` são necessários no ambiente local.

## Commands

Configuração local:

```powershell
Copy-Item .env.example .env
```

Execução local:

```powershell
docker compose up -d
```

Encerramento da stack:

```powershell
docker compose down
```

Não há comandos de lint, type check, testes ou build definidos.

## Architecture

```text
.
├── docker-compose.yml  # Serviço, rede, volume, healthcheck e logs
├── .env.example        # Variáveis de configuração da stack
├── README.md           # Uso local documentado
└── .gitignore          # Exclusões de configuração, logs e backups locais
```

- `docker-compose.yml` define apenas o serviço `portainer`.
- `portainer_data` preserva dados em `/data`; `portainer_network` é uma rede bridge nomeada.
- O container recebe o socket Docker do host para administrar o ambiente Docker.

## Conventions

- Parametrize nomes, imagem, porta HTTPS e limite de memória pelas variáveis já usadas no Compose.
- Mantenha o acesso somente por HTTPS; a porta HTTP `9000` não deve ser exposta.
- Preserve `no-new-privileges`, o healthcheck e a rotação de logs ao alterar o serviço.
- Não versione `.env`; use `.env.example` como modelo de configuração.
- Backups locais pertencem a `backups/`, diretório ignorado pelo Git.

## Validation

- Não há validações automatizadas, lint, type check, testes ou build configurados.
- Para alterações na stack, revise `docker-compose.yml` e a correspondência entre as variáveis usadas nele e `.env.example`.
- Não há indicação de validações caras ou dependentes de serviços externos.

## Deployment

- Não há deploy, infraestrutura remota, CI/CD ou ambientes de homologação documentados.
- A única execução documentada é a stack local iniciada por Docker Compose.

## Project Notes

- O Portainer expõe HTTPS pela porta configurável do host e usa a porta `9443` no container.
- Alterar `COMPOSE_PROJECT_NAME` muda os nomes do container, volume e rede.
- O socket Docker montado concede ao Portainer controle sobre o Docker do host.
- Graphify data is available at `graphify-out/graph.json`.
- Prefer Graphify for architecture, dependency and impact questions.
- Validate important Graphify conclusions against the source code.
