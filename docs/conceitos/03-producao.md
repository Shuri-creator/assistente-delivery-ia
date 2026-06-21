# Produção e Prontidão de Sistemas Agênticos

### 1. Transição para a Produção: Do Design à Execução
A prontidão para produção em sistemas agênticos exige a separação rigorosa entre o processamento cognitivo de alta latência e os blocos de execução determinísticos. Enquanto protótipos focam em lógica de raciocínio, sistemas em produção requerem isolamento multi-tenant, controles de concorrência e mecanismos de rastreio visual para garantir a estabilidade operacional. A evolução do projeto segue um roteiro faseado, culminando no deploy automatizado e na auditoria de configurações ativas na nuvem.

### 2. Infraestrutura como Código (IaC)
Para garantir a consistência entre ambientes de desenvolvimento, teste e produção, todos os recursos (Lambdas, Step Functions, DynamoDB) devem ser declarados via IaC.
*   **AWS SAM:** Ideal para abstrações de alto nível e políticas de segurança pré-definidas para recursos serverless.
*   **Terraform:** Preferencial para ambientes híbridos ou multi-cloud, permitindo a gestão de estados complexos e integração com provedores externos.

### 3. Segurança e Governança: Least Privilege
A segurança é priorizada através da aplicação rigorosa do **Princípio do Menor Privilégio**.
*   **IAM Scoping:** As roles de execução devem ser restritas a modelos específicos (ex: `bedrock:InvokeModel` apenas para o modelo Nova ou Claude utilizado) e recursos nomeados, evitando o uso de wildcards (`*`).
*   **VPC Endpoints:** Para mitigar riscos de exfiltração de dados, todo o tráfego entre a computação serverless e os serviços de IA/Banco de Dados deve trafegar pela rede privada da AWS, sem exposição à internet pública.

### 4. Observabilidade e Tracing
Sistemas agênticos podem falhar de forma sutil através de alucinações ou respostas malformadas que passam em validações sintáticas, mas falham na lógica de negócio.
*   **Tracing Visual:** O uso de **AWS X-Ray** e **CloudWatch Logs** permite visualizar cada transição de estado e o payload exato que alimentou o modelo de IA em um momento específico.
*   **LangSmith:** Integrado via variáveis de ambiente nos workers, fornece rastreabilidade profunda das cadeias de raciocínio das LLMs e latência de ferramentas.

### 5. Estratégias de Custo e Performance
A escolha do tipo de workflow impacta diretamente o custo e a experiência do usuário.
*   **Workflows Standard:** Utilizados para processos de longa duração (até 1 ano), como pedidos que aguardam aprovação humana, oferecendo histórico de execução detalhado.
*   **Workflows Express:** Ideais para bots de chat em tempo real e RAG de alto volume, focados em baixa latência e menor custo por execução.
*   **Task-Type Routing:** Para otimizar custos, tarefas de extração simples são enviadas a modelos menores (Haiku), reservando modelos de fronteira (Sonnet) para raciocínio complexo.

### 6. Resiliência: Saga Pattern e Human-in-the-Loop
Em arquiteturas distribuídas, a integridade é mantida por mecanismos de compensação e validação humana.
*   **Saga Pattern:** Garante que, se uma transação de pagamento falhar, o orquestrador dispare automaticamente ações para reverter o estoque, mantendo a consistência eventual.
*   **HITL (Human-in-the-Loop):** Em operações críticas ou de alta ambiguidade, o sistema pausa a execução via `.waitForTaskToken`, aguardando validação manual antes de autorizar a transação financeira final.
