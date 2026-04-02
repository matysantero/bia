# 🤖 BIA — Backend com IA & Alta Disponibilidade na AWS

> **Português** | [English below](#-bia--ai-powered-backend-on-aws)

Aplicação Node.js com CRUD completo, agente de IA conectado diretamente ao banco de dados, e infraestrutura de produção na AWS com alta disponibilidade testada sob stress.

Desenvolvido durante a **Imersão AWS & IA** (março/2026), implementado e operado de forma independente em conta AWS real.

---

## 🏗️ Arquitetura

```
GitHub (push)
     │
     ▼
 CodeBuild ──► Docker Build ──► ECR (imagem taggeada por commit hash)
                                       │
                                       ▼
                              ECS Service (Fargate)
                            /                      \
                     Task (AZ-1)              Task (AZ-2)
                            \                      /
                             ▼                    ▼
                      ALB (HTTPS) ◄── Target Group (2 healthy)
                             │
                             ▼
                    RDS PostgreSQL
                   /               \
           Writer Endpoint     Reader Endpoint
                             │
                             ▼
                    MCP Server ◄──► Agente de IA
```

---

## ⚙️ Stack Técnica

| Camada | Tecnologia |
|---|---|
| Runtime | Node.js + Express |
| ORM | Sequelize |
| Banco de dados | PostgreSQL (RDS Multi-AZ) |
| Container | Docker + Amazon ECR |
| Orquestração | Amazon ECS (Fargate) |
| Load Balancer | ALB com HTTPS (ACM) |
| CI/CD | GitHub → AWS CodeBuild → ECS |
| IA | Agente com MCP Server + PostgreSQL |
| Segurança | Security Groups por camada (ALB / ECS / RDS) |

---

## 🚀 Pipeline CI/CD

Cada `git push` para `main` dispara automaticamente:

1. **CodeBuild** faz login no ECR com credenciais temporárias
2. **Docker build** da imagem com tag baseada no commit hash (`$CODEBUILD_RESOLVED_SOURCE_VERSION`)
3. **Push** das imagens `:latest` e `:<commit-hash>` para o ECR
4. Geração do artefato `imagedefinitions.json` para o ECS
5. **Deploy automático** da nova task no ECS Cluster

> Zero downtime: o ECS faz rolling update mantendo tasks healthy durante o deploy.

---

## 🤖 Agente de IA com MCP Server

O projeto inclui um agente de IA especializado com acesso direto ao banco PostgreSQL via **MCP Server** (Model Context Protocol).

Capacidades:
- Consultar dados em tempo real no banco
- Guiar configurações de infraestrutura AWS
- Responder perguntas sobre o projeto com contexto real

---

## ✅ Resultados de Alta Disponibilidade

- **100% uptime** durante teste de stress com requisições contínuas no ALB
- **2 targets healthy** simultâneos em Availability Zones distintas
- **Status 200** contínuo monitorado via CloudWatch durante toda a janela de teste
- **RDS Multi-AZ** com endpoints separados para escritas (Writer) e leituras (Reader)

---

## 🔒 Segurança em Camadas

- **ALB Security Group**: aceita tráfego externo na porta 443 (HTTPS)
- **ECS Security Group**: aceita tráfego apenas do ALB
- **RDS Security Group**: aceita tráfego apenas do ECS
- Sem exposição direta do banco ou dos containers à internet

---

## 📁 Estrutura do Repositório

```
bia/
├── api/                    # Rotas e controllers da API
├── client/                 # Frontend estático
├── config/                 # Configurações (DB, ambiente)
├── database/migrations/    # Migrations Sequelize
├── docs/                   # Documentação
├── lib/                    # Utilitários
├── scripts/                # Scripts de automação AWS (ECR push, etc.)
├── scripts_evento/         # Scripts usados durante a imersão
├── tests/                  # Testes unitários
├── Dockerfile              # Build da imagem de produção
├── buildspec.yml           # Pipeline CodeBuild (CI/CD)
├── compose.yml             # Ambiente local com Docker Compose
└── index.js / server.js    # Entry points da aplicação
```

---

## 🖥️ Rodando Localmente

**Pré-requisitos**: Docker, Node.js

```bash
# Clonar o repositório
git clone https://github.com/matysantero/bia.git
cd bia

# Subir com Docker Compose
./rodar_app_local_unix.sh
# ou no Windows:
# rodar_app_local_windows.bat

# Rodar as migrations
docker compose exec server bash -c 'npx sequelize db:migrate'
```

A aplicação estará disponível em `http://localhost:3000`

---

## 📸 Evidências de Produção

> *Screenshots da infraestrutura em execução — adicionadas em breve*

- [ ] ECS Cluster com tasks rodando em 2 AZs
- [ ] ALB com 2 targets healthy
- [ ] RDS com Writer/Reader endpoints ativos
- [ ] CodeBuild com histórico de builds por commit hash
- [ ] CloudWatch com métricas durante o stress test
- [ ] ECR com imagens taggeadas

---

## 🔗 Contexto

Este projeto foi desenvolvido como parte da **Imersão AWS & IA** ministrada por [Henrylle Maia](https://github.com/henrylle), com foco em aplicações reais de Cloud Computing e IA generativa integrada à infraestrutura AWS.

Faz parte do meu portfólio de transição de carreira para Cloud/DevOps, ao lado de:
- **CrawlInsights** — análise de logs de servidor com S3 + Athena para cliente real
- **Loft Gigóia** — site WordPress Multi-AZ no Lightsail com SSL
- **Meu Primeiro Deploy** — ebook sobre AWS e Docker (publicado e vendido)

---

---

# 🤖 BIA — AI-Powered Backend on AWS

> [Português acima](#-bia--backend-com-ia--alta-disponibilidade-na-aws) | **English**

Node.js application with full CRUD, an AI agent connected directly to the database, and production-grade AWS infrastructure with stress-tested high availability.

Built during **Imersão AWS & IA** (March 2026), deployed and operated independently on a real AWS account.

---

## 🏗️ Architecture

```
GitHub (push)
     │
     ▼
 CodeBuild ──► Docker Build ──► ECR (commit-hash tagged image)
                                       │
                                       ▼
                              ECS Service (Fargate)
                            /                      \
                     Task (AZ-1)              Task (AZ-2)
                            \                      /
                             ▼                    ▼
                      ALB (HTTPS) ◄── Target Group (2 healthy)
                             │
                             ▼
                    RDS PostgreSQL
                   /               \
           Writer Endpoint     Reader Endpoint
                             │
                             ▼
                    MCP Server ◄──► AI Agent
```

---

## ⚙️ Tech Stack

| Layer | Technology |
|---|---|
| Runtime | Node.js + Express |
| ORM | Sequelize |
| Database | PostgreSQL (RDS Multi-AZ) |
| Container | Docker + Amazon ECR |
| Orchestration | Amazon ECS (Fargate) |
| Load Balancer | ALB with HTTPS (ACM) |
| CI/CD | GitHub → AWS CodeBuild → ECS |
| AI | Agent with MCP Server + PostgreSQL |
| Security | Per-layer Security Groups (ALB / ECS / RDS) |

---

## 🚀 CI/CD Pipeline

Every `git push` to `main` automatically triggers:

1. **CodeBuild** logs into ECR with temporary credentials
2. **Docker build** with image tagged by commit hash (`$CODEBUILD_RESOLVED_SOURCE_VERSION`)
3. **Push** of both `:latest` and `:<commit-hash>` images to ECR
4. `imagedefinitions.json` artifact generated for ECS
5. **Automatic rolling deploy** to ECS Cluster with zero downtime

---

## 🤖 AI Agent with MCP Server

The project includes a specialized AI agent with direct PostgreSQL access via **MCP Server** (Model Context Protocol).

Capabilities:
- Real-time database queries
- AWS infrastructure configuration guidance
- Project-aware responses with live data context

---

## ✅ High Availability Results

- **100% uptime** during stress test with continuous ALB requests
- **2 healthy targets** simultaneously across distinct Availability Zones
- **Continuous HTTP 200** monitored via CloudWatch throughout the test window
- **RDS Multi-AZ** with separate Writer and Reader endpoints

---

## 🔒 Layered Security

- **ALB Security Group**: accepts external HTTPS traffic on port 443
- **ECS Security Group**: accepts traffic only from the ALB
- **RDS Security Group**: accepts traffic only from ECS
- No direct internet exposure of containers or database

---

## 🖥️ Running Locally

**Prerequisites**: Docker, Node.js

```bash
git clone https://github.com/matysantero/bia.git
cd bia

# Start with Docker Compose
./rodar_app_local_unix.sh

# Run migrations
docker compose exec server bash -c 'npx sequelize db:migrate'
```

App available at `http://localhost:3000`

---

## 🔗 Context

Built during **Imersão AWS & IA** by [Henrylle Maia](https://github.com/henrylle), focused on real-world cloud applications with generative AI integrated into AWS infrastructure.

Part of my cloud/DevOps career transition portfolio, alongside:
- **CrawlInsights** — server log analysis with S3 + Athena for a real client
- **Loft Gigóia** — Multi-AZ WordPress site on Lightsail with SSL
- **Meu Primeiro Deploy** — published ebook on AWS and Docker

---

*Infrastructure deployed on AWS (us-east-1) — torn down after documentation to avoid idle costs. All components validated in a live production environment.*
```

📸 Evidências de Produção / Production Evidence
Note: The following infrastructure was live and tested in us-east-1. Resources were decommissioned after documentation to optimize AWS costs.

1. Orquestração e Alta Disponibilidade (ECS & ALB)
O tráfego é distribuído em múltiplas zonas de disponibilidade (AZs) garantindo resiliência.
Traffic is distributed across multiple Availability Zones (AZs) ensuring resilience.

2. Banco de Dados Gerenciado (RDS Multi-AZ)
Configuração com isolamento de rede e endpoints de leitura/escrita.
Managed database with network isolation and read/write endpoints.

3. Pipeline CI/CD Automatizado (CodeBuild & CodePipeline)
Despliegue automático con versionamiento por commit hash.
Automated deployment with commit-hash versioning.

4. Agente de IA com MCP Server
A IA acessando o banco de dados em tempo real para consultas contextuais.
AI accessing the database in real-time for contextual queries.

5. Interface da Aplicação (Frontend)
Aplicação funcional acessível via HTTPS.
Functional application accessible via HTTPS.

6. Segurança e Redes (Security Groups)
Configuração de regras de entrada e saída por camada para isolamento total.
Layered Security Group rules for total environment isolation.

📂 **Confira a galeria completa de evidências aqui:**[Fotos para portfolio](https://github.com/matysantero/bia/tree/main/Fotos%20para%20portfolio)

