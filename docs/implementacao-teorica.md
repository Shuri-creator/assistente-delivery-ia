# Implementação Teórica: Do Design ao Sistema Executável

### 1. Roteiro de Implementação (Roadmap)
A transição da **Fase 2 (Arquitetura)** para a **Fase 3 (Sistema Executável)** exige uma abordagem modular e incremental para validar cada camada de inteligência e controle.
*   **Fase 1 - Desenvolvimento Local:** Configuração de workers individuais e teste de prompts via Amazon Bedrock Converse API.
*   **Fase 2 - Teste de Integração:** Uso do **Step Functions Local** via Docker para validar transições de estado e caminhos de erro (Try/Catch) sem custos de nuvem.
*   **Fase 3 - Deploy e Auditoria:** Implantação via IaC e configuração de endpoints privados (VPC Endpoints) para garantir que os dados não trafeguem pela internet pública.

---

### 2. O Agent Runtime e os Workers
O núcleo de execução, ou **Agent Runtime**, é o serviço responsável por receber o evento do pedido, criar o contexto e coordenar a resposta dos agentes.
*   **Workers de Execução:** Implementados em **Python** (ideal para processamento de dados e bibliotecas científicas) ou **Node.js** (superior para transações assíncronas de I/O).
*   **Ciclo de Vida:** Gerenciado por frameworks como **LangChain**, que encapsulam instruções de sistema, ferramentas e histórico em abstrações reutilizáveis como o `ChatBedrock`.

---

### 3. Protocolos e Agent Gateway
Para evitar o acoplamento rígido entre a interface do usuário e os modelos de IA, utiliza-se o padrão **Agent Gateway**.
*   **Governança:** O gateway centraliza a autenticação, monitoramento de custos de tokens e imposição de políticas de segurança (guardrails).
*   **Interoperabilidade:** Adoção dos protocolos **MCP (Model Context Protocol)** para conexões locais com ferramentas e **A2A (Agent-to-Agent)** para delegação de tarefas entre agentes independentes.

---

### 4. Codificação da Orquestração (ASL)
A lógica de negócio é traduzida para a **Amazon States Language (ASL)**, permitindo que o AWS Step Functions execute fluxos determinísticos.
*   **Saga Pattern:** Implementado para garantir a integridade em transações distribuídas. Se a reserva de estoque for concluída, mas o pagamento falhar, o orquestrador dispara automaticamente funções Lambda de compensação para reverter o estado.
*   **Human-in-the-Loop:** Uso do padrão `.waitForTaskToken` para pausar o fluxo em pedidos de alta ambiguidade, aguardando aprovação manual via Slack ou e-mail antes de processar o pagamento.

---

### 5. Gestão de Persistência e Memória
A arquitetura diferencia a memória de curto prazo da memória de longo prazo para otimizar performance e custo.
*   **Memória de Sessão (Curto Prazo):** Persistida via **DynamoDBSaver**, que salva snapshots do estado do agente a cada transição. Payloads que excedem 350 KB são automaticamente movidos para o Amazon S3 (**S3 Offloading**).
*   **Memória de Contexto (Longo Prazo):** Uso de bancos de dados vetoriais (ex: PostgreSQL com pgvector) para buscas semânticas em históricos de pedidos e preferências do cliente via RAG.

---

### 6. Grounding e Saídas Estruturadas
Para eliminar alucinações e falhas de processamento, o sistema utiliza a funcionalidade de **Structured Outputs** do Amazon Bedrock.
*   **Constrained Decoding:** O modelo é forçado, no nível de geração de tokens, a responder estritamente conforme um esquema JSON pré-definido.
*   **Integridade:** Ao definir `additionalProperties: false`, garantimos que o agente não injete variáveis inesperadas que quebrariam a lógica da Step Function subsequente.

---

### 7. Infraestrutura como Código (IaC)
A automação do ambiente é realizada preferencialmente via **AWS SAM** (otimizado para serverless) ou **Terraform** (ideal para ambientes multi-cloud).
*   **Princípio do Menor Privilégio:** Todas as roles IAM são escopadas para modelos e recursos específicos, evitando o uso de wildcards (`*`) para mitigar riscos de segurança.
