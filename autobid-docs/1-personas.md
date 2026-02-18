# Personas & User Profiles

To understand the needs of the **AutoBid** platform, we have defined three primary personas that represent our core user base.

## 👤 Marcos (The Seller)
* **Role:** Casual or Professional Seller.
* **Goal:** To sell his vehicles quickly and for the best possible market price without bureaucratic hurdles.
* **Pain Point:** Spending too much time filling out technical forms and worrying about payment security.
* **System Interaction:** Uses `ms-vehicle` to list cars and `ms-notification` to track auction progress.

## 👤 Beatriz (The Buyer/Bidder)
* **Role:** Vehicle Enthusiast or Reseller.
* **Goal:** To find good deals and win auctions through a fair and transparent process.
* **Pain Point:** Losing an auction to "snipers" (last-second bots) or experiencing high latency in bid updates.
* **System Interaction:** Uses `ms-auction` for real-time bidding and `ms-financial` to manage her virtual wallet.

## 👤 Roberto (The Administrator)
* **Role:** Platform Manager.
* **Goal:** To ensure the health of the marketplace, audit suspicious bids, and manage auction schedules.
* **Pain Point:** Lack of visibility into system logs or difficulty in intervening in fraudulent auctions.
* **System Interaction:** Accesses administrative endpoints across all microservices and monitors health via `ms-identity` roles.