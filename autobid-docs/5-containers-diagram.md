# Container Architecture - System Breakdown

This document explains the structural design of the **AutoBid** ecosystem. It describes how each "container" (service or database) works and how they communicate to ensure a fast, secure, and reliable auction experience.

---

## 🏢 Architectural Overview

Imagine the **AutoBid** system as a high-end **Auction House**. Instead of one person doing everything, we have specialized departments. This prevents the whole business from stopping if one person gets sick and allows us to hire more people for the busiest departments (like the bidding room) without overstaffing the quiet ones (como o cadastro de veículos).



---

## 1. The Entry Point (The Front Door)

* **User/Client (Web & Mobile):** These are the customers. They interact with the system using their phones or browsers.
* **HTTPS/WSS (The Language):**
    * **HTTPS:** Secure letters sent back and forth.
    * **WSS (WebSocket):** A "live phone call." It stays open so the system can tell the user instantly when someone else places a higher bid without the user having to refresh the page.
* **API Gateway (The Receptionist):**
    * Every single request hits the Receptionist first.
    * **Role:** It checks if the user has a valid ID and directs them to the right room (**Microservice**). It also protects the "rooms" inside from being overwhelmed by too many people at once.

---

## 2. The Departments (Microservices)

Each microservice is a "specialist" that handles one specific part of the business.

* **ms-identity (The Security Guard):** Handles logins and registrations. It issues a **JWT Token** (a digital badge) that proves who you are and what you are allowed to do.
* **ms-vehicle (The Garage Manager):** Manages the car catalog. It talks to the **BrasilAPI (FIPE)**—an external consultant—to find out the market value of a car based on its license plate.
* **ms-auction (The Auctioneer):** This is the busiest room. It handles the actual bidding. It has to be incredibly fast to process hundreds of bids per second.
* **ms-financial (The Bank Accountant):** Manages user wallets. It is very strict. It ensures that if you bid **$50,000**, you actually have that money, and it "locks" that amount so you don't spend it elsewhere while the auction is live.

---

## 3. The Memory (Persistence & Infrastructure)

Each department has its own way of storing information, depending on what they need.

### Databases & Cache

| Component | Type | Why? |
| :--- | :--- | :--- |
| **PostgreSQL** | The Steel Safe | Used for **Financial**, **User** and **Vehicle** data. Follows **ACID** rules (100% reliable). |
| **MongoDB** | The High-Speed Notebook | Used for **Auction Logs**. Built for speed and writing huge amounts of **NoSQL** data quickly. |
| **Redis** | The Sticky Notes | **In-Memory** storage. Uses a **Distributed Lock** to prevent two people from winning the same auction at the same price. |

### Message Broker

* **RabbitMQ (The Post Office):** This handles **Asynchronous Communication**.
    * **Example:** When an auction ends, the Auctioneer doesn't wait for the Accountant to finish the paperwork. He just drops a message in the RabbitMQ mailbox: *"Auction #10 ended, User Beatriz won."* The Accountant picks it up and processes it whenever he is ready. This keeps the system from getting "clogged."

---

## 📊 Visual Representation

<p align="center">
  <img src="images\autobid-containers-diagram-mermaid.png" alt="Containers Diagram">
  <br>
  <em>Figure 1: AutoBid Containers Diagram generated via Mermaid.</em>
</p>

---

## 🔄 Revision History

| Date | Name | Observation |
| :--- | :--- | :--- |
| 2026-02-17 | Gemini Pro & Taynara Vitorino | Initial document creation via AI and technical review of requirements. |
| | | |