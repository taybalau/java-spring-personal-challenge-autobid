# API Contract Documentation

This document defines the primary REST endpoints for the **AutoBid** platform. All requests and responses use `application/json` unless stated otherwise.

---

## 🔐 1. Identity Service (`ms-identity`)
Responsible for user access and "digital ID badges" (Tokens).

| Method | Endpoint | Auth | Description | User Story |
| :--- | :--- | :--- | :--- | :--- |
| **POST** | `/api/auth/register` | Public | Register a new user (Buyer/Seller). | US-01 |
| **POST** | `/api/auth/login` | Public | Authenticates credentials and returns a JWT Token. | US-00 |

## 🚗 2. Vehicle Service (`ms-vehicle`)
Manages the inventory and technical data of the cars.

| Method | Endpoint | Auth | Description | User Story |
| :--- | :--- | :--- | :--- | :--- |
| **POST** | `/api/vehicles` | `ROLE_SELLER` | Creates a vehicle record. Auto-fills FIPE data via external API. | US-02 |
| **POST** | `/api/vehicles/{id}/photos` | `ROLE_SELLER` | Uploads up to 10 images for a specific vehicle. | US-03 |
| **GET** | `/api/vehicles/{id}` | User | Retrieves full details and technical data of a specific vehicle. | US-12 |

## 🔨 3. Auction Service (`ms-auction`)
The core engine for live bidding and real-time updates.

| Method | Endpoint | Auth | Description | User Story |
| :--- | :--- | :--- | :--- | :--- |
| **POST** | `/api/auctions` | `ROLE_ADMIN` | Schedules a new auction for a registered vehicle. | US-04 |
| **GET** | `/api/auctions/active` | User | Lists all auctions currently in the `ACTIVE` state. | US-13 |
| **POST** | `/api/auctions/{id}/bids` | `ROLE_USER` | Places a new bid. Triggers the Redis lock and financial check. | US-05 |
| **WS** | `/ws/auctions` | User | **WebSocket** connection for real-time price and time updates. | US-07 |

## 💰 4. Financial Service (`ms-financial`)
The "Bank" that handles the money and the ledger.

| Method | Endpoint | Auth | Description | User Story |
| :--- | :--- | :--- | :--- | :--- |
| **POST** | `/api/wallets/deposit` | `ROLE_USER` | Simulates a deposit (Mock PIX) to increase available balance. | US-08 |
| **GET** | `/api/wallets/balance` | `ROLE_USER` | Returns current `available_balance` and `locked_balance`. | US-14 |

---

## 🛠️ Global Standards & Errors

### Security
* Every request to protected endpoints must include the following header: `Authorization: Bearer <YOUR_JWT_TOKEN>`.
* Passwords are never returned in responses and are stored using strong hashing (BCrypt).

### Common Status Codes
* **200 OK:** Success.
* **201 Created:** Resource (bid, vehicle, or auction) created successfully.
* **401 Unauthorized:** Missing or invalid JWT token.
* **402 Payment Required:** Returned when `ms-financial` reports insufficient funds for a bid.
* **403 Forbidden:** The user has a token but lacks the required Role (e.g., Buyer trying to create a vehicle).
* **429 Too Many Requests:** Triggered by the API Gateway's rate limiter during high traffic.

---

## 🔄 Revision History

| Date | Author | Description |
| :--- | :--- | :--- |
| 2026-02-17 | Gemini Pro & Taynara Vitorino | Initial API Contract design based on User Stories and NFRs. |