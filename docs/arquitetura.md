# Arquitetura do Sistema: Assistente de Delivery Agêntico

### 1. Visão Geral da Solução
A arquitetura do assistente baseia-se no princípio de desacoplamento rigoroso entre a **Tomada de Decisão Cognitiva** (não-determinística) e a **Execução de Fluxos de Trabalho** (determinística). O sistema é construído sobre uma infraestrutura *serverless*, garantindo escalabilidade automática e eficiência de custos.

---

### 2. Camada de Cognição (Cérebro do Agente)
O núcleo de inteligência utiliza o **Amazon Bedrock** para processar linguagem natural e extrair intenções estruturadas.

*   **Motor:** Modelos de Fundação (FMs) acessados via Converse API.
*   **Agent Gateway:** Atua como o plano de controle para autenticação, roteamento de intenções baseadas em ferramentas e monitoramento de custos de tokens.
*   **Grounding (RAG):** Consulta a bases de conhecimento para validar itens do menu e histórico do usuário, mitigando alucinações.
*   **Structured Outputs:** Uso de *constrained decoding* para garantir que o agente responda estritamente em JSON, eliminando falhas de *parsing* na transição para a orquestração.

---

### 3. Camada de Orquestração (Espinha Dorsal)
O **AWS Step Functions** coordena as regras de negócio através de máquinas de estado definidas em **Amazon States Language (ASL)**.

*   **Padrões de Fluxo:** Implementação de estados de escolha (*Choice*) para roteamento, paralelos (*Parallel*) para otimização de tempo e tratamento de erros (*Try/Catch*).
*   **Saga Pattern:** Gerenciamento de transações distribuídas entre microserviços de estoque, pagamento e logística. Se um passo falhar (ex: pagamento negado), o orquestrador dispara automaticamente ações compensatórias (ex: liberar reserva de estoque).
*   **Human-in-the-Loop (HITL):** Pausa de execução via `.waitForTaskToken` para aprovações manuais em pedidos ambíguos ou de alto valor.

---

### 4. Camada de Persistência e Memória
A memória do sistema é dividida em dois níveis para garantir continuidade e personalização:

*   **Curto Prazo (Estado da Sessão):** Gerenciado via **DynamoDBSaver**, que persiste o estado do grafo de execução a cada transição.
*   **Longo Prazo (Contexto do Usuário):** Base de dados vetorial (PostgreSQL/pgvector ou DynamoDB) para armazenamento de preferências e histórico de transações passíveis de consulta via RAG.
*   **Claim Check Pattern:** Payloads grandes (>256KB) são armazenados no Amazon S3, passando apenas a referência (URI) entre os estados da Step Function para evitar limites de tamanho de mensagem.

---

### 5. Fluxo de Eventos End-to-End
1.  **Ingestão:** O pedido chega via API Gateway ou evento de mensagem.
2.  **Roteamento:** O **Amazon EventBridge** dispara a execução da Step Function correspondente.
3.  **Processamento:** O agente no Bedrock transforma o texto em um payload JSON de transição de estado.
4.  **Execução:** O orquestrador executa as funções Lambda de validação, reserva e pagamento.
5.  **Feedback:** O status final é persistido no DynamoDB e notificado ao usuário via SNS/Push.

---

### 6. Alternativa Open Source (n8n & Ollama)
Para cenários que exigem controle total da infraestrutura ou restrições orçamentárias:

*   **Orquestrador:** Substituição do Step Functions pelo **n8n Self-Hosted**.
*   **IA:** Substituição do Bedrock pelo **Ollama** executando Llama 3 ou Mistral localmente.
*   **RAG:** Uso de PostgreSQL com a extensão **pgvector**.
