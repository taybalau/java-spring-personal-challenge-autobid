# Functional Requirements (User Stories)

## Module 0: Identity and Access (`ms-identity` or Keycloak)

### US-00: Centralized Authentication (Login)
**As a** Platform User (Seller, Buyer, or Admin),  
**I would like to** log in with my email and password,  
**So that** I receive a secure access token and the system knows who I am and what my permission profile is.

**Acceptance Criteria:**
1.  **Token Generation:** The system must return a valid **JWT Token** (JSON Web Token) after validating the credentials.
2.  **Token Content (Claims):** The token must contain:
    * `sub`: User ID.
    * `email`: User email.
    * `roles`: List of roles (e.g., `["ROLE_BIDDER"]` or `["ROLE_ADMIN"]`).
3.  **Security:** The password must be transmitted encrypted (HTTPS) and validated against a stored hash (never plain text).
4.  **Temporary Lockout:** After 5 consecutive failed attempts, the account must be locked for 15 minutes to prevent brute force.

### US-01: New User Registration (Sign Up)
**As a** Visitor,  
**I would like to** create an account on the platform by providing my basic data (Name, Tax ID/CPF, Email, and Password),  
**So that** I can start participating in the auctions.

**Acceptance Criteria:**
1.  **Uniqueness:** The system cannot allow two registrations with the same CPF or Email.
2.  **Password Validation:** The password must have at least 8 characters, containing uppercase letters, lowercase letters, and numbers.
3.  **Default Profile:** Every new user starts with the `ROLE_USER` profile (cannot access administrative panels).

## Module 1: Catalog and Vehicles (`ms-vehicle`)

### US-02: Smart Vehicle Registration
**As a** Seller (Marcos),  
**I would like to** provide only the license plate or model of the vehicle when creating an ad,  
**So that** the system automatically fills in the technical data and market value, saving me time.

**Acceptance Criteria:**
1.  The system must consult the external API (BrasilAPI/FIPE) using the provided license plate/model.
2.  Mandatory fields filled automatically: Brand, Model, Manufacture Year, Model Year, and Reference Value.
3.  The starting value of the auction cannot be lower than 50% of the returned FIPE Table value.
4.  If the external API is down, allow manual filling with a "Pending Validation" warning.

### US-03: Photo Gallery
**As a** Seller (Marcos),  
**I would like to** upload photos of my vehicle,  
**So that** buyers can evaluate the condition.

**Acceptance Criteria:**
1.  Allow upload of up to 10 images (JPG/PNG).
2.  Each image cannot exceed 5MB.
3.  The system must reject files that are not images.

---

## Module 2: Auction Room (`ms-auction`)

### US-04: Auction Creation
**As an** Admin (Roberto),  
**I would like to** schedule an auction for a registered vehicle,  
**So that** it becomes available for bidding at a future date.

**Acceptance Criteria:**
1.  Start Date/Time and End Date/Time must be defined.
2.  The initial status must be `SCHEDULED`.
3.  It is not allowed to create an auction for a vehicle that already has an active auction or is finished as "Sold".

### US-05: Place Bid (Bidding)
**As a** Buyer (Beatriz),  
**I would like to** place a bid on an active auction,  
**So that** I can try to win the vehicle.

**Acceptance Criteria:**
1.  The auction must be with status `IN_PROGRESS`.
2.  The bid amount must be greater than: (Current Bid + Minimum Increment).
3.  **Dynamic Increment Rule:**
    * Value < R$ 20k: Increment R$ 100.
    * Value < R$ 50k: Increment R$ 500.
    * Value >= R$ 50k: Increment R$ 1.000.
4.  The user cannot outbid their own bid (self-bidding).
5.  The user must have available balance in the Wallet.

### US-06: Sniping Protection (Automatic Extension)
**As a** Buyer (Beatriz),  
**I would like** the auction to be extended if a bid occurs at the very end,  
**So that** I have a chance to react and not lose to "last-second" bots.

**Acceptance Criteria:**
1.  If a valid bid is registered with less than 2 minutes remaining:
    * Add +2 minutes to the closing time.
2.  Notify all participants via WebSocket about the new time.
3.  There is no maximum limit for extensions (the auction continues as long as there is a dispute).

### US-07: Real-Time Monitoring
**As a** Buyer (Beatriz),  
**I would like to** see the updated value of the auction without reloading the page,  
**So that** I know immediately if I have been outbid.

**Acceptance Criteria:**
1.  Use WebSocket to transmit the `NEW_BID` event.
2.  The visual latency must be less than 1 second after bid processing.
3.  Display on screen: Current Value, Winning User Name (Obfuscated: "Bia***"), and Time Remaining.

---

## Module 3: Financial (`ms-financial`)

### US-08: Wallet Management (Deposit)
**As a** Buyer (Beatriz),  
**I would like to** add balance to my virtual wallet,  
**So that** I have funds to participate in the auctions.

**Acceptance Criteria:**
1.  Simulate entry of funds (Deposit via Mock PIX).
2.  The balance must be reflected immediately in the "Available Balance" field.

### US-09: Fund Locking (Escrow)
**As a** System (AutoBid Bot),  
**I would like to** lock the bid amount in the user's wallet at the moment of the offer,  
**So that** the platform has a guarantee that the user can pay if they win.

**Acceptance Criteria:**
1.  Upon receiving a bid intent of R$ X:
    * Validate if `Available Balance` >= R$ X.
    * Debit R$ X from `Available Balance`.
    * Credit R$ X to `Locked Balance`.
2.  If there is no balance, the bid must be rejected immediately (Error 402 Payment Required).

### US-10: Automatic Unlocking (Outbid)
**As a** System (AutoBid Bot),  
**I would like to** return user A's money when user B outbids them,  
**So that** user A can use the money to make a new offer.

**Acceptance Criteria:**
1.  Event: User B outbid User A.
2.  Action on User A:
    * Debit old bid amount from `Locked Balance`.
    * Credit old bid amount to `Available Balance`.
3.  This operation must occur in less than 500ms to allow for a "counter-attack".

---

## Module 4: Closing (`ms-auction` & `ms-notification`)

### US-11: Closing and Winner Declaration
**As a** System (AutoBid Bot),  
**I would like to** finalize the auction automatically when time expires,  
**So that** the winner is declared and the payment process begins.

**Acceptance Criteria:**
1.  The system must not accept bids with a timestamp greater than the end time.
2.  Change auction status to `AWAITING_PAYMENT`.
3.  Send email to the winner with instructions.
4.  The winning bid amount remains in `Locked Balance` until final effective settlement (accounting).

---

## 🔄 Revision History

| Date | Name | Observation |
| :--- | :--- | :--- |
| 2026-02-17 | Gemini Pro & Taynara Vitorino | Initial document creation via AI and technical review of requirements. |
| | | |