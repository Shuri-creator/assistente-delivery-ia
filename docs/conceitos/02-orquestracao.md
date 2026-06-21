# Orquestração de Workflows: A Espinha Dorsal Determinística

### 1. Definição e Propósito
Enquanto os Agentes de IA representam a cognição e a tomada de decisão não-determinística, a **Orquestração** é o componente responsável por garantir que as regras de negócio sejam executadas de forma previsível, segura e escalável. Neste projeto, utilizamos o **AWS Step Functions** como o motor de execução que coordena microserviços distribuídos, transformando intenções extraídas pela IA em fluxos de trabalho operacionais.

### 2. AWS Step Functions e ASL (Amazon States Language)
A orquestração é definida através da **Amazon States Language (ASL)**, uma especificação declarativa baseada em JSON que descreve máquinas de estado. 
*   **Desacoplamento:** Permite separar a lógica de negócio do código das funções individuais (AWS Lambda), facilitando a manutenção e a observabilidade.
*   **Visualização:** Oferece um grafo visual em tempo real para monitorar cada etapa do pedido de delivery, desde a recepção até a entrega.

### 3. Padrões de Orquestração Aplicados
Para gerenciar a complexidade de um pedido de delivery, aplicamos os seguintes padrões fundamentais:
*   **Sequenciamento:** Execução linear de passos como `ValidarPedido` seguido de `ProcessarPagamento`.
*   **Seleção Baseada em Dados (Estado Choice):** O orquestrador avalia o payload JSON retornado pelo Bedrock para decidir o caminho: se o item existe, segue para pagamento; se não, solicita esclarecimento.
*   **Execução em Paralelo (Estado Parallel):** Otimiza o tempo de resposta disparando simultaneamente a reserva do estoque e a autorização do cartão de crédito.
*   **Tratamento de Exceções (Try/Catch/Finally):** Captura erros de rede ou falhas financeiras de forma granular, evitando que o sistema trave em estados inconsistentes.

### 4. Saga Pattern: Resiliência em Sistemas Distribuídos
Em uma arquitetura de microserviços, onde cada serviço possui seu próprio banco de dados, transações ACID tradicionais não são viáveis. Implementamos o **Saga Pattern** para garantir a integridade dos dados:
*   **Transações Locais:** Cada etapa (estoque, pagamento, logística) é uma transação independente.
*   **Ações Compensatórias:** Se o pagamento falhar após a reserva do estoque, o Step Functions dispara automaticamente uma "função de compensação" para liberar o item reservado, mantendo a consistência eventual do sistema.

### 5. Integração Determinística vs. Cognição
A fronteira entre IA e Orquestração é clara:
1.  **IA (Bedrock):** Interpreta "Quero uma pizza de calabresa sem cebola" e gera `{"item": "pizza_calabresa", "remocao": ["cebola"]}`.
2.  **Orquestrador (Step Functions):** Recebe o JSON e garante que, se o pagamento for aprovado, o pedido *obrigatoriamente* chegue à cozinha e o estoque seja baixado.
