# Sequence Diagram: Placing a Bid

This document details the step-by-step interaction between microservices when a user places a bid. It highlights the orchestration between high-performance bidding and financial integrity.

## 📊 Visual Representation

The following diagram illustrates the "Happy Path" where a buyer (Beatriz) places a successful bid.

<p align="center">
  <img src="images\autobid-sequence-diagram-mermaid.png" alt="Containers Diagram">
  <br>
  <em>Figure 6: AutoBid Containers Diagram generated via Mermaid.</em>
</p>

## 🔍 Key Technical Steps
### 1. Request Routing & Security
The API Gateway acts as the first line of defense, validating the user's identity via JWT claims before the request ever reaches the bidding engine.

### 2. Distributed Locking
To prevent Race Conditions (where two bids at the same price could be accepted), the system uses Redis to implement a distributed lock. Only one request per auction ID is processed at any given millisecond.

### 3. Synchronous Escrow (Locking Funds)
Before a bid is officially accepted, ms-auction performs a Synchronous (Sync) call to ms-financial. This ensures the user has sufficient "Available Balance". The funds are moved to a "Locked" state immediately to guarantee payment if the user wins.

### 4. Real-time Feedback
Once the database and cache are updated, the system uses WebSockets to push the update to all connected clients. This ensures Beatriz and all other participants see the new price in less than 200ms.

---

## 🔄 Revision History

| Date | Author | Description |
| :--- | :--- | :--- |
| 2026-02-17 | Gemini Pro & Taynara Vitorino | Initial creation of Data Model documentation and explanation of the Foreign Key Dilemma. |
| | | |