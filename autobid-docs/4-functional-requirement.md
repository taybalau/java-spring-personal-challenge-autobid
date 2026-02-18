# Functional Requirements (User Stories)

## Module 0: Identity and Access (`ms-identity`)

### US-00: Centralized Authentication (Login)
**As a** Platform User (Seller, Bidder, or Admin),  
**I would like to** log in with my email and password,  
**So that** I receive a secure access token and the system identifies who I am and my permission profile.

**Acceptance Criteria:**
1.  **Token Generation:** The system must return a valid **JWT Token** (JSON Web Token) after credential validation.
2.  **Token Content (Claims):** The token must contain:
    * `sub`: User ID.
    * `email`: User email.
    * `roles`: List of profiles (e.g., `["ROLE_BIDDER"]` or `["ROLE_ADMIN"]`).
3.  **Security:** Passwords must travel encrypted (HTTPS) and be validated against a stored hash (never plain text).
4.  **Temporary Lock:** After 5 consecutive failed attempts, the account must be locked for 15 minutes to prevent brute force attacks.

### US-01: New User Registration (Sign Up)
**As a** Visitor,  
**I would like to** create an account on the platform by providing my basic details (Name, CPF/Tax ID, Email, and Password),  
**So that** I can start participating in auctions.

**Acceptance Criteria:**
1.  **Uniqueness:** The system must not allow two registrations with the same CPF/Tax ID or Email.
2.  **Password Validation:** The password must be at least 8 characters long, containing uppercase letters, lowercase letters, and numbers.
3.  **Default Profile:** Every new user is created with the `ROLE_USER` profile (cannot access administrative panels).

---

## Module 1: Catalog and Vehicles (`ms-vehicle`)

### US-02: Intelligent Vehicle Registration
**As a** Seller (Marcos),  
**I would like to** provide only the license plate or vehicle model when creating an ad,  
**So that** the system automatically fills in technical data and market value, saving my time.

**Acceptance Criteria:**
1.  The system must consult an external API (BrasilAPI/FIPE) using the provided license plate/model.
2.  Mandatory fields to be automatically filled: Brand, Model, Manufacture Year, Model Year, and Reference Value.
3.  The initial auction value cannot be less than 50% of the returned FIPE Table value.
4.  If the external API is offline, allow manual filling with a "Pending Validation" warning.

### US-03: Photo Gallery
**As a** Seller (Marcos),  
**I would like to** upload photos of my vehicle,  
**So that** buyers can evaluate its condition.

**Acceptance Criteria:**
1.  Allow upload of up to 10 images (JPG/PNG).
2.  Each image must not exceed 5MB.
3.  The system must reject files that are not images.

---

## Module 2: Auction Room (`ms-auction`)

### US-04: Auction Creation
**As an** Admin (Roberto),  
**I would like to** schedule an auction for a registered vehicle,  
**So that** it becomes available for bidding at a future date.

**Acceptance Criteria:**
1.  Must define Start Date/Time and End Date/Time.
2.  The initial status must be `SCHEDULED`.
3.  It is not permitted to create an auction for a vehicle that already has an active or completed "Sold" auction.

### US-05: Place a Bid (Bidding)
**As a** Bidder (Beatriz),  
**I would like to** place a bid on an active auction,  
**So that** I can try to win the vehicle.

**Acceptance Criteria:**
1.  The auction must have the status `IN_PROGRESS`.
2.  The bid value must be greater than: (Current Bid + Minimum Increment).
3.  **Dynamic Increment Rule:**
    * Value < R$ 20k: Increment R$ 100.
    * Value < R$ 50k: Increment R$ 500.
    * Value >= R$ 50k: Increment R$ 1,000.
4.  The user cannot outbid their own current bid (auto-bid).
5.  The user must have an available balance in their Wallet.

### US-06: Anti-Sniping Protection (Automatic Extension)
**As a** Bidder (Beatriz),  
**I would like** the auction to be extended if a bid occurs at the very last moment,  
**So that** I have a chance to react and don't lose to "last-second" bots.

**Acceptance Criteria:**
1.  If a valid bid is registered with less than 2 minutes remaining:
    * Add +2 minutes to the closing time.
2.  Notify all participants via WebSocket about the new closing time.
3.  There is no maximum limit on extensions (the auction continues as long as there is active bidding).

### US-07: Real-Time Monitoring
**As a** Bidder (Beatriz),  
**I would like to** see the updated auction value without reloading the page,  
**So that** I know immediately if I have been outbid.

**Acceptance Criteria:**
1.  Use WebSocket to transmit the `NEW_BID` event.
2.  Visual latency must be less than 1 second after the bid is processed.
3.  Display on screen: Current Value, Winning User's Name (Masked: "Bia***"), and Remaining Time.

---

## Module 3: Financial (`ms-financial`)

### US-08: Wallet Management (Deposit)
**As a** Bidder (Beatriz),  
**I would like to** add balance to my virtual wallet,  
**So that** I have funds to participate in auctions.

**Acceptance Criteria:**
1.  Simulate value input (Deposit via Mock PIX).
2.  The balance must be reflected immediately in the "Available Balance" field.

### US-09: Funds Locking (Escrow)
**As a** System (AutoBid Bot),  
**I would like to** lock the bid amount in the user's wallet at the time of the offer,  
**So that** the platform has a guarantee that the user can pay if they win.

**Acceptance Criteria:**
1.  Upon receiving a bid intention of R$ X:
    * Validate if `Available Balance` >= R$ X.
    * Debit R$ X from `Available Balance`.
    * Credit R$ X to `Locked Balance`.
2.  If there is insufficient balance, the bid must be rejected immediately (Error 402 Payment Required).

### US-10: Automatic Unlocking (Outbid)
**As a** System (AutoBid Bot),  
**I would like to** return User A's money when User B outbids them,  
**So that** User A can use that money to make a new offer.

**Acceptance Criteria:**
1.  Event: User B outbids User A.
2.  Action for User A:
    * Debit the old bid value from `Locked Balance`.
    * Credit the old bid value to `Available Balance`.
3.  This operation must occur in less than 500ms to allow for a "counter-attack."

---

## Module 4: Closing (`ms-auction` & `ms-notification`)

### US-11: Closing and Winner Declaration
**As a** System (AutoBid Bot),  
**I would like to** automatically finalize the auction when the time expires,  
**So that** the winner is declared and the payment process begins.

**Acceptance Criteria:**
1.  The system must not accept bids with a timestamp later than the end time.
2.  Change auction status to `AWAITING_PAYMENT`.
3.  Send an email to the winner with instructions.
4.  The winning bid amount remains in `Locked Balance` until final (accounting) settlement.

---

## Module 5: Queries and Transparency

### US-12: Vehicle Detail Query
**As a** Platform User,  
**I would like to** view the full details of a vehicle (Make, Model, Year, FIPE, and Photos),  
**So that** I can make an informed decision before bidding.

**Acceptance Criteria:**
1.  The system must return all technical data retrieved via BrasilAPI/FIPE.
2.  The photo gallery must be displayed with all images uploaded by the seller.
3.  Must indicate if the vehicle is already associated with an active or scheduled auction.

### US-13: Active Auction Listing
**As a** Bidder (Beatriz),  
**I would like to** view a list of all auctions that are `IN_PROGRESS`,  
**So that** I can choose which ones to participate in.

**Acceptance Criteria:**
1.  The list must display: Main photo, Vehicle Name, Current Bid, and Remaining Time.
2.  The update of the "Current Bid" in the listing must follow the real-time rule via WebSocket whenever possible.
3.  Must allow filters by brand or price range.

### US-14: Wallet Balance Query
**As a** User,  
**I would like to** view my available balance and my locked balance,  
**So that** I have control over my financial bidding capacity.

**Acceptance Criteria:**
1.  Clearly display the distinction between `Available Balance` and `Locked Balance` (Escrow).
2.  List the recent history of deposits and bid locks.

---

## 🔄 Revision History

| Date | Name | Observation |
| :--- | :--- | :--- |
| 2026-02-17 | Gemini Pro & Taynara Vitorino | Initial document creation via AI and technical review of requirements. |