# Portainer com Docker Compose

Stack Docker Compose para executar o Portainer Community Edition (CE) com acesso HTTPS, dados persistentes, limite de memória, healthcheck e rotação de logs.

## Tecnologias

- Docker Compose
- Portainer CE (`portainer/portainer-ce`), versão padrão `2.39.2`

## Pré-requisitos

- Docker Desktop em execução, com Docker Compose disponível.
- Acesso ao socket Docker do host, necessário para que o Portainer possa gerenciar o ambiente Docker.

## Configuração

Crie o arquivo de configuração local a partir do exemplo:

```powershell
Copy-Item .env.example .env
```

Depois, ajuste as variáveis necessárias em `.env`. Esse arquivo é ignorado pelo Git e não deve ser versionado.

## Instalação

Não há dependências de aplicação ou gerenciador de pacotes neste repositório. A imagem do Portainer é obtida pelo Docker Compose durante a primeira inicialização.

## Execução local

Inicie a stack em segundo plano:

```powershell
docker compose up -d
```

Acesse o Portainer em `https://localhost:<PORTAINER_HTTPS_PORT>`. Se a variável não for definida, a porta padrão é `9443`.

Para interromper a stack:

```powershell
docker compose down
```

## Scripts disponíveis

Não há scripts de desenvolvimento, lint, type check, testes ou build definidos no repositório.

| Comando | Descrição |
| ------- | --------- |
| `docker compose up -d` | Cria e inicia a stack em segundo plano. |
| `docker compose down` | Interrompe e remove os recursos da stack criados pelo Compose. |

## Variáveis de ambiente

As variáveis abaixo são definidas em `.env.example` e usadas pelo `docker-compose.yml`:

| Variável | Descrição |
| -------- | --------- |
| `COMPOSE_PROJECT_NAME` | Prefixo usado nos nomes de container, volume e rede da stack. |
| `PORTAINER_VERSION` | Versão da imagem do Portainer CE a executar. |
| `PORTAINER_HTTPS_PORT` | Porta HTTPS exposta no host. |
| `PORTAINER_MEMORY_LIMIT` | Limite de memória atribuído ao container. |

## Estrutura do projeto

```text
.
├── docker-compose.yml  # Definição da stack do Portainer
└── .env.example        # Modelo de configuração local
```

## Testes e validações

Não há testes automatizados, linter, verificação de tipos ou processo de build documentados.

## Docker

A stack executa um único serviço, `portainer`, conectado à rede bridge `portainer_network`. Os dados persistem no volume nomeado `portainer_data` e o socket `/var/run/docker.sock` é montado para permitir o gerenciamento do Docker do host.

O serviço expõe somente HTTPS na porta `9443` do container, usa `restart: unless-stopped`, aplica `no-new-privileges`, limita a memória conforme a configuração e mantém logs no driver `json-file` com rotação.

## Deploy

Não há processo, plataforma ou workflow de deploy documentado no repositório.

## Observações importantes

- A porta HTTP `9000` não é exposta pela configuração.
- O acesso ao socket Docker concede ao Portainer capacidade de administrar o Docker do host; mantenha a interface restrita a usuários autorizados.
