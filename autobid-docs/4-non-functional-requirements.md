# Non-Functional Requirements (NFR)

Technical restrictions and quality standards that the system must follow to ensure performance, security, and reliability.

## Performance and Scalability
* **NFR-01 (Bid Latency):** The total time between the user's click and the bid confirmation (persistence + cache update) must not exceed **200ms** in 95% of cases.
* **NFR-02 (Concurrency):** The auction service must support peaks of up to **1,000 simultaneous requests** on the same bid endpoint without generating race conditions.
* **NFR-03 (Cache):** Frequently read data (Current Value, Auction Status, Vehicle Data) must be cached in Redis with a TTL of 5 seconds for active auctions and a long TTL for static vehicle data.

## Security and Audit
* **NFR-04 (Authentication):** All external communication must be protected via **JWT (JSON Web Tokens)**.
* **NFR-05 (Password):** User passwords must be stored using a strong cryptographic hash, specifically **BCrypt** or **Argon2**.
* **NFR-06 (Audit):** Every bid must be recorded in an immutable database (MongoDB) containing user ID, auction ID, amount, millisecond-precision timestamp, and IP address.

## Technology and Architecture
* **NFR-07 (Backend):** Developed in **Java 21 LTS** using **Spring Boot 3.2+**.
* **NFR-08 (Databases):** * **PostgreSQL:** Used for critical relational data such as Users, Wallets, and Catalog.
    * **MongoDB:** Used for high-volume event logs and bid history.
* **NFR-09 (Messaging):** **RabbitMQ** for asynchronous communication between microservices (e.g., triggering notifications after an auction ends).
* **NFR-10 (Containerization):** All services must include a `Dockerfile`, and the local environment must be orchestrated via **Docker Compose**.

## Availability and Resilience
* **NFR-11 (Resilience):** If external services like the FIPE API (BrasilAPI) are unavailable, the system must remain operational through a **Circuit Breaker** pattern, allowing manual registration or retry mechanisms.

## API Management and Reliability
* **NFR-12 (Documentation):** Each microservice must expose its API contract via **Swagger/OpenAPI 3.0**, accessible through the API Gateway.
* **NFR-13 (Monitoring):** Services must implement health check endpoints using **Spring Boot Actuator** for real-time monitoring.
* **NFR-14 (Error Standardization):** All services must return errors in a unified JSON format containing a timestamp, status code, message, and the requested path.

## Data Performance and Consistency
* **NFR-15 (Read Performance):** Query endpoints (`GET`) must respond in less than **100ms** in 90% of cases, utilizing local or distributed caches when applicable.
* **NFR-16 (Eventual Consistency):** Data synchronization between services via RabbitMQ must have a maximum propagation delay of **2 seconds**.

## Integrity and Advanced Security
* **NFR-17 (Idempotency):** Critical operations such as **Place Bid** and **Wallet Deposit** must be idempotent to prevent duplicate processing in case of network failures.
* **NFR-18 (Input Validation):** All incoming data must be sanitized against XSS (Cross-Site Scripting) and SQL Injection before persistence.
* **NFR-19 (Image Processing):** Uploaded vehicle photos must be automatically resized and optimized to reduce bandwidth consumption.

---

## 🔄 Revision History

| Date | Name | Observation |
| :--- | :--- | :--- |
| 2026-02-17 | Gemini Pro & Taynara Vitorino | Initial document creation via AI and technical review of requirements. |
| 2026-02-18 | Taynara Vitorino | Unified documentation to English and refined technical terminology. |