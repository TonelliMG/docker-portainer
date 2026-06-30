# Docker Portainer Setup

Stack Docker para subir o **Portainer CE** (interface web de gerenciamento de
containers) de forma reproduzível, segura e 100% orientada por variáveis de
ambiente — funciona igual no Docker Desktop (Windows) e em um servidor Linux.

- **Portainer CE 2.39.4 LTS** (linha LTS, fixada via `.env`)
- Volume nomeado **persistente** (dados sobrevivem a reinício de container e do PC)
- Network bridge isolada
- Hardening básico: `no-new-privileges`, somente HTTPS, limite de memória,
  healthcheck e rotação de logs

> **Por que não há `Dockerfile` nem `.dockerignore`?**
> O Portainer é distribuído como **imagem oficial pronta** (`portainer/portainer-ce`).
> Não há código para compilar, então não existe build context — um `Dockerfile`
> seria apenas um `FROM` passthrough (anti-padrão) e o `.dockerignore` não teria
> efeito. O setup correto e otimizado para este projeto é **compose-only**.

---

## ✅ Pré-requisitos

- **Docker Desktop** (Windows) com o engine rodando, **ou** Docker Engine + Compose v2 (Linux).
- A porta definida em `PORTAINER_HTTPS_PORT` (padrão `9443`) livre no host.

## 🚀 Como rodar (Docker Desktop / Windows)

Abra o **PowerShell** na pasta do projeto:

```powershell
# 1. Criar o .env a partir do exemplo
Copy-Item .env.example .env

# 2. (opcional) Editar versões/portas/limites
notepad .env

# 3. Subir em background
docker compose up -d

# 4. Acompanhar a inicialização
docker compose logs -f
```

Acesse: **https://localhost:9443**

> O navegador vai avisar sobre certificado autoassinado — é esperado. Clique em
> "Avançado → continuar". No **primeiro acesso** crie o usuário admin (faça isso
> em até alguns minutos, senão o Portainer bloqueia o setup por segurança).

No Linux/servidor os comandos são idênticos, trocando o passo 1 por
`cp .env.example .env`.

## ⚙️ Variáveis de ambiente (`.env`)

| Variável                 | Padrão            | Descrição                                            |
| ------------------------ | ----------------- | ---------------------------------------------------- |
| `COMPOSE_PROJECT_NAME`   | `portainer_stack` | Prefixo de container, volume e network               |
| `PORTAINER_VERSION`      | `2.39.4`          | Tag da imagem Portainer CE (use a linha LTS)         |
| `PORTAINER_HTTPS_PORT`   | `9443`            | Porta HTTPS exposta no host                          |
| `PORTAINER_MEMORY_LIMIT` | `512M`            | Limite de memória do container                       |

## 💾 Persistência dos dados

Os dados ficam no volume nomeado **`<projeto>_data`** (montado em `/data`).
Volumes nomeados **persistem** a:

- reinício do container (`restart`, `docker compose restart`)
- reinício do PC / Docker Desktop
- `docker compose down` (derruba o container, **mantém** o volume)

⚠️ O volume **só** é apagado com `docker compose down -v` ou `docker volume rm`.
Evite a flag `-v` se quiser manter os dados.

### Backup / restore do volume

```powershell
# Backup -> gera backups/portainer_data.tar.gz
docker run --rm -v portainer_stack_data:/data -v "${PWD}/backups:/backup" `
  alpine tar czf /backup/portainer_data.tar.gz -C /data .

# Restore
docker run --rm -v portainer_stack_data:/data -v "${PWD}/backups:/backup" `
  alpine sh -c "rm -rf /data/* && tar xzf /backup/portainer_data.tar.gz -C /data"
```

(No Bash, troque a crase `` ` `` de quebra de linha por `\`.)

## 🔧 Operação

```powershell
docker compose ps              # status
docker compose logs -f         # logs
docker compose restart         # reiniciar
docker compose down            # parar e remover (mantém o volume/dados)
docker compose pull            # baixar nova versão após editar PORTAINER_VERSION
docker compose up -d           # aplicar a atualização
```

## ⬆️ Atualizar a versão

1. Edite `PORTAINER_VERSION` no `.env` (consulte os
   [releases LTS](https://github.com/portainer/portainer/releases)).
2. `docker compose pull && docker compose up -d`.

Os dados são preservados pelo volume.
