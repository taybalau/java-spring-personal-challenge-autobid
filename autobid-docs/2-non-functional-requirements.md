# Non-Functional Requirements (NFR)

Technical restrictions and quality standards that the system must follow.

## Performance and Scalability
* **NFR-01 (Bid Latency):** The total time between the user's click and the bid confirmation (persistence + cache update) must not exceed **200ms** in 95% of cases.
* **NFR-02 (Concurrency):** The auction service must support peaks of up to **1,000 simultaneous requests** on the same bid endpoint without generating *race conditions* (two equal winning bids).
* **NFR-03 (Cache):** Frequently read data (Current Value, Auction Status, Vehicle Data) must be cached in Redis (TTL of 5 seconds for active auctions, long TTL for vehicles).

## Security and Audit
* **NFR-04 (Authentication):** All external communication must be protected via **JWT (JSON Web Tokens)**.
* **NFR-05 (Password):** User passwords must be stored using a strong hash (**BCrypt** or **Argon2**).
* **NFR-06 (Audit):** Every bid must be recorded in an immutable database (MongoDB) containing: `user_id`, `auction_id`, `amount`, `timestamp_precision_ms`, `ip_address`.

## Technology and Architecture
* **NFR-07 (Backend):** Java 21 LTS with Spring Boot 3.2+.
* **NFR-08 (Database):**
    * **PostgreSQL:** For critical relational data (Users, Wallet, Catalog).
    * **MongoDB:** For high-volume data and history (Bids, Logs).
* **NFR-09 (Messaging):** RabbitMQ for asynchronous communication between microservices (e.g., `auction.ended` -> triggers email and accounting).
* **NFR-10 (Containerization):** All services must have a `Dockerfile` and the local environment must be orchestrated via `docker-compose`.

## Availability
* **NFR-11 (Resilience):** If the FIPE service (BrasilAPI) is unavailable, the system cannot crash/freeze. It must allow manual registration or try again later (Pattern: Circuit Breaker).

## Gestão de API e Fiabilidade
* **NFR-12 (Documentação):** Cada microsserviço deve expor o seu contrato de API via **Swagger/OpenAPI 3.0**, acessível através do API Gateway.
* **NFR-13 (Monitorização):** Os serviços devem implementar endpoints de `/health` (Spring Boot Actuator) para monitorização de estado em tempo real.
* **NFR-14 (Padronização de Erros):** Todos os serviços devem retornar erros num formato JSON unificado contendo `timestamp`, `status_code`, `message` e `path`.

## Performance e Consistência de Dados
* **NFR-15 (Performance de Leitura):** Endpoints de consulta (`GET`) devem responder em menos de **100ms** em 90% dos casos, utilizando caches locais quando aplicável.
* **NFR-16 (Consistência Eventual):** A sincronização de dados entre serviços (via RabbitMQ) deve ter um atraso máximo de **2 segundos**.

## Integridade e Segurança Avançada
* **NFR-17 (Idempotência):** Operações críticas como **Realizar Lance** e **Depósito** devem ser idempotentes para evitar duplicidade em caso de falhas de rede.
* **NFR-18 (Validação de Input):** Todos os dados de entrada devem ser saneados contra XSS (Cross-Site Scripting) e SQL Injection antes da persistência.
* **NFR-19 (Processamento de Imagem):** As fotos enviadas (US-03) devem ser redimensionadas e otimizadas automaticamente para reduzir o consumo de largura de banda.

---

## 🔄 Revision History

| Date | Name | Observation |
| :--- | :--- | :--- |
| 2026-02-17 | Gemini Pro & Taynara Vitorino | Initial document creation via AI and technical review of requirements. |
| | | |