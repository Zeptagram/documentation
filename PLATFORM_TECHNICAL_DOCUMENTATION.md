# Zeptagram Platform - Technical Documentation

## Executive Summary

Zeptagram is a blockchain-based real-world asset (RWA) tokenization platform that evolved from a music rights tokenization system into a broader RWA platform. The platform enables fractional ownership of assets through smart contracts deployed on the **Stellar blockchain** using **Soroban smart contracts**.

---

## 1. Architecture & Infrastructure

### 1.1 System Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                         Frontend Layer                           │
├──────────────────────┬──────────────────────┬───────────────────┤
│  Mobile App          │  Web UI              │  Admin Dashboard  │
│  (React Native)      │  (React.js)          │  (React.js)       │
└──────────────────────┴──────────────────────┴───────────────────┘
                                 │
                                 ▼
┌─────────────────────────────────────────────────────────────────┐
│                         API Gateway                              │
│                    (Node.js + Express)                           │
│                  RESTful API / Socket.io                         │
└─────────────────────────────────────────────────────────────────┘
                                 │
                ┌────────────────┼────────────────┐
                ▼                ▼                ▼
┌────────────────────┐  ┌────────────────┐  ┌──────────────┐
│   Business Logic   │  │  Blockchain    │  │  Database    │
│   Layer            │  │  Integration   │  │  Layer       │
├────────────────────┤  ├────────────────┤  ├──────────────┤
│ • KYC/AML          │  │ • Stellar SDK  │  │ • MongoDB    │
│ • Crowdsales       │  │ • Soroban      │  │ • Redis      │
│ • Royalties        │  │ • Horizon API  │  │              │
│ • Wallet Mgmt      │  │ • Contract     │  │              │
│ • Notifications    │  │   Interface    │  │              │
└────────────────────┘  └────────────────┘  └──────────────┘
                                 │
                                 ▼
┌─────────────────────────────────────────────────────────────────┐
│                    Stellar Blockchain Network                    │
├─────────────────────────────────────────────────────────────────┤
│                   Soroban Smart Contract Layer                   │
│  Contract ID: CAH4QFUXJHBIZS2WYAVQHAPNGNDZBFNJ6OFDRMWWVMWAB6CAYBUN6AUI
│                                                                  │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐         │
│  │ User Manager │  │ NFT Manager  │  │ Sale Manager │         │
│  │ (KYC/Balance)│  │ (Music/RWA)  │  │ (Crowdsale)  │         │
│  └──────────────┘  └──────────────┘  └──────────────┘         │
│                                                                  │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐         │
│  │ MTO Manager  │  │ Token Std    │  │ Admin Module │         │
│  │ (Fractional) │  │ (Soroban)    │  │ (Governance) │         │
│  └──────────────┘  └──────────────┘  └──────────────┘         │
└─────────────────────────────────────────────────────────────────┘
                                 │
                                 ▼
┌─────────────────────────────────────────────────────────────────┐
│                    External Integrations                         │
├─────────────────────────────────────────────────────────────────┤
│ • AWS S3 (Storage)          • RabbitMQ (Message Queue)          │
│ • Pinata (IPFS)             • Firebase (Push Notifications)     │
│ • Twilio (SMS)              • Payment Rails                      │
│ • Email Services            • Trading Server                     │
└─────────────────────────────────────────────────────────────────┘
```

### 1.2 Supported Blockchains

#### Primary Blockchain: Stellar
- **Network**: Stellar Testnet (currently), Mainnet (production)
- **Consensus**: Stellar Consensus Protocol (SCP)
- **Smart Contract Platform**: Soroban
- **Smart Contract Language**: Rust (using Soroban SDK v22.0.1+)
- **Transaction Finality**: ~5 seconds
- **Network URL**: `https://horizon-testnet.stellar.org`
- **RPC URL**: `https://soroban-testnet.stellar.org`

#### Legacy Support
The platform previously supported:
- **Telos (EOSIO)** - Legacy music tokenization system
- Migration tooling exists to transfer assets from Telos to Stellar

### 1.3 Hosting & Deployment Model

#### Deployment Architecture: **Hybrid SaaS**

**Backend Services** (Centralized SaaS):
- **Hosting**: AWS / Cloud Infrastructure
- **API Server**: Node.js backend hosted on cloud VMs
- **Database**: MongoDB Atlas (managed) + Redis
- **Message Queue**: RabbitMQ
- **File Storage**: AWS S3 + Pinata IPFS
- **CDN**: CloudFront (`https://d3or5gldks5wb2.cloudfront.net`)

**Smart Contracts** (Decentralized):
- Deployed on Stellar blockchain (immutable, decentralized)
- Contract interaction via Stellar Horizon API and Soroban RPC

**Frontend** (Distributed):
- Web UI: Static hosting (S3 + CloudFront or similar)
- Mobile: App stores (iOS/Android)
- Admin Dashboard: Restricted access, cloud-hosted

#### Environment Configuration

```javascript
// Development
Domain: https://zepta-new-api.zeptagram.com
Web UI: https://new-ui-test.zeptagram.com
Admin: https://zepta-admin.zeptagram.com
Trading Server: https://trade-server-test.zeptagram.com

// Network
Stellar Network: Testnet
MongoDB: Local / Atlas cluster
Redis: Local cache
Port: 4000
```

### 1.4 API Capabilities

The platform exposes **29+ Soroban-related APIs** across multiple categories:

| Category | Endpoints | Description |
|----------|-----------|-------------|
| **User Management** | 4 | Create users, fetch data, update KYC/balance |
| **Music NFT Operations** | 6 | Create/query NFTs, ownership tracking |
| **Sale Operations** | 7 | Crowdsale creation, active sales, sale details |
| **Investment/Purchase** | 4 | Invest in crowdsales, purchase shares |
| **MTO Balance/Transfer** | 3 | Check balances, transfer fractional tokens |
| **Transaction History** | 1 | Paginated transaction logs with filtering |
| **Token Operations** | 2 | Mint tokens, balance checks |
| **Admin Operations** | 2 | Admin management, contract governance |

**API Base URL**: `/api/v1`

**Authentication**: JWT-based (`x-auth-token` header)

**Key API Examples**:
- `POST /soroban/user` - Create blockchain user
- `POST /user/crowdsale/soroban/start/:id` - Deploy crowdsale
- `POST /user/invest/soroban` - Invest with XDR signing flow
- `GET /soroban/sale/active` - Get active crowdsales
- `POST /user/soroban/mto/balance` - Check fractional ownership
- `GET /user/soroban/txn/history` - Transaction history with pagination

**API Documentation**: `SOROBAN_API_FRONTEND_GUIDE.md`

---

## 2. Tokenization Logic

### 2.1 Token Standards

#### Soroban Token Interface (Stellar)
The platform implements a **custom token standard** built on Soroban's token interface:

**Base Token** (Platform Native):
- Implements Soroban's standard token interface (`token::Interface`)
- Functions: `transfer`, `balance`, `approve`, `mint`, `burn`
- Decimals: Configurable (default standard)
- Admin-controlled minting

**Music Token Offering (MTO)** (Fractional Ownership):
- Custom implementation for **fractional asset ownership**
- Each NFT can be subdivided into MTO tokens
- MTO tokens represent percentage ownership of underlying asset
- Stored in contract state: `DataKey::MtoBalance(Address, MtoId)`

**Standard Compliance**:
- Not ERC-20/1400/3643 (Ethereum standards)
- Custom Soroban standard optimized for music/RWA fractional ownership
- Follows Soroban's SCVal (Smart Contract Value) serialization

### 2.2 NFT Structure

```rust
pub struct MusicNFT {
    pub id: u32,                    // Unique NFT ID (auto-incremented)
    pub issuer: Address,            // Creator/issuer wallet address
    pub category: String,           // Asset category (e.g., "music", "real-estate")
    pub token_name: String,         // Human-readable name
    pub burnable: bool,             // Can NFT be destroyed?
    pub base_uri: String,           // Metadata URI (IPFS/HTTP)
    pub time_span: u32,             // Expiration timestamp (ledger time)
    pub created_at: u32,            // Creation timestamp
    pub per_amount: u32,            // Price per fractional unit
    pub total_supply: u32,          // Total fractional units
    pub remaining_supply: u32,      // Units still available
}
```

**Ownership Tracking**:
```rust
pub struct MusicOwner {
    pub owner: Address,     // Current owner wallet
    pub token_id: u32,      // NFT ID
}
```

**User NFT List**:
- Each user has a persistent vector of owned NFT IDs
- Stored as: `DataKey::UserNFTList(Address) -> Vec<u32>`

### 2.3 Transfer Restrictions

#### On-Chain Restrictions

**1. Authorization Requirements**:
```rust
// All transfers require explicit authorization
from.require_auth();  // Sender must sign transaction
```

**2. Balance Validation**:
```rust
// Insufficient balance check
assert!(from_bal >= amount, "insufficient MTO balance");
```

**3. Time-Based Restrictions**:
```rust
// Sale must be active within time window
assert!(e.ledger().timestamp() as u32 <= sale.end_time, "Sale has expired");
```

**4. Share Availability**:
```rust
// Cannot purchase more shares than available
assert!(quantity <= sale.percent_share, "Insufficient shares available");
```

**5. Contribution Limits**:
```rust
// Min/max contribution enforcement
assert!(quantity >= sale.min_contribution, "Below minimum");
assert!(quantity <= sale.max_contribution, "Exceeds maximum");
```

#### Off-Chain Restrictions (Backend)

**KYC Level Restrictions**:
- Level 1: Limited functionality
- Level 2: Standard trading
- Level 3: Full platform access

**Wallet Whitelisting**:
- User wallets must be registered on-chain via `create_user()`
- KYC data stored both on-chain and in MongoDB

### 2.4 Whitelisting Mechanism

#### On-Chain User Registry
```rust
pub struct UserData {
    pub kyc_level: u64,           // 1, 2, or 3
    pub balance: i128,            // User's token balance
    pub expiration_ledger: u32,   // Data validity period
}
```

**User Creation Flow**:
1. Off-chain KYC verification (backend)
2. Admin calls `create_user()` on contract
3. User wallet address whitelisted with KYC level
4. User can now interact with contract

**Access Control**:
```rust
// Validate user exists and has sufficient KYC level
let user_data = read_user_data(&e, &user)
    .expect("User not whitelisted");
assert!(user_data.kyc_level >= required_level, "Insufficient KYC level");
```

### 2.5 Corporate Actions

#### Mint (Token Creation)
```rust
pub fn mint(e: Env, to: Address, amount: i128) {
    let admin = read_administrator(&e);
    admin.require_auth();  // Only admin can mint
    
    check_nonnegative_amount(amount);
    receive_balance(&e, to.clone(), amount);
    TokenUtils::new(&e).events().mint(admin, to, amount);
}
```

#### Burn (Token Destruction)
```rust
pub fn burn_nft_music(env: Env, owner: Address, nft_id: u32) {
    owner.require_auth();  // Owner authorization required
    
    let nft = read_music_data(&env, nft_id).expect("NFT not found");
    assert!(nft.burnable, "NFT is not burnable");
    
    // Remove from storage
    env.storage().persistent().remove(&DataKey::MusicNFT(nft_id));
    env.storage().persistent().remove(&DataKey::MusicOwner(nft_id));
    
    // Event emission
    env.events().publish((symbol_short!("burn"), owner), nft_id);
}
```

#### Freeze (Sale Suspension)
```rust
pub fn close_sale(e: Env, sale_id: u32) {
    suspendsale(&e, sale_id);
    // Removes sale from active_sales list
}
```

#### Distributions (Royalty Payments)
- **Not implemented at contract level** (by design)
- Managed off-chain by backend cron jobs
- Backend reads MTO balances and distributes proportionally
- Distribution records stored in MongoDB

**Distribution Flow**:
1. Royalty event triggered (streaming revenue, sale, etc.)
2. Backend calculates MTO holder percentages
3. Payments distributed via Payment Rails / Stellar payments
4. Transaction records created in database

---

## 3. Security

### 3.1 Smart Contract Security

#### Contract Architecture
- **Language**: Rust (memory-safe, prevents buffer overflows)
- **SDK**: Soroban SDK v22.0.1 (latest stable)
- **Compilation**: Release mode with optimization level `z`

**Security Features**:
```toml
[profile.release]
opt-level = "z"              # Optimize for size + security
overflow-checks = true       # Panic on integer overflow
debug-assertions = false     # Remove debug code
panic = "abort"              # Minimize attack surface
strip = "symbols"            # Remove symbols
lto = true                   # Link-time optimization
```

#### Authorization Model
```rust
// All state-changing operations require authorization
from.require_auth();        // Transaction signer verification
admin.require_auth();       // Admin-only operations
```

#### Reentrancy Protection
- Soroban's execution model prevents reentrancy by design
- No external calls to untrusted contracts
- State changes finalized before external interactions

#### Integer Overflow Protection
```rust
// All arithmetic uses checked operations
.checked_mul(per_percent_amt as i128).expect("overflow");
.checked_add(total_supply).expect("overflow");
```

#### Input Validation
```rust
// Negative amounts rejected
fn check_nonnegative_amount(amount: i128) {
    if amount < 0 {
        panic!("negative amount is not allowed: {}", amount);
    }
}

// KYC level validation
if kyc_level > 3 {
    panic!("Invalid KYC Level! Valid levels are 1, 2, 3.");
}
```

### 3.2 Smart Contract Audits

**Audit Status**: ⚠️ **Not yet audited by third-party firm**

**Internal Testing**:
- ✅ Unit tests covering 16/22 methods (73%)
- ✅ Deployed and tested on Stellar Testnet
- ✅ Live contract: `CAH4QFUXJHBIZS2WYAVQHAPNGNDZBFNJ6OFDRMWWVMWAB6CAYBUN6AUI`
- ✅ All core functionality verified (see `TEST_RESULTS.md`)

**Recommended Before Production**:
- [ ] Professional security audit (e.g., Trail of Bits, OpenZeppelin)
- [ ] Formal verification of critical functions
- [ ] Bug bounty program
- [ ] Penetration testing of API layer

### 3.3 Backend Security

#### Authentication & Authorization
- **JWT Tokens**: HS256 signing, token-based sessions
- **Session Management**: Express session with secure cookies
- **CSRF Protection**: `csurf` middleware enabled
- **Rate Limiting**: 1000 requests/15 minutes per IP

```javascript
rateLimiter: {
    windowMs: 15 * 60 * 1000,  // 15 minutes
    max: 1000                   // limit per IP
}
```

#### Input Sanitization
```javascript
// XSS protection
const xss = require('xss');

// SQL injection prevention (NoSQL)
const { celebrate, Joi } = require('celebrate');

// Request sanitization
app.use(require('express-sanitizer')());
```

#### Security Headers
```javascript
const helmet = require('helmet');
app.use(helmet());  // Sets secure HTTP headers
```

#### Encryption
- **AES Encryption**: Wallet private keys encrypted at rest
- **Environment Secrets**: Stored in `.env` (not committed)
- **Password Hashing**: bcrypt with salt rounds

```javascript
const bcrypt = require('bcrypt-nodejs');
// User passwords hashed before storage
```

### 3.4 Key Management

#### Wallet Key Storage
**Admin Keys**:
- Stellar admin private key: Environment variable
- Music admin key: Separate environment variable
- **Never logged or exposed in responses**

**User Keys**:
- Users control their own private keys (non-custodial)
- Platform never stores user private keys
- Freighter/Albedo wallet integration for signing

**Key Rotation**:
```rust
pub fn set_admin(e: Env, new_admin: Address) {
    let admin = read_administrator(&e);
    admin.require_auth();
    
    write_administrator(&e, &new_admin);
    TokenUtils::new(&e).events().set_admin(admin, new_admin);
}
```

#### XDR Signing Flow (Non-Custodial)
1. Backend builds unsigned XDR transaction
2. Frontend requests signature from user's wallet
3. User signs with Freighter/Albedo (keys never leave wallet)
4. Frontend submits signed XDR to backend
5. Backend broadcasts to Stellar network

### 3.5 Incident Response

**Monitoring**:
- **Application Monitoring**: APM Insight (Zoho)
- **Blockchain Monitoring**: Stellar Explorer integration
- **Error Logging**: Winston logger with daily rotation
- **Failed Transaction Tracking**: MongoDB collection

**Response Plan**:
1. **Detection**: Automated alerts for failed transactions
2. **Assessment**: Review logs and transaction hashes
3. **Containment**: Admin can close sales, freeze accounts
4. **Recovery**: Revert state via new transactions (blockchain immutability)
5. **Post-Incident**: Update code, re-deploy, document learnings

**Emergency Contacts**:
```javascript
collection_agency: {
    email: 'vishal@zeptagram.io'
}
```

---

## 4. Compliance Layer (Technical Implementation)

### 4.1 KYC/AML Integration

#### KYC Data Flow
```
User Registration → Backend KYC Form → Manual/Automated Verification
         ↓
MongoDB (Off-chain) + Soroban Contract (On-chain)
         ↓
User assigned KYC Level (1-3) → Whitelist on contract
```

**On-Chain KYC Storage**:
```rust
pub struct UserData {
    pub kyc_level: u64,           // Verification tier
    pub balance: i128,            // Token holdings
    pub expiration_ledger: u32,   // Re-verification due date
}
```

**KYC Levels**:
- **Level 1**: Basic email/phone verification → Limited trading
- **Level 2**: Government ID verification → Standard trading limits
- **Level 3**: Enhanced due diligence → Institutional/high-value

#### AML Checks
**Off-Chain** (Backend responsibility):
- Transaction monitoring for suspicious patterns
- Integration with Payment Rails for fiat on/off-ramp
- Flagging large/unusual transactions for review

**On-Chain**:
- Transaction history immutable on Stellar ledger
- Backend indexes all contract interactions
- Cross-reference with known bad actors (off-chain database)

### 4.2 User/Wallet Whitelisting

**Two-Tier Whitelisting**:

**1. Off-Chain (MongoDB)**:
```javascript
User Schema {
    email: String,
    phone: String,
    stellarPublicKey: String,
    kycLevel: Number,
    kycDocuments: [Document],
    isWhitelisted: Boolean,
    createdAt: Date
}
```

**2. On-Chain (Soroban Contract)**:
```rust
// User must be created on contract to interact
stellar contract invoke --id $CONTRACT_ID -- create_user \
  --user <PUBLIC_KEY> \
  --kyc_level 2 \
  --balance 0
```

**Whitelisting Process**:
1. User completes off-chain KYC
2. Admin reviews and approves
3. Backend calls `POST /soroban/user` to whitelist on-chain
4. User can now invest in crowdsales

**Blacklisting**:
- Update KYC level to 0 (off-chain)
- Future: Implement on-chain blacklist check

### 4.3 Audit Logs

#### On-Chain Audit Trail
- **Immutable**: All transactions recorded on Stellar ledger
- **Queryable**: Via Horizon API + Backend indexing
- **Event Emission**: Contract emits events for key actions

```rust
// Event examples
e.events().publish(("buyshare",), (buyer, seller, sale_id, quantity));
e.events().publish(("mto_transfer",), (from, to, mto, amount));
TokenUtils::new(&e).events().mint(admin, to, amount);
```

#### Off-Chain Audit Logs (Backend)

**Transaction Model** (`SorobanTransaction`):
```javascript
{
    status: "SUCCESS" | "FAILED",
    hash: "transaction_hash",
    action: "invest" | "create_nft" | "mint" | ...,
    forUserPublickey: "GXXX...XXX",  // Target user
    byUserPublickey: "GYYY...YYY",   // Initiator
    latestLedger: Number,
    createdAt: Date,
    memo: String
}
```

**Audit Query API**:
```http
GET /user/soroban/txn/history
  ?page=1
  &limit=10
  &action=invest
  &forUserPublickey=GXXX
  &sortBy=created_at
  &order=-1
```

**Log Rotation**:
- Winston daily rotate file
- Logs stored in `/log` directory
- Archived in S3 for long-term retention

### 4.4 GDPR-Aligned Data Handling

#### Personal Data Classification

| Data Type | Storage | GDPR Category | Retention |
|-----------|---------|---------------|-----------|
| Email, Phone | MongoDB (encrypted) | PII | 7 years after account closure |
| KYC Documents | AWS S3 (encrypted) | Special Category | 7 years (AML requirement) |
| Transaction History | Blockchain (public) | Pseudonymous | Permanent (immutable) |
| Wallet Addresses | Blockchain + MongoDB | Pseudonymous | Permanent |
| IP Addresses | Logs (temp) | PII | 90 days max |

#### GDPR Rights Implementation

**1. Right to Access** (Article 15):
```http
GET /user/profile
GET /user/soroban/txn/history
```
Returns all user data in structured format.

**2. Right to Rectification** (Article 16):
```http
PATCH /user/profile
PATCH /soroban/user/:publicKey/kyc
```

**3. Right to Erasure** (Article 17):
- **Off-chain**: User can request account deletion
- **On-chain**: Cannot delete blockchain data (immutability)
- **Solution**: Soft delete in MongoDB + anonymize metadata

**4. Data Portability** (Article 20):
- Export user data via API
- JSON format for machine readability

**5. Privacy by Design**:
- Minimal data collection (only KYC-required fields)
- Encryption at rest (AWS S3, MongoDB)
- Encryption in transit (HTTPS/TLS)
- Access controls (JWT, role-based)

#### Cookie Policy
```javascript
// Session cookies
cookieSession({
    name: 'session',
    keys: [process.env.COOKIE_SECRET],
    httpOnly: true,
    secure: true,  // HTTPS only
    sameSite: 'strict'
})
```

#### Data Processing Agreement
- Users agree to ToS before account creation
- Explicit consent for data processing
- Opt-in for marketing communications

---

## 5. Platform Legacy & Evolution

### 5.1 Original System: Music Rights Platform (Telos/EOSIO)

#### Historical Architecture (v1.0 - 2020-2023)
- **Blockchain**: Telos (EOSIO fork)
- **Language**: C++ smart contracts
- **Focus**: Music NFT tokenization
- **Features**:
  - Music track tokenization
  - Fractional ownership (MUSIC tokens)
  - Crowdsale mechanism
  - Trading server integration
  - Royalty distribution

#### Key Components (Still in Codebase)
```
/backend_new/app/v1/modules/eosioUtils/
├── txnBuilder.js        # EOSIO transaction builder
├── index.js             # Legacy contract interactions
└── constants.js         # Contract account names

Config:
- zeptaEosioAcc          # Main Telos account
- zeptaTradeAcc          # Trading account
- zeptaMusicAcc          # Music token account
- music_token_acc_name   # 'zeptamtdapp4'
```

#### Retained Infrastructure
- **Trading Server**: `https://trade-server-test.zeptagram.com`
- **EOSIO Node**: `http://test.telos.eosusa.io`
- **Wallets**: Legacy accounts still in config

### 5.2 Migration to Stellar/Soroban (v2.0 - 2024-Present)

#### Why Stellar?
1. **Lower Fees**: ~$0.00001 per transaction vs EOSIO's resource model
2. **Soroban Smart Contracts**: Rust-based, modern tooling
3. **Finality**: 5-second finality vs EOSIO's 3-minute irreversibility
4. **Ecosystem**: Better fiat on/ramps, wallet support (Freighter, Albedo)
5. **Compliance**: Built-in asset issuance with compliance hooks

#### Migration Architecture
```rust
// NEW: Soroban Contract (Rust)
zepta_contract/
├── src/
│   ├── contract.rs       # Main contract logic
│   ├── music.rs          # NFT management
│   ├── sale.rs           # Crowdsale logic
│   ├── user.rs           # KYC/user management
│   ├── balance.rs        # Token balances
│   └── storage_types.rs  # Data structures

stellar-zepta-contract/   # Alternative implementation
└── (Parallel development)
```

**Backend Adaptation**:
```javascript
// NEW: Soroban Integration Modules
/app/v1/modules/soroban/
├── index.js              # Contract interaction layer
├── route.js              # 29 new API endpoints
├── model.js              # Transaction logging

/app/v1/modules/stellar/
├── index.js              # Stellar SDK wrapper
├── contract.js           # Contract helpers
└── constant.js           # Network configs
```

#### Feature Parity Matrix

| Feature | EOSIO (v1.0) | Soroban (v2.0) | Status |
|---------|--------------|----------------|--------|
| User KYC Tracking | ✅ Off-chain | ✅ On-chain | Improved |
| Music NFT Creation | ✅ C++ | ✅ Rust | ✅ Migrated |
| Fractional Ownership | ✅ MUSIC token | ✅ MTO token | ✅ Migrated |
| Crowdsale | ✅ | ✅ | ✅ Migrated |
| Trading | ✅ Exchange | ✅ P2P MTO transfer | ✅ Migrated |
| Royalty Distribution | ✅ Contract | ⚠️ Backend | In Progress |
| Staking | ❌ | ❌ | Planned |

### 5.3 System Upgrades for RWA Tokenization

#### Generalization from Music → RWA

**v1.0 (Music-Specific)**:
```javascript
// Hard-coded for music
category: "music"
token_name: "SONG123"
base_uri: "ipfs://music-metadata"
```

**v2.0 (RWA-Generic)**:
```rust
// Flexible asset types
pub category: String,      // "music", "real-estate", "art", etc.
pub token_name: String,    // Human-readable asset identifier
pub base_uri: String,      // Points to any metadata (IPFS/HTTP)
```

#### New Capabilities

**1. Multi-Asset Support**:
- Originally: Only music tracks
- Now: Any real-world asset (art, real estate, commodities)

**2. Enhanced Metadata**:
```rust
pub struct MusicNFT {  // Name retained but semantics expanded
    pub total_supply: u32,      // Total fractional units
    pub remaining_supply: u32,  // Dynamic supply tracking
    pub time_span: u32,         // Asset lifecycle/expiration
    pub per_amount: u32,        // Price per unit (flexible)
}
```

**3. Improved Sale Mechanics**:
```rust
pub struct Sale {
    pub min_contribution: u32,   // Minimum investment
    pub max_contribution: u32,   // Maximum per investor
    pub soft_cap: u32,           // Minimum funding goal
    pub start_time: u32,         // Campaign start
    pub end_time: u32,           // Campaign end
}
```

**4. MTO Token System** (New):
- Represents fractional ownership percentage
- Transferable between users
- Queryable balance per asset
- Dual-key storage (by token name + MTO ID)

### 5.4 Technical Debt & Cleanup

#### Still Present in Codebase
- **EOSIO Modules**: Not removed for backward compatibility
- **Legacy Config**: Telos account names still in `config/index.js`
- **Dual Wallet System**: Both Stellar and EOSIO wallet services

#### Cleaned Up
- ✅ Removed EOS-specific crowdsale logic from main flow
- ✅ Deprecated Telos endpoints (kept for migration tools)
- ✅ Unified authentication (JWT works for both systems)

#### Future Deprecation Plan
1. **Phase 1** (Current): Run both systems in parallel
2. **Phase 2**: Migrate all users to Stellar
3. **Phase 3**: Sunset EOSIO modules
4. **Phase 4**: Remove legacy code

### 5.5 Lessons Learned & Improvements

#### From EOSIO to Soroban

**Improvements**:
1. **Better Developer Experience**: Rust > C++ (memory safety, tooling)
2. **Cost Efficiency**: No CPU/NET staking required
3. **Faster Iteration**: Testnet deployments in seconds
4. **Type Safety**: Rust compiler catches bugs at compile-time

**Retained Strengths**:
1. **Crowdsale Logic**: Core algorithm unchanged (proven model)
2. **Royalty Distribution**: Same proportional calculation
3. **KYC System**: Enhanced but same compliance approach

#### Architectural Decisions

**What Stayed On-Chain**:
- User whitelisting (KYC levels)
- Asset ownership (NFT + MTO balances)
- Sale state (active, percent remaining)
- Transfer authorization

**What Moved Off-Chain**:
- Royalty distribution (gas efficiency)
- KYC document storage (GDPR compliance)
- Search/indexing (performance)
- Email notifications

---

## 6. Operational Metrics

### 6.1 System Performance

**Blockchain Performance**:
- Transaction Finality: ~5 seconds (Stellar)
- Contract Call Cost: ~0.00001 XLM per invocation
- Throughput: Supports 1000+ TPS (Stellar network capacity)

**Backend Performance**:
- API Response Time: <200ms (typical)
- Database Queries: <50ms (MongoDB indexed)
- Rate Limit: 1000 req/15min per IP

### 6.2 Deployed Contract

**Live Testnet Contract**:
```
Contract ID: CAH4QFUXJHBIZS2WYAVQHAPNGNDZBFNJ6OFDRMWWVMWAB6CAYBUN6AUI
Network: Stellar Testnet
Admin: GBDBYUN2LWDLYZQOQQ2XFUKBUZQWHWE3YIXGVP44JO3SG5DKWS7RCLHD
Explorer: https://stellar.expert/explorer/testnet/contract/CAH4Q...
```

**Test Results**:
- 16/22 methods fully tested (73%)
- All core functionality verified
- Production-ready architecture

---

## 7. Future Roadmap

### 7.1 Short-Term (Q1-Q2 2025)
- [ ] Professional security audit
- [ ] Mainnet deployment
- [ ] On-chain royalty distribution
- [ ] Enhanced compliance reporting

### 7.2 Medium-Term (Q3-Q4 2025)
- [ ] Multi-asset marketplace UI
- [ ] Automated KYC verification (e.g., Civic integration)
- [ ] DeFi integration (liquidity pools for MTO tokens)
- [ ] Mobile wallet SDK

### 7.3 Long-Term (2026+)
- [ ] Cross-chain bridge (Ethereum, Polygon)
- [ ] DAO governance for platform decisions
- [ ] Decentralized dispute resolution
- [ ] Full EOSIO sunset

---

## 8. Technical Specifications Summary

| Aspect | Specification |
|--------|---------------|
| **Blockchain** | Stellar (Testnet → Mainnet) |
| **Smart Contracts** | Soroban (Rust, SDK v22.0.1) |
| **Backend** | Node.js 10+, Express |
| **Database** | MongoDB 4.4+, Redis 2.8+ |
| **Frontend** | React.js, React Native |
| **Authentication** | JWT (HS256) |
| **File Storage** | AWS S3, Pinata IPFS |
| **Hosting** | AWS Cloud (SaaS), Stellar Network (Decentralized) |
| **APIs** | RESTful (29+ endpoints), WebSocket (Socket.io) |
| **Compliance** | KYC 3-tier, GDPR-aligned, Audit logs |
| **Security** | AES encryption, Helmet headers, Rate limiting |

---

## 9. Contact & Resources

**Development Team**: cyberne7work  
**Admin Contact**: vishal@zeptagram.io  

**Key URLs**:
- API: `https://zepta-new-api.zeptagram.com`
- Web UI: `https://new-ui-test.zeptagram.com`
- Admin Dashboard: `https://zepta-admin.zeptagram.com`

**Documentation Files**:
- `SOROBAN_API_FRONTEND_GUIDE.md` - Complete API reference
- `WALLET_MIGRATION_GUIDE.md` - EOSIO → Stellar migration
- `TEST_RESULTS.md` - Contract test results
- `README.md` (per module) - Module-specific documentation

**External Resources**:
- Stellar Docs: https://developers.stellar.org/
- Soroban Docs: https://soroban.stellar.org/
- Freighter Wallet: https://www.freighter.app/

---

**Document Version**: 1.0  
**Last Updated**: 2025-01-17  
**Status**: Production-Ready (Testnet) | Pending Mainnet Deployment
