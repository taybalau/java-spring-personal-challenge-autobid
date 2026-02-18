# AutoBid - Personal Challenge 🚗💰

> **A High-Performance Distributed Vehicle Auction System**

[![Java](https://img.shields.io/badge/Java-21-orange?logo=java)](https://www.java.com/)
[![Spring Boot](https://img.shields.io/badge/Spring_Boot-3.2+-green?logo=spring)](https://spring.io/projects/spring-boot)
[![Docker](https://img.shields.io/badge/Docker-Enabled-blue?logo=docker)](https://www.docker.com/)
[![License](https://img.shields.io/badge/License-MIT-lightgrey)](LICENSE)

## 📖 About the Project

**AutoBid** is a vehicle auction platform developed using a microservices architecture. The goal of this project is to simulate a real-world high-concurrency environment where users place bids in real-time, integrating data from the FIPE Table (Brazilian Vehicle Price Index) and managing virtual wallets with financial integrity.

This repository documents my journey ("Personal Challenge") to master advanced concepts such as **Event-Driven Architecture**, **Distributed Locking**, **Real-time WebSockets**, and **CQRS**.

---

## 🤖 Methodology: AI + Human Review

This project adopts a modern approach to software development assisted by AI.

* **Conception & Architecture Phase:** All theoretical aspects — including requirements gathering, personas, UML diagrams, C4 architecture, and User Stories — were generated using **Google Gemini Pro**, simulating an external consultancy scenario.
* **Review & Engineering Phase:** All AI-generated content underwent a **human technical review** to ensure feasibility, logical coherence, and alignment with Software Engineering best practices learned in my degree program.
* **Implementation Phase:** The code, infrastructure configuration, and complex problem-solving are human-authored, focusing on practical learning and mastery of the stack.

---

## 📚 Architecture Documentation

The complete project documentation, including functional requirements, architecture diagrams, and design decisions, can be found in the directory:

📂 **[`autobid-docs/`](autobid-docs/)**

There you will find:
* Personas and User Stories (US).
* Functional and Non-Functional Requirements.
* Use Case and Sequence Diagrams.
* Data Model (ER Diagram).

---

## 🛠️ Tech Stack & Tools

The project utilizes a modern "Enterprise Grade" stack, running entirely on Docker containers.

| Category | Technology | Version | Role in Project |
| :--- | :--- | :--- | :--- |
| **Core** | **Java** | 21 (LTS) | Base language with *Virtual Threads* support. |
| **Framework** | **Spring Boot** | 3.2+ | Framework for microservices creation. |
| **Database (Relational)** | **PostgreSQL** | 16 | Persistence for critical data (Users, Wallet, Vehicles). |
| **Database (NoSQL)** | **MongoDB** | Latest | Storage for bid history and audit logs. |
| **Cache / Lock** | **Redis** | Latest | High-speed cache and *Distributed Locks* (Redisson). |
| **Messaging** | **RabbitMQ** | 3.12+ | Asynchronous communication between services (Event-Driven). |
| **API Gateway** | **Spring Cloud Gateway** | Latest | Single entry point and routing. |
| **Communication** | **OpenFeign** | 4.x | Declarative HTTP client for external APIs (BrasilAPI). |
| **Real-time** | **WebSockets** | Spring | Real-time bid updates for the frontend. |
| **Migration** | **Flyway** | Latest | Database versioning and migration. |
| **Infra** | **Docker Compose** | Latest | Development environment orchestration. |

---

## 🚀 How to Run (Coming Soon)

*Instructions to run the project locally will be added after the microservices development phase is complete.*

---

**Author:** Taynara Vitorino  
*Personal Architecture and Java Development Challenge.*