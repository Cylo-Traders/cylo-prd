# Agrocylo — Product Requirements Document (PRD)

**Version:** 1.0.0
**Status:** Draft
**Last Updated:** March 2026
**Author:** Agrocylo Core Team

---

## Table of Contents

1. [Executive Summary](#1-executive-summary)
2. [Problem Statement](#2-problem-statement)
3. [Product Vision and Goals](#3-product-vision-and-goals)
4. [Target Audience](#4-target-audience)
5. [System Architecture](#5-system-architecture)
6. [Smart Contract Specification](#6-smart-contract-specification)
7. [Backend Specification](#7-backend-specification)
8. [Frontend Specification](#8-frontend-specification)
9. [Starkzap SDK Integration](#9-starkzap-sdk-integration)
10. [Security Requirements](#10-security-requirements)
11. [Performance Requirements](#11-performance-requirements)
12. [Success Metrics](#12-success-metrics)
13. [Release Plan](#13-release-plan)
14. [Open Questions and Risks](#14-open-questions-and-risks)

---

## 1. Executive Summary

Agrocylo is an Agro-DeFi marketplace built on Starknet that enables direct, trustless trade between farmers and consumers. It eliminates agricultural middlemen through programmable escrow settlement, peer-to-peer order management, and native DeFi access via the Starkzap toolkit.

Every transaction on Agrocylo is secured by a Cairo smart contract deployed on Starknet. Funds are locked on-chain at order creation and released only after the buyer confirms receipt — guaranteeing protection for both parties without any custodial risk to the platform.

**Platform tagline:** *Fair trade. On-chain. For every farmer.*

---

## 2. Problem Statement

### 2.1 The Agricultural Value Chain is Broken

Small-scale and urban farmers in emerging markets operate in a system structurally biased against them:

| Problem | Impact on Farmers | Impact on Consumers |
|---|---|---|
| Middlemen capture 40–60% of margins | Farmers earn below fair value | Consumers pay above fair price |
| Limited market access | Produce sold locally at low prices | Limited product diversity |
| No trusted payment infrastructure | Delayed or failed payments | Risk of non-delivery after payment |
| Post-harvest losses | Up to 30% of produce lost | Supply inconsistency and price volatility |
| Lack of digital tools | No demand forecasting, no price discovery | No traceability or quality guarantees |

### 2.2 Why Existing Solutions Fall Short

- **Traditional e-commerce platforms** take commissions (10–30%), are custodial, and have no on-chain settlement guarantees.
- **Agricultural cooperatives** improve access but still rely on trusted intermediaries and are geographically constrained.
- **General crypto payment apps** lack the domain-specific features (order management, escrow, dispute resolution) that agricultural trade requires.

### 2.3 The Agrocylo Thesis

Blockchain-based escrow, combined with a consumer-grade marketplace interface, can replicate the trust guarantees that middlemen historically provided — while eliminating the economic extraction they represent. Agrocylo replaces trust in institutions with trust in code.

---

## 3. Product Vision and Goals

### 3.1 Vision Statement

> *To become the foundational trading infrastructure for agricultural commerce in Africa and emerging markets, enabling every farmer to access global markets and every consumer to buy directly from the source — with settlement guaranteed by code, not contracts.*

### 3.2 Product Goals

**G1 — Trustless Settlement**
Every payment on the platform is secured by a non-custodial on-chain escrow. Neither party can be defrauded if they follow the protocol.

**G2 — Market Accessibility**
A farmer with a smartphone and a Starknet wallet can list products, receive orders, and get paid — without a bank account, a physical storefront, or a broker.

**G3 — DeFi Integration**
Through the Starkzap toolkit, farmers and consumers can access native DeFi services — staking, yield on idle escrow funds, and BTC-fi — directly from the platform.

**G4 — Data-Driven Agriculture**
Aggregated on-chain and off-chain order data powers price discovery tools and demand/supply dashboards that help farmers make production decisions.

**G5 — Financial Inclusion**
Platform fees are minimal (3%) and transparent. The platform never holds private keys. Users retain full custody at all times.

---

## 4. Target Audience

### 4.1 Primary Users

**Farmers / Producers**

| Attribute | Detail |
|---|---|
| Profile | Individual smallholder farmers, urban farmers, farming cooperatives |
| Geography | Nigeria, Ghana, Kenya, and broader Sub-Saharan Africa (Phase 1) |
| Technical level | Non-technical — guided wallet setup, mobile-first interface |
| Motivation | Higher income, broader market access, reliable payment |
| Wallet | Argent X, Braavos (Starknet) |
| Pain point | Uncertainty of payment, limited buyers, middleman extraction |

**Consumers / Buyers**

| Attribute | Detail |
|---|---|
| Profile | Individuals, urban households, small retailers, food businesses |
| Geography | Same as farmers, with potential for diaspora buyers (Phase 2) |
| Technical level | Non-technical to blockchain-aware |
| Motivation | Lower prices, fresh produce, direct farmer relationship |
| Wallet | Argent X, Braavos |
| Pain point | Trust in delivery, quality uncertainty, price opacity |

### 4.2 Secondary Stakeholders

- **Platform operators** — Analytics, monitoring, dispute support, fee collection
- **NGOs and cooperatives** — Farmer onboarding programs and subsidy distribution
- **Government agencies** — Agricultural data and market monitoring
- **DeFi protocols** — Integrated via Starkzap for yield and liquidity products

### 4.3 User Personas

**Persona A — Amara (Farmer)**
Amara grows tomatoes and peppers on 2 acres outside Lagos. She currently sells through a market middleman who takes 35% of her revenue. She has a smartphone and recently set up an Argent X wallet through a cooperative program. She needs a simple interface to list her products, receive orders, and get paid in USDC without touching the terminal.

**Persona B — Chidi (Buyer)**
Chidi runs a small restaurant in Abuja and spends NGN 200,000/month on produce. He buys from a market vendor but suspects he is overpaying. He is comfortable with mobile apps and has used a crypto wallet before. He wants to buy directly from farmers, get better prices, and have delivery guaranteed before releasing payment.

---

## 5. System Architecture

### 5.1 High-Level Architecture

```
┌─────────────────────────────────────────────────────┐
│                   CLIENT LAYER                       │
│         Next.js Web App  /  React Native App         │
│    starknet-react · Starkzap SDK · Leaflet.js        │
└────────────────────────┬────────────────────────────┘
                         │ HTTPS / WebSocket
┌────────────────────────▼────────────────────────────┐
│                  BACKEND LAYER                       │
│            Node.js / Express API Server              │
│         Supabase (Postgres + Storage + Auth)         │
│      Event Indexer · Notification Service            │
└──────────┬───────────────────────┬──────────────────┘
           │ RPC                   │ Supabase Client
┌──────────▼──────────┐  ┌────────▼──────────────────┐
│   STARKNET LAYER    │  │      DATA LAYER            │
│  Escrow Contract    │  │  Supabase Postgres DB      │
│  (Cairo 2.8.4)      │  │  Supabase Storage (S3)     │
│  STRK / USDC ERC20  │  │  Redis (sessions/nonces)   │
└─────────────────────┘  └───────────────────────────┘
           │
┌──────────▼──────────┐
│   STARKZAP LAYER    │
│  DeFi Toolkit SDK   │
│  Staking · BTC-fi   │
│  Yield Products     │
└─────────────────────┘
```

### 5.2 Component Responsibilities

| Component | Responsibility |
|---|---|
| **Escrow Contract** | Locks funds, computes fees, manages order lifecycle, enforces settlement rules |
| **Backend API** | Profiles, products, cart, location data, order metadata, authentication |
| **Event Indexer** | Listens to on-chain events (`OrderCreated`, `OrderCompleted`, `OrderRefunded`), syncs to database |
| **Notification Service** | Delivers real-time order status updates via push, email, and in-app |
| **Frontend** | Wallet connection, map discovery, product browsing, cart, checkout, dashboards |
| **Starkzap SDK** | DeFi access layer — staking, BTC-fi, yield on idle escrow balances |

---

## 6. Smart Contract Specification

### 6.1 Overview

The escrow contract is the trust backbone of the platform. It is written in Cairo 2.8.4, deployed on Starknet, and structured as a single deployable contract with four internal modules.

| Property | Value |
|---|---|
| Language | Cairo 2.8.4 |
| Toolchain | Scarb 2.8.4, snforge 0.31.0, sncast 0.31.0 |
| Token Standard | ERC-20 (IERC20Dispatcher) |
| Platform Fee | 3% deducted at order creation |
| Order Expiry | 96 hours |
| Supported Tokens | STRK, USDC (configurable at initialization) |

### 6.2 Module Structure

```
src/
├── lib.cairo         — Storage, constructor, event emission, index management
├── types.cairo       — Order, OrderStatus, EscrowError, events, constants
├── interface.cairo   — IEscrow public ABI trait
└── escrow.cairo      — compute_fee(), build_order(), complete_order(), expire_order()
```

### 6.3 Storage Layout

```cairo
Storage {
    admin: ContractAddress,
    fee_collector: ContractAddress,
    initialized: bool,
    supported_tokens: Map<u32, ContractAddress>,
    supported_tokens_len: u32,
    order_count: u64,
    orders: Map<u64, Order>,
    buyer_orders_len: Map<ContractAddress, u64>,
    buyer_orders: Map<(ContractAddress, u64), u64>,
    farmer_orders_len: Map<ContractAddress, u64>,
    farmer_orders: Map<(ContractAddress, u64), u64>,
}
```

### 6.4 Order Data Model

```cairo
struct Order {
    buyer: ContractAddress,
    farmer: ContractAddress,
    token: ContractAddress,
    amount: u256,          // net amount (97% of gross)
    timestamp: u64,        // block timestamp at creation
    status: OrderStatus,   // Pending | Completed | Refunded
}
```

### 6.5 Fee Computation

```
fee_amount  = gross_amount * 3 / 100   (integer division, rounds down)
net_amount  = gross_amount - fee_amount

Invariant:  fee_amount + net_amount == gross_amount  (always)
```

Fee is deducted at order creation, sent immediately to `fee_collector`. It is non-refundable.

### 6.6 Function Specifications

| Function | Access | Description |
|---|---|---|
| `initialize(admin, tokens, fee_collector)` | Once only | Sets supported tokens and fee collector. Immutable post-call. |
| `create_order(buyer, farmer, token, amount)` | Buyer only (caller == buyer) | Locks funds, deducts fee, creates order record, updates indexes |
| `confirm_receipt(buyer, order_id)` | Buyer only (caller == buyer) | Releases net amount to farmer, marks Completed |
| `refund_expired_order(order_id)` | Permissionless | Refunds buyer if order is Pending and > 96h old |
| `refund_expired_orders(order_ids)` | Permissionless | Batch refund — atomic, all-or-nothing |
| `get_order_details(order_id)` | Public read | Returns full Order struct |
| `get_orders_by_buyer(buyer)` | Public read | Returns Array of order IDs |
| `get_orders_by_farmer(farmer)` | Public read | Returns Array of order IDs |
| `get_supported_tokens()` | Public read | Returns supported token addresses |
| `get_fee_collector()` | Public read | Returns fee collector address |
| `get_order_count()` | Public read | Returns total orders created |

### 6.7 Order State Machine

```
create_order()
      │
      ▼
 ┌─────────┐
 │ PENDING │
 └─────────┘
      │                    │
confirm_receipt()     timestamp + 96h elapsed
      │                    │
      ▼                    ▼
┌──────────┐         ┌──────────┐
│COMPLETED │         │ REFUNDED │
└──────────┘         └──────────┘
  (farmer              (buyer
 receives net)       receives net)
```

Both `COMPLETED` and `REFUNDED` are terminal states with no further transitions.

### 6.8 Events

| Event | Trigger | Indexed Fields |
|---|---|---|
| `OrderCreated` | `create_order` succeeds | `order_id` |
| `OrderCompleted` | `confirm_receipt` succeeds | `order_id` |
| `OrderRefunded` | `refund_expired_order` succeeds | `order_id` |

### 6.9 Error Codes

| Error | Condition |
|---|---|
| `AlreadyInitialized` | `initialize()` called more than once |
| `MustSupportTwoTokens` | Fewer than 2 tokens passed to initialize |
| `AmountMustBePositive` | Order amount is zero |
| `ContractNotInitialized` | Order created before initialize |
| `UnsupportedToken` | Token not in supported list |
| `OrderDoesNotExist` | Order ID out of range |
| `NotBuyer` | Caller is not order's buyer |
| `OrderNotPending` | Order already completed or refunded |
| `OrderNotExpired` | Less than 96h since order creation |

### 6.10 Test Coverage Requirements

| Test Category | Required Coverage |
|---|---|
| Fee computation unit tests | 100% — all edge cases including zero and rounding |
| Initialize guard tests | Double-init, insufficient tokens |
| Create order guard tests | Wrong caller, unsupported token, zero amount, uninitialized |
| Confirm receipt tests | Happy path, wrong caller, double confirm, nonexistent order |
| Refund tests | Before 96h (fails), after 96h (succeeds), post-confirm (fails) |
| Fee invariant test | fee + net == gross for all amounts |
| Integration tests | Full flow with mock ERC-20: create → confirm, create → expire → refund |

---

## 7. Backend Specification

### 7.1 Technology Stack

| Component | Technology |
|---|---|
| Runtime | Node.js 20 LTS |
| Framework | Express.js |
| Database | Supabase (Postgres 15) |
| Object Storage | Supabase Storage (S3-compatible) |
| Cache / Sessions | Redis (nonce storage, rate limiting) |
| Authentication | SIWS (Sign-In With Starknet) + JWT |
| Event Indexing | Custom Starknet event poller (starknet.js) |
| Notifications | Firebase Cloud Messaging + Resend (email) |
| API Documentation | OpenAPI 3.0 |

### 7.2 Database Schema

**`profiles`**
```sql
wallet_address   text PRIMARY KEY
role             text CHECK (role IN ('farmer', 'buyer'))
display_name     text NOT NULL
bio              text
avatar_url       text
created_at       timestamptz DEFAULT now()
updated_at       timestamptz DEFAULT now()
```

**`locations`**
```sql
wallet_address   text REFERENCES profiles PRIMARY KEY
latitude         double precision
longitude        double precision
geohash          text
city             text
country          text
is_public        boolean DEFAULT true
updated_at       timestamptz DEFAULT now()
```

**`products`**
```sql
id               uuid PRIMARY KEY
farmer_wallet    text REFERENCES profiles
name             text NOT NULL
category         text
price_per_unit   numeric(18,6) NOT NULL
currency         text CHECK (currency IN ('STRK','USDC'))
unit             text NOT NULL
stock_quantity   numeric(10,2)
image_url        text
is_available     boolean DEFAULT true
created_at       timestamptz DEFAULT now()
updated_at       timestamptz DEFAULT now()
```

**`carts`**
```sql
id               uuid PRIMARY KEY
buyer_wallet     text REFERENCES profiles
status           text CHECK (status IN ('active','checked_out','abandoned'))
created_at       timestamptz DEFAULT now()
updated_at       timestamptz DEFAULT now()
```

**`cart_items`**
```sql
id               uuid PRIMARY KEY
cart_id          uuid REFERENCES carts
product_id       uuid REFERENCES products
farmer_wallet    text NOT NULL
quantity         numeric(10,2) NOT NULL
unit_price       numeric(18,6) NOT NULL     -- price snapshot at add time
currency         text NOT NULL
```

**`orders_metadata`**
```sql
id                  uuid PRIMARY KEY
on_chain_order_id   bigint UNIQUE NOT NULL
buyer_wallet        text NOT NULL
farmer_wallet       text NOT NULL
description         text
items_snapshot      jsonb                   -- cart items at checkout time
created_at          timestamptz DEFAULT now()
```

**`notifications`**
```sql
id               uuid PRIMARY KEY
wallet_address   text REFERENCES profiles
type             text                        -- order_created, confirmed, refunded
payload          jsonb
read             boolean DEFAULT false
created_at       timestamptz DEFAULT now()
```

### 7.3 Authentication Flow

```
1. Client: POST /auth/nonce { wallet_address }
   Server: generate nonce, store in Redis (TTL 5 min), return { nonce }

2. Client: wallet signs message:
   "Sign in to Agrocylo\nWallet: {address}\nNonce: {nonce}\nTimestamp: {ts}"

3. Client: POST /auth/verify { wallet_address, signature, nonce }
   Server: verify Starknet signature → issue JWT (24h) + refresh token (7d)

4. Client: attach JWT as Bearer token on all write requests

5. Server middleware: verify JWT → extract wallet_address → authorize
```

Security properties:
- Nonce is single-use — invalidated immediately after verification
- Replay attacks impossible — nonce TTL + single-use
- JWT is stateless — no server-side session storage required

### 7.4 API Endpoints

**Auth**
```
POST   /auth/nonce
POST   /auth/verify
POST   /auth/refresh
DELETE /auth/logout
```

**Profiles**
```
GET    /profiles/:wallet
POST   /profiles
PATCH  /profiles/:wallet
```

**Locations**
```
GET    /locations/farmers
GET    /locations/farmers?lat=&lng=&radius=
POST   /locations
PATCH  /locations/:wallet
DELETE /locations/:wallet
```

**Products**
```
GET    /products
GET    /products/:id
GET    /products?farmer=:wallet&category=:cat
POST   /products
PATCH  /products/:id
DELETE /products/:id
POST   /products/:id/image
```

**Cart**
```
GET    /cart
POST   /cart/items
PATCH  /cart/items/:id
DELETE /cart/items/:id
DELETE /cart
POST   /cart/checkout
```

**Order Metadata**
```
POST   /orders/metadata
GET    /orders/metadata/:on_chain_order_id
```

**Notifications**
```
GET    /notifications
PATCH  /notifications/:id/read
DELETE /notifications/:id
```

### 7.5 Event Indexer

The indexer polls Starknet for contract events every 10 seconds and syncs state to Supabase.

```
Indexer loop:
  1. Fetch latest block number from RPC
  2. Query contract events since last indexed block
  3. For each OrderCreated event:
     - Insert into orders_metadata
     - Trigger push notification to farmer
  4. For each OrderCompleted event:
     - Update orders_metadata status
     - Trigger push notification to farmer (payment released)
  5. For each OrderRefunded event:
     - Update orders_metadata status
     - Trigger push notification to buyer (refund sent)
  6. Update last_indexed_block cursor
```

Reliability requirements:
- Idempotent — re-processing the same block produces no duplicate records
- Resumable — cursor persisted to database, survives restarts
- Alerting — Slack/email alert if indexer falls more than 50 blocks behind

---

## 8. Frontend Specification

### 8.1 Technology Stack

| Component | Technology |
|---|---|
| Framework | Next.js 14 (App Router) |
| Language | TypeScript |
| Wallet Integration | starknet-react v3 |
| Styling | Tailwind CSS |
| Map | react-leaflet + Leaflet.js + OpenStreetMap |
| State Management | Zustand |
| Data Fetching | TanStack Query (React Query) |
| Forms | React Hook Form + Zod |
| Notifications | react-hot-toast |

### 8.2 Page Structure

```
/                        — Landing page
/market                  — Product browsing + farmer map
/market/:farmer_wallet   — Farmer profile + product listings
/cart                    — Shopping cart + checkout flow
/orders                  — Buyer order dashboard
/dashboard               — Farmer dashboard
/dashboard/products      — Farmer product management
/dashboard/orders        — Farmer order management
/onboarding              — Wallet connect + profile setup
/settings                — Profile, location, notification preferences
```

### 8.3 Core Components

**`<WalletConnect />`**
- Supports Argent X and Braavos
- Displays truncated address and balance when connected
- SIWS sign-in on first connect

**`<FarmerMap />`**
- Leaflet.js map centered on buyer location (with fallback)
- Green farmer pins with `leaflet.markercluster`
- Farmer popup: name, distance, bio, "View Profile" / "Add to Cart" CTAs
- Distance filter: 10km / 25km / 50km / 100km / All

**`<ProductCard />`**
- Image thumbnail, name, price per unit, unit, availability
- Quantity stepper (+ / -)
- "Add to Cart" button with optimistic update

**`<CartDrawer />`**
- Accessible from navbar on all pages
- Items grouped by farmer with subtotals
- Fee breakdown: gross → 3% fee → net per farmer group
- "Checkout" CTA

**`<CheckoutFlow />`**
- Step 1: Cart review with full fee breakdown
- Step 2: Sequential multi-farmer order creation
  - One `approve` + one `create_order` per farmer group
  - Wallet prompt with clear labelling per transaction
  - Progress: "Creating order 1 of 2..."
- Step 3: Confirmation with on-chain order IDs

**`<OrderCard />`**
- Order ID, farmer/buyer name, amount, status badge, timestamp
- Countdown timer for Pending orders (time to 96h expiry)
- "Confirm Receipt" button (buyer only, Pending orders only)

**`<FarmerDashboard />`**
- Incoming orders list with status
- Product management (add, edit, toggle availability, delete)
- Revenue summary (total earned, pending in escrow, total orders)

### 8.4 Wallet Integration Requirements

- Wallet connection must work with Argent X and Braavos on Sepolia and Mainnet
- All contract write calls (create_order, confirm_receipt) must go through starknet-react's `useContract` + `useContractWrite`
- Transaction status must be polled and displayed to user: `Pending → Confirmed / Failed`
- Insufficient balance must be detected before signing — show error before wallet prompt
- ERC-20 `approve` must be submitted and confirmed before `create_order` — sequential, not parallel

### 8.5 UX Requirements

- Mobile-first responsive design — all flows usable on 375px viewport
- No blockchain jargon exposed to non-technical users:
  - "Confirm Receipt" not "call confirm_receipt()"
  - "Payment Secured" not "funds locked in escrow"
  - "Order ID" not "on-chain order ID"
- All loading states must have skeletons — no blank screens
- All error states must have human-readable messages and recovery CTAs
- Lighthouse scores: Performance ≥ 85, Accessibility ≥ 90, Best Practices ≥ 90

---

## 9. Starkzap SDK Integration

### 9.1 What is Starkzap

Starkzap (`starkzap.io`) is a DeFi toolkit for Starknet that provides access to native financial products including token staking, BTC-fi, and yield generation. Agrocylo integrates Starkzap to provide farmers and consumers with access to DeFi services directly from the platform — without leaving the app or interacting with external protocols manually.

### 9.2 Integration Points

**A. Token Staking for Farmers**

Farmers who have received payments into their wallet can stake STRK directly from their dashboard via the Starkzap SDK. This provides yield on idle funds between market cycles.

```
Farmer receives 970 STRK (order confirmed)
       ↓
Farmer dashboard shows "Earn yield on your STRK" CTA
       ↓
Starkzap SDK: stake(amount, token, duration)
       ↓
STRK deposited into Starkzap staking contract
       ↓
Farmer earns staking rewards passively
```

**B. BTC-fi for Buyers**

Buyers can deposit BTC (via wrapped BTC on Starknet) and use BTC-fi products offered through Starkzap — earning yield while their funds are not actively in escrow.

**C. Yield on Idle Cart Balance (Future)**

When a buyer has funds approved but not yet committed to an order, Starkzap can route idle approved balances into a short-duration yield product, returning funds to the buyer with yield on checkout.

### 9.3 SDK Integration Requirements

- Starkzap SDK must be initialized with the connected wallet on login
- All Starkzap transactions must use the same starknet-react wallet provider as the escrow contract — no separate wallet connection required
- Starkzap features are presented as optional enhancements — platform is fully functional without them
- SDK errors must not propagate to or affect core escrow functionality
- Starkzap UI components rendered inside Agrocylo must match Agrocylo's design system

### 9.4 SDK Interface (Expected)

```typescript
import { StarkzapClient } from '@starkzap/sdk';

const starkzap = new StarkzapClient({
  provider: starknetReactProvider,
  network: 'sepolia' | 'mainnet',
});

// Staking
await starkzap.staking.stake({ token: 'STRK', amount: '970000000000000000000' });
await starkzap.staking.unstake({ token: 'STRK', amount: '970000000000000000000' });
await starkzap.staking.getRewards({ wallet: address });

// BTC-fi
await starkzap.btcfi.deposit({ amount: '0.01' });
await starkzap.btcfi.withdraw({ amount: '0.01' });
await starkzap.btcfi.getPosition({ wallet: address });
```

> Note: Exact SDK interface to be confirmed with Starkzap team. This spec assumes a client-side SDK with async/await interface over starknet-react wallet provider.

---

## 10. Security Requirements

### 10.1 Smart Contract Security

| Requirement | Implementation |
|---|---|
| No reentrancy risk | State updated before external calls (checks-effects-interactions) |
| Caller authentication | `get_caller_address() == buyer` enforced on all write functions |
| Immutable fee config | `fee_collector` and `supported_tokens` cannot change post-init |
| No integer overflow | Cairo's native u256 handles large token amounts safely |
| Permissionless refund | Expired orders refundable by anyone — protects inactive buyers |
| Formal audit | Required before Mainnet deployment with real funds |

### 10.2 Backend Security

| Requirement | Implementation |
|---|---|
| SIWS authentication | Wallet signature verification on every session |
| Nonce replay prevention | Nonces are single-use with 5-minute TTL |
| JWT expiry | Access tokens: 24h, Refresh tokens: 7d |
| Rate limiting | 100 req/min per IP on public endpoints, 10 req/min on auth endpoints |
| Row Level Security | Supabase RLS on all tables — users can only access their own data |
| Input validation | Zod schema validation on all request bodies |
| SQL injection | Parameterized queries via Supabase client — no raw SQL |
| HTTPS only | All API endpoints served over TLS 1.3 |
| Secret management | All secrets in environment variables — never in code |

### 10.3 Frontend Security

| Requirement | Implementation |
|---|---|
| No private key handling | Platform is fully non-custodial — wallets never expose keys |
| XSS prevention | React's default escaping + strict Content Security Policy |
| CSRF protection | JWT Bearer tokens (not cookies) — immune to CSRF |
| Dependency scanning | Automated via GitHub Dependabot |
| Environment variables | All API keys in `.env.local` — never committed |

---

## 11. Performance Requirements

| Metric | Target |
|---|---|
| Page load time (LCP) | < 2.5 seconds on 4G mobile |
| Farmer map load | < 2 seconds for up to 1,000 farmer pins |
| API response time (p95) | < 300ms for all read endpoints |
| Proximity search | < 300ms for farmers within radius (10,000 rows) |
| Event indexer lag | < 30 seconds from on-chain event to database |
| Contract call round-trip | < 5 seconds from wallet signature to confirmed transaction |
| Uptime | 99.5% for API and frontend (excluding Starknet network downtime) |

---

## 12. Success Metrics

### 12.1 Adoption Metrics

| Metric | Phase 1 Target | Phase 2 Target |
|---|---|---|
| Registered farmers | 50 | 500 |
| Registered buyers | 200 | 2,000 |
| Completed orders | 100 | 2,000 |
| Active farmers (weekly) | 20 | 200 |

### 12.2 Financial Metrics

| Metric | Description |
|---|---|
| Total Value Locked (TVL) | Sum of all funds currently in Pending escrow orders |
| Total Volume | Cumulative gross value of all completed orders |
| Average Order Value | Total volume / number of orders |
| Platform Revenue | Total fees collected (3% × total volume) |

### 12.3 Reliability Metrics

| Metric | Target |
|---|---|
| Successful settlement rate | > 95% of orders reach Completed or Refunded |
| Refund rate | < 20% (high refund rate indicates delivery problems) |
| Contract revert rate | < 1% of on-chain transactions |
| Indexer uptime | > 99% |

### 12.4 User Satisfaction Metrics

| Metric | Target |
|---|---|
| Onboarding completion rate | > 70% of wallets connected complete profile setup |
| Checkout completion rate | > 60% of carts proceed to confirmed on-chain order |
| Net Promoter Score (NPS) | > 40 (Phase 2) |
| Support tickets per 100 orders | < 5 |

---

## 13. Release Plan

### Phase 1 — MVP (Weeks 1–4)

**Smart Contract**
- [x] Escrow contract fully implemented and tested
- [x] Deployed to Starknet Sepolia
- [ ] Formal security review (internal)

**Backend**
- [ ] Supabase schema: profiles, locations, orders_metadata
- [ ] SIWS authentication
- [ ] Profile and location API endpoints
- [ ] Basic event indexer (OrderCreated, OrderCompleted, OrderRefunded)

**Frontend**
- [ ] Wallet connection (Argent X, Braavos)
- [ ] Onboarding flow (role, profile, location)
- [ ] Farmer map with pins and proximity filter
- [ ] Basic order creation flow (single farmer, single token)
- [ ] Buyer order dashboard with confirm receipt

**Milestone:** End-to-end escrow flow working — buyer creates order, confirms receipt, farmer receives payment.

---

### Phase 2 — Beta (Weeks 5–10)

**Backend**
- [ ] Products and cart schema
- [ ] Cart and product API endpoints
- [ ] Image upload and Supabase Storage
- [ ] Notification service (push + email)

**Frontend**
- [ ] Product catalog and browsing
- [ ] Shopping cart with multi-farmer checkout
- [ ] Farmer product management dashboard
- [ ] Real-time order status updates
- [ ] Starkzap SDK integration (staking UI)

**Milestone:** Full marketplace flow — farmer lists products, buyer adds to cart, multi-farmer checkout, staking available.

---

### Phase 3 — Production Launch (Weeks 11–16)

- [ ] Independent smart contract security audit
- [ ] Mainnet deployment
- [ ] Performance optimization and load testing
- [ ] Analytics dashboard for platform operators
- [ ] Price discovery tools (demand/supply aggregation)
- [ ] Marketing site and onboarding materials
- [ ] NGO and cooperative partnership onboarding

**Milestone:** Mainnet live. First real-value trades completed.

---

## 14. Open Questions and Risks

### 14.1 Open Questions

| # | Question | Owner | Priority |
|---|---|---|---|
| Q1 | What is the exact Starkzap SDK interface and availability date? | Starkzap team | High |
| Q2 | Will the 96-hour dispute window be configurable per order in future versions? | Product | Medium |
| Q3 | Should `fee_collector` be upgradeable via a governance mechanism in V2? | Engineering | Medium |
| Q4 | What is the plan for fiat on-ramp so non-crypto farmers can receive payments? | Product | High |
| Q5 | Should refund trigger be automated (cron job) or remain permissionless? | Engineering | Low |
| Q6 | Is there a need for a formal dispute resolution mechanism beyond auto-refund? | Product | High |

### 14.2 Risks

| Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|
| Smart contract vulnerability | Low | Critical | Formal audit before Mainnet, bug bounty post-launch |
| Starknet network downtime | Medium | High | User-facing status page, retry logic in frontend |
| Starkzap SDK delays or API changes | Medium | Medium | Build integration behind feature flag, fallback to core features |
| Low farmer adoption | Medium | High | Partner with NGOs and cooperatives for onboarding |
| Token price volatility (STRK) | High | Medium | Prioritize USDC stablecoin as default payment token |
| Regulatory risk (DeFi / crypto payments) | Medium | High | Legal review per target market before launch |
| Indexer falling behind during high load | Low | Medium | Alerting, auto-restart, cursor-based resumption |
