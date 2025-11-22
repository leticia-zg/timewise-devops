# ☁️ Arquitetura do TimeWise no Azure

Este diagrama mostra a arquitetura completa do projeto **TimeWise**, que utiliza **Azure DevOps**, **ACR (Azure Container Registry)**, **ACI (Azure Container Instance)** e **Azure Database for PostgreSQL** para entrega contínua e execução em nuvem.

```mermaid
graph TD
    A[Usuário Final / Navegador] -->|Acessa aplicação via URL| B[Azure Container Instance (ACI)]
    B -->|Executa imagem Docker| C[Azure Container Registry (ACR)]
    C -->|Pipeline de Build - Push da imagem| D[Azure DevOps - Pipeline de Build]
    D -->|Código-fonte e YAML| E[Azure Repos]
    D -->|Publica artefato + executa testes| C
    D -->|Dispara Release| F[Azure DevOps - Pipeline de Release]
    F -->|Deploy automático| B
    B -->|Conexão JDBC| G[Azure Database for PostgreSQL]

    subgraph CI/CD
        D
        F
    end

    subgraph Infraestrutura Azure
        C
        B
        G
    end

    classDef azure fill:#007FFF,stroke:#004080,color:#fff;
    classDef devops fill:#FF8C00,stroke:#B87333,color:#fff;
    class C,B,G azure;
    class D,F devops;
```

---

### 🧩 Descrição dos Componentes
- **Usuário Final:** Acessa a API hospedada no ACI por meio de uma URL pública.
- **Azure DevOps - Build:** Compila o código, executa os testes e envia a imagem Docker para o ACR.
- **Azure DevOps - Release:** Realiza o deploy automático no ACI, utilizando a imagem mais recente.
- **Azure Container Registry (ACR):** Armazena as imagens Docker versionadas.
- **Azure Container Instance (ACI):** Executa a aplicação Java Spring Boot em container.
- **Azure Database for PostgreSQL:** Banco de dados PaaS para persistência dos dados da aplicação.

---

📘 **Fluxo Completo:**
1. O desenvolvedor faz o commit no Azure Repos.
2. A pipeline de **Build** é disparada, gerando a imagem e enviando ao **ACR**.
3. A pipeline de **Release** realiza o deploy da imagem no **ACI**.
4. A aplicação no **ACI** se conecta ao **PostgreSQL**.
5. O **usuário final** acessa a API via URL pública gerada pelo ACI.
