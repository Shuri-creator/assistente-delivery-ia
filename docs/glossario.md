# Glossário Técnico: Orquestração Agêntica e Delivery

Este glossário reúne os principais termos e conceitos técnicos utilizados no desenvolvimento do Assistente de Delivery, abrangendo Inteligência Artificial, Orquestração de Sistemas e Infraestrutura Cloud.

---

### 🧠 Inteligência Artificial e Agentes

*   **A2A (Agent-to-Agent):** Protocolo de interface aberta projetado para coordenar a delegação de tarefas entre plataformas de agentes independentes, permitindo que um agente cliente solicite ações de um agente remoto sem acessar sua memória ou ferramentas internas.
*   **Agentes de IA:** Sistemas de software autônomos que utilizam Modelos de Fundação (FMs) para raciocinar, planejar e completar tarefas complexas através de fluxos de trabalho distribuídos, operando de forma dinâmica em vez de seguir árvores de decisão estáticas.
*   **Amazon Bedrock:** Serviço totalmente gerenciado da AWS que oferece acesso unificado a Modelos de Fundação (FMs) líderes de mercado via API, permitindo a construção de aplicações de IA generativa com segurança e privacidade.
*   **Chain-of-Thought (CoT):** Técnica de *Prompt Engineering* onde o modelo é instruído a decompor o raciocínio em etapas lógicas intermediárias antes de fornecer a resposta final, melhorando a precisão em tarefas complexas.
*   **Constrained Decoding (Decodificação Restrita):** Tecnologia que opera no nível de geração de tokens do modelo para forçar a saída a conformar-se estritamente a um esquema (ex: JSON Schema), eliminando preâmbulos conversacionais ou formatações inválidas.
*   **Fine-Tuning:** Processo de ajuste fino dos parâmetros de um Modelo de Fundação através de treinamento supervisionado com dados específicos de um domínio, visando especializar o modelo em contextos de negócio particulares.
*   **Grounding:** Técnica de ancoragem que valida as respostas do modelo contra bases de conhecimento oficiais (como o menu do restaurante) para mitigar alucinações e garantir a veracidade das informações fornecidas.
*   **LLM-as-a-Judge:** Conceito onde um modelo de IA altamente capacitado é utilizado para avaliar, de forma automatizada e escalável, a qualidade e a segurança das saídas produzidas por outros modelos em produção.
*   **MCP (Model Context Protocol):** Padrão de interoperabilidade que conecta modelos diretamente a ferramentas do sistema local, utilizando fluxos de entrada/saída padrão para interagir com bancos de dados e ambientes de execução seguros.
*   **Prompt Engineering:** Prática de estruturar instruções e contextos de forma avançada para direcionar o comportamento de um modelo sem a necessidade de alterar seus pesos originais.
*   **RAG (Retrieval-Augmented Generation):** Arquitetura que recupera dados de fontes externas (bases de conhecimento vetorizadas) em tempo real para fornecer contexto adicional ao modelo, garantindo respostas atualizadas e precisas.
*   **Structured Outputs:** Funcionalidade que garante o recebimento de respostas JSON validadas por schema, permitindo que sistemas determinísticos confiem nos dados gerados pela IA sem a necessidade de lógica de validação adicional.

---

### ⚙️ Orquestração e Workflows

*   **ASL (Amazon States Language):** Linguagem declarativa baseada em JSON utilizada para definir máquinas de estado no AWS Step Functions, descrevendo estados, transições e tratamentos de erro.
*   **AWS Step Functions:** Serviço de orquestração *serverless* que coordena microsserviços distribuídos através de fluxos de trabalho visuais, gerenciando o estado da execução e retentativas automaticamente.
*   **Claim Check Pattern:** Padrão arquitetural utilizado para gerenciar payloads grandes no Step Functions (que possui limite de 256KB). Os dados reais são armazenados no Amazon S3 e apenas uma referência (URI) é passada entre os estados do workflow.
*   **HITL (Human-in-the-Loop):** Padrão de integração onde o workflow automático é pausado (geralmente via `.waitForTaskToken`) para aguardar uma aprovação ou intervenção manual humana antes de prosseguir para etapas críticas, como transações financeiras.
*   **Saga Pattern:** Padrão de design para gerenciar transações distribuídas entre múltiplos microsserviços com bancos de dados independentes. Se uma etapa falha, o orquestrador dispara transações compensatórias para reverter as mudanças anteriores e manter a consistência eventual.
*   **Transações Compensatórias:** Ações executadas para desfazer o efeito de uma transação local bem-sucedida anterior quando uma etapa subsequente no workflow do Saga Pattern falha (ex: estornar um pagamento se a reserva do produto falhar).

---

### 🏗️ Arquitetura e Produção

*   **Agent Gateway:** Plano de controle especializado posicionado entre as aplicações e os agentes. Ele centraliza autenticação, governança, roteamento de intenções e monitoramento granular de custos de tokens e ferramentas.
*   **Agent Runtime:** Núcleo de execução real do sistema agêntico responsável por receber eventos de entrada, gerenciar o ciclo de vida dos agentes, manter o contexto da sessão e coordenar a execução das ferramentas.
*   **DynamoDBSaver:** Biblioteca de persistência (*checkpointer*) que salva snapshots do estado do agente no Amazon DynamoDB a cada transição, permitindo recuperação de falhas e depuração do tipo "viagem no tempo".
*   **IaC (Infrastructure as Code):** Prática de declarar e gerenciar recursos de infraestrutura (Lambdas, Step Functions, bancos de dados) através de arquivos de configuração legíveis por máquina, utilizando frameworks como AWS SAM ou Terraform.
*   **Idempotência:** Propriedade de uma operação que pode ser executada múltiplas vezes sem alterar o resultado além da aplicação inicial. Essencial para funções de compensação que podem sofrer retentativas automáticas.
*   **Princípio do Menor Privilégio:** Prática de segurança que consiste em conceder às identidades (roles IAM) apenas as permissões estritamente necessárias para realizar suas tarefas, evitando o uso de curingas (`*`) para mitigar riscos.
*   **VPC Endpoints:** Interfaces de rede que permitem a comunicação privada entre a VPC e os serviços AWS (como Bedrock e S3) através da rede interna da AWS, garantindo que os dados de transações sensíveis não trafeguem pela internet pública.

---
*Este glossário serve como referência central para a terminologia técnica adotada no repositório, garantindo alinhamento entre as fases de arquitetura e execução.*
