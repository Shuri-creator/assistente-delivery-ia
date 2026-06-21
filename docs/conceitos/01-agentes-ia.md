# Agentes de IA: O Motor Cognitivo do Assistente

### 1. Definição e Papel
Os **Agentes de IA** são definidos como sistemas de software autônomos que utilizam Modelos de Fundação (FMs) para raciocinar, planejar e executar tarefas complexas através de fluxos de trabalho distribuídos. Diferente de sistemas baseados em árvores de decisão estáticas, esses agentes operam de forma dinâmica, utilizando o processamento de linguagem natural para converter demandas não estruturadas em intenções determinísticas e ações operacionais.

### 2. O Motor Cognitivo: Amazon Bedrock
O processamento de inteligência central do assistente reside no **Amazon Bedrock**, um serviço totalmente gerenciado que provê acesso unificado a modelos de alta performance (como Anthropic Claude, Meta Llama e Amazon Nova).
*   **Abstração Serverless:** A infraestrutura elimina a gestão de servidores, permitindo escalabilidade horizontal automática e foco na lógica agêntica.
*   **Desacoplamento Técnico:** Permite separar a cognição de alta latência da execução determinística realizada por máquinas de estado.

### 3. Técnicas de Inteligência e Grounding
Para garantir a precisão operacional e mitigar alucinações em sistemas de missão crítica, o agente utiliza as seguintes abordagens:
*   **Prompt Engineering:** Uso de técnicas avançadas como *Chain-of-Thought* (CoT) para instruir o modelo a decompor o raciocínio em etapas lógicas antes da resposta final.
*   **RAG (Retrieval-Augmented Generation):** Integração com bases de conhecimento para consulta em tempo real do menu oficial, histórico do usuário e políticas de negócio, garantindo que as respostas sejam contextuais e atualizadas.
*   **Structured Outputs:** Implementação de **Constrained Decoding** para forçar o modelo a gerar respostas estritamente em formato JSON validado. Isso elimina preâmbulos conversacionais que causariam falhas na orquestração determinística.

### 4. Ciclo de Vida: Cognição vs. Execução
A arquitetura do repositório impõe uma separação rigorosa entre a tomada de decisão e a aplicação de regras de negócio:
*   **Fase de Cognição (Agente):** Recebe o input, aplica o grounding via RAG e gera o payload de transição de estado.
*   **Fase de Execução (Orquestração):** O **AWS Step Functions** assume o controle para gerenciar transações de longa duração via **Saga Pattern**, garantindo integridade financeira através de ações compensatórias em caso de falha técnica.

### 5. Governança e Protocolos
Para viabilizar operações multi-agente escaláveis e seguras, utiliza-se o padrão de **Agent Gateway**:
*   **Controle Centralizado:** Camada responsável por autenticação, autorização (RBAC), observabilidade e monitoramento de custos de tokens e ferramentas.
*   **Interoperabilidade:** Adoção de protocolos como **MCP (Model Context Protocol)** para conexões locais com ferramentas e **A2A (Agent-to-Agent)** para delegação de tarefas entre sistemas independentes.
