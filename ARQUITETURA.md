# 🏗 Arquitetura do Projeto TimeWise

Este diagrama mostra a arquitetura completa do **TimeWise**, que utiliza **Azure DevOps (Build e Release Pipelines)**, **Azure Container Registry (ACR)**, **Azure Container Instance (ACI)** e **Azure Database for PostgreSQL**, garantindo integração contínua e deploy automatizado em nuvem.

```mermaid
flowchart LR

USER((Usuário Final))
DEV((Desenvolvedor))

subgraph "Azure DevOps CI/CD"
  CI[1️⃣ Build Pipeline - Maven + Docker Push]
  CD[2️⃣ Release Pipeline - Deploy Automático no ACI]
end

subgraph "Azure Cloud - timewise-rg"
  ACR[(ACR - acrrm555276)]
  ACI[(ACI - acirm555276)]
  DB[(PostgreSQL Flexible Server - pg-rm555276)]
end

%% --- FLUXO DEVOPS ---
DEV -->|Commit & Push| CI
CI -->|Cria e envia imagem Docker| ACR
CD -->|Faz deploy da imagem| ACI

%% --- EXECUÇÃO EM PRODUÇÃO ---
USER -->|Acessa API via HTTP| ACI
ACI -->|Conexão JDBC| DB

%% --- RELACIONAMENTO DE COMPONENTES ---
CI -->|Dispara Release| CD
ACR <--> ACI
