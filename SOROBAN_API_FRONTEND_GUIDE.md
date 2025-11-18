# Soroban API Endpoints - Frontend Implementation Guide

## 🎯 **Priority APIs for Frontend**

### **1. User Authentication & Management**

#### **GET** `/soroban/user/:publicKey`
**Purpose**: Fetch user data by Stellar public key  
**Required Headers**: `x-auth-token`  
**Response**:
```json
{
  "success": true,
  "tx": "transaction_hash",
  "value": {
    "kyc_level": 1,
    "balance": 1000
  }
}
```
**Frontend Use**: Display user KYC level and balance

---

#### **POST** `/soroban/user`
**Purpose**: Create a new user on Soroban  
**Required Headers**: `x-auth-token`  
**Request Body**:
```json
{
  "user": "GXXX...XXX",
  "kyc_level": 1,
  "balance": 0
}
```
**Frontend Use**: Onboard new users to Soroban

---

#### **PATCH** `/soroban/user/:publicKey/kyc`
**Purpose**: Update user KYC level  
**Required Headers**: `x-auth-token`  
**Request Body**:
```json
{
  "level": 2
}
```
**Frontend Use**: KYC verification flow

---

#### **PATCH** `/soroban/user/:publicKey/balance`
**Purpose**: Update user balance  
**Required Headers**: `x-auth-token`  
**Request Body**:
```json
{
  "balance": 5000
}
```
**Frontend Use**: Admin panel or balance updates

---

### **2. Music NFT Operations** 🎵

#### **POST** `/user/crowdsale/soroban/start/:id`
**Purpose**: Deploy crowdsale (creates music NFT + sale)  
**Required Headers**: `x-auth-token`  
**URL Params**: `id` (MongoDB crowdsale ID)  
**Response**:
```json
{
  "success": true,
  "categoryId": 1,
  "tokenName": "SONG123",
  "saleId": 5
}
```
**Frontend Use**: Artist publishes music for crowdfunding

---

#### **POST** `/soroban/music`
**Purpose**: Create a music NFT directly  
**Required Headers**: `x-auth-token`  
**Request Body**:
```json
{
  "issuer": "GXXX...XXX",
  "category": "music",
  "token_name": "SONG123",
  "burnable": true,
  "base_uri": "https://ipfs.io/ipfs/...",
  "time_span": 1735615543,
  "per_amount": 100
}
```
**Response**:
```json
{
  "success": true,
  "tx": "transaction_hash",
  "retval": 1
}
```
**Frontend Use**: Direct NFT creation (alternative flow)

---

#### **GET** `/soroban/music/:nftId`
**Purpose**: Get music NFT details  
**Required Headers**: `x-auth-token`  
**Response**:
```json
{
  "success": true,
  "value": {
    "id": 1,
    "issuer": "GXXX...XXX",
    "category": "music",
    "token_name": "SONG123",
    "burnable": true,
    "base_uri": "https://...",
    "time_span": 1704615543,
    "per_amount": 100
  }
}
```
**Frontend Use**: Display music NFT details page

---

#### **GET** `/soroban/music/:nftId/owner`
**Purpose**: Get owner information for an NFT  
**Required Headers**: `x-auth-token`  
**Response**:
```json
{
  "success": true,
  "value": {
    "owner": "GXXX...XXX",
    "nft_id": 1
  }
}
```
**Frontend Use**: Display ownership information

---

#### **GET** `/soroban/music/user/:publicKey/list`
**Purpose**: Get list of NFT IDs owned by user  
**Required Headers**: `x-auth-token`  
**Response**:
```json
{
  "success": true,
  "value": [1, 2, 5, 10]
}
```
**Frontend Use**: User's music collection/portfolio

---

#### **GET** `/soroban/music/user/:publicKey/data`
**Purpose**: Get full NFT metadata for user's collection  
**Required Headers**: `x-auth-token`  
**Response**:
```json
{
  "success": true,
  "value": [
    {
      "id": 1,
      "token_name": "SONG123",
      "base_uri": "https://...",
      "category": "music",
      "issuer": "GXXX...XXX"
    }
  ]
}
```
**Frontend Use**: Display user's music library with details

---

### **3. Crowdsale/Sale Operations** 💰

#### **POST** `/soroban/sale`
**Purpose**: Create a sale directly  
**Required Headers**: `x-auth-token`  
**Request Body**:
```json
{
  "seller": "GXXX...XXX",
  "nft_id": 1,
  "percent_share": 100,
  "per_percent_amt": 50,
  "start_time": 1704615543,
  "end_time": 1735615543,
  "min_contribution": 1,
  "max_contribution": 100,
  "soft_cap": 5000,
  "mto": "SONG123"
}
```
**Response**:
```json
{
  "success": true,
  "tx": "transaction_hash",
  "retval": 5
}
```
**Frontend Use**: Create sale directly (alternative to crowdsale flow)

---

#### **GET** `/soroban/sale`
**Purpose**: Get all sales  
**Required Headers**: `x-auth-token`  
**Response**:
```json
{
  "success": true,
  "value": [
    {
      "sale_id": 1,
      "seller": "GXXX...XXX",
      "percent_share": 100,
      "per_percent_amt": 50,
      "start_time": 1704615543,
      "end_time": 1735615543,
      "mto": "SONG123"
    }
  ]
}
```
**Frontend Use**: Browse all available music crowdsales

---

#### **GET** `/soroban/sale/active`
**Purpose**: Get only active sales  
**Required Headers**: `x-auth-token`  
**Response**:
```json
{
  "success": true,
  "value": [1, 5, 7, 10]
}
```
**Frontend Use**: Display live crowdsales

---

#### **GET** `/soroban/sale/:saleId`
**Purpose**: Get specific sale details  
**Required Headers**: `x-auth-token`  
**URL Params**: `saleId` (sale ID)  
**Response**:
```json
{
  "success": true,
  "value": {
    "sale_id": 1,
    "seller": "GXXX...XXX",
    "category": "music",
    "token_name": "SONG123",
    "percent_share": 100,
    "per_percent_amt": 50,
    "start_time": 1704615543,
    "end_time": 1735615543,
    "min_contribution": 1,
    "max_contribution": 100,
    "soft_cap": 5000,
    "mto": "SONG123",
    "music_nft_id": 1
  }
}
```
**Frontend Use**: Crowdsale detail page

---

#### **GET** `/soroban/sale/:saleId/details`
**Purpose**: Get detailed sale information (alternative endpoint)  
**Required Headers**: `x-auth-token`  
**Frontend Use**: Alternative to `/soroban/sale/:saleId`

---

#### **POST** `/soroban/sale/:saleId/close`
**Purpose**: Close/suspend a sale  
**Required Headers**: `x-auth-token`  
**URL Params**: `saleId` (sale ID)  
**Response**:
```json
{
  "success": true,
  "tx": "transaction_hash"
}
```
**Frontend Use**: Artist closes their crowdsale

---

### **4. Investment/Purchase Operations** 🛒

#### **POST** `/user/invest/soroban`
**Purpose**: Invest in a crowdsale  
**Required Headers**: `x-auth-token`  
**Request Body (Step 1 - Get XDR to sign)**:
```json
{
  "xdr": false,
  "xdr_value": "",
  "amount": 100,
  "crowdsaleId": "64abc123def456789"
}
```
**Response (Step 1)**:
```json
{
  "success": true,
  "xdr": "base64_unsigned_xdr",
  "status": 200,
  "publickey": "GXXX...XXX"
}
```

**Request Body (Step 2 - Submit signed XDR)**:
```json
{
  "xdr": true,
  "xdr_value": "base64_signed_xdr",
  "amount": 100,
  "crowdsaleId": "64abc123def456789"
}
```
**Response (Step 2)**:
```json
{
  "success": true,
  "status": 200,
  "message": "Done."
}
```
**Frontend Use**: 
1. Call with `xdr: false` to get unsigned XDR
2. User signs XDR with Freighter/Albedo wallet
3. Call again with `xdr: true` and signed XDR to complete investment

---

#### **POST** `/soroban/sale/:saleId/buy`
**Purpose**: Direct purchase of shares (alternative to invest)  
**Required Headers**: `x-auth-token`  
**URL Params**: `saleId` (sale ID)  
**Request Body**:
```json
{
  "buyer": "GXXX...XXX",
  "category_id": 1,
  "quantity": 10,
  "token_name": "SONG123",
  "category": "music"
}
```
**Response**:
```json
{
  "success": true,
  "tx": "transaction_hash"
}
```
**Frontend Use**: Direct share purchase flow (admin/backend initiated)

---

#### **GET** `/soroban/sale/:saleId/buy/:buyer`
**Purpose**: Get buyer's purchase info for a sale  
**Required Headers**: `x-auth-token`  
**URL Params**: 
- `saleId`: Sale ID
- `buyer`: Buyer's Stellar public key  
**Response**:
```json
{
  "success": true,
  "value": {
    "buyer": "GXXX...XXX",
    "sale_id": 1,
    "quantity": 10,
    "memo": "Purchase",
    "timestamp": 1704615543
  }
}
```
**Frontend Use**: Show user's investment in specific crowdsale

---

#### **GET** `/soroban/sale/:saleId/buy/:buyer/details`
**Purpose**: Get detailed buyer purchase information  
**Required Headers**: `x-auth-token`  
**Frontend Use**: Alternative to `/soroban/sale/:saleId/buy/:buyer`

---

### **5. MTO Balance & Transfer Operations** 💸

#### **POST** `/user/soroban/mto/balance`
**Purpose**: Check MTO (Music Token Offering) balance  
**Required Headers**: `x-auth-token`  
**Request Body**:
```json
{
  "token_name": "SONG123"
}
```
**Response**:
```json
{
  "success": true,
  "value": 150,
  "tx": "transaction_hash"
}
```
**Frontend Use**: Display user's shares in specific music

---

#### **GET** `/soroban/mto/:user/:token`
**Purpose**: Alternative way to check MTO balance  
**Required Headers**: `x-auth-token`  
**URL Params**: 
- `user`: Stellar public key
- `token`: Token name (e.g., "SONG123")  
**Response**:
```json
{
  "success": true,
  "value": 150,
  "tx": "transaction_hash"
}
```
**Frontend Use**: Quick balance check

---

#### **POST** `/soroban/mto/transfer`
**Purpose**: Transfer MTO tokens between users  
**Required Headers**: `x-auth-token`  
**Request Body**:
```json
{
  "from": "GXXX...XXX",
  "to": "GYYY...YYY",
  "mto": "SONG123",
  "amount": 50
}
```
**Response**:
```json
{
  "success": true,
  "tx": "transaction_hash"
}
```
**Frontend Use**: Transfer music shares to another user (P2P trading)

---

### **6. Transaction History** 📜

#### **GET** `/user/soroban/txn/history`
**Purpose**: Get user's transaction history  
**Required Headers**: `x-auth-token`  
**Query Params**:
```
?page=1&limit=10&sortBy=created_at&order=-1&action=invest&forUserPublickey=GXXX...XXX
```

**Available Query Params**:
- `page`: Page number (default: 1)
- `limit`: Results per page (default: 10)
- `sortBy`: Field to sort by (default: 'created_at')
- `order`: Sort order, -1 for desc, 1 for asc (default: -1)
- `action`: Filter by action type (invest, verify_acct, create_music_nft, etc.)
- `forUserPublickey`: Filter by user public key
- `byUserPublickey`: Filter by admin/initiator public key
- `memo`: Filter by memo

**Response**:
```json
{
  "success": true,
  "status": 200,
  "totalRecords": 45,
  "data": [
    {
      "status": "SUCCESS",
      "hash": "abc123...",
      "action": "invest",
      "forUserPublickey": "GXXX...XXX",
      "byUserPublickey": "GYYY...YYY",
      "latestLedger": 12345,
      "createdAt": "2025-01-01T00:00:00Z"
    }
  ],
  "nextPage": true
}
```
**Frontend Use**: Transaction history page with pagination and filtering

---

### **7. Token Balance Operations** 💵

#### **GET** `/soroban/balance/:publicKey`
**Purpose**: Get base token balance (not MTO)  
**Required Headers**: `x-auth-token`  
**URL Params**: `publicKey` (Stellar public key)  
**Response**:
```json
{
  "success": true,
  "value": 1000,
  "tx": "transaction_hash"
}
```
**Frontend Use**: Display native token balance

---

#### **POST** `/soroban/mint`
**Purpose**: Mint base tokens (admin only)  
**Required Headers**: `x-auth-token`  
**Request Body**:
```json
{
  "to": "GXXX...XXX",
  "amount": 1000
}
```
**Response**:
```json
{
  "success": true,
  "tx": "transaction_hash"
}
```
**Frontend Use**: Admin panel for token minting

---

### **8. Admin Operations** 🔧

#### **GET** `/soroban/admin`
**Purpose**: Get admin address  
**Required Headers**: `x-auth-token`  
**Response**:
```json
{
  "success": true,
  "tx": "transaction_hash",
  "retval": "GADMIN...XXX"
}
```
**Frontend Use**: Display or verify admin information

---

#### **POST** `/user/soroban/call`
**Purpose**: Generic Soroban contract call (testing/debug)  
**Required Headers**: None (open for testing)  
**Request Body**:
```json
{
  "publicKey": "GXXX...XXX"
}
```
**Frontend Use**: Development/testing purposes

---

## 📋 **Implementation Priority**

### **Phase 1: Essential (MVP)** ⭐⭐⭐
These are critical for basic functionality:

1. ✅ **POST** `/soroban/user` - Create user
2. ✅ **GET** `/soroban/user/:publicKey` - Fetch user data
3. ✅ **POST** `/user/crowdsale/soroban/start/:id` - Deploy crowdsale
4. ✅ **GET** `/soroban/sale` - Browse all sales
5. ✅ **GET** `/soroban/sale/active` - Get active sales
6. ✅ **GET** `/soroban/sale/:saleId` - Get sale details
7. ✅ **POST** `/user/invest/soroban` - Invest in crowdsale
8. ✅ **POST** `/user/soroban/mto/balance` - Check MTO balance
9. ✅ **GET** `/soroban/music/:nftId` - View music NFT

### **Phase 2: Core Features** ⭐⭐
Important for full user experience:

10. ✅ **GET** `/soroban/music/user/:publicKey/list` - User's NFT list
11. ✅ **GET** `/soroban/music/user/:publicKey/data` - User's NFT data
12. ✅ **GET** `/soroban/sale/:saleId/buy/:buyer` - Purchase info
13. ✅ **GET** `/user/soroban/txn/history` - Transaction history
14. ✅ **GET** `/soroban/mto/:user/:token` - Quick balance check
15. ✅ **GET** `/soroban/music/:nftId/owner` - NFT ownership

### **Phase 3: Advanced Features** ⭐
Nice to have for complete functionality:

16. ✅ **POST** `/soroban/mto/transfer` - Transfer MTO tokens
17. ✅ **PATCH** `/soroban/user/:publicKey/kyc` - Update KYC
18. ✅ **PATCH** `/soroban/user/:publicKey/balance` - Update balance
19. ✅ **POST** `/soroban/sale/:saleId/close` - Close sale
20. ✅ **POST** `/soroban/music` - Direct NFT creation
21. ✅ **POST** `/soroban/sale` - Direct sale creation
22. ✅ **POST** `/soroban/sale/:saleId/buy` - Direct purchase
23. ✅ **GET** `/soroban/balance/:publicKey` - Base token balance
24. ✅ **POST** `/soroban/mint` - Mint tokens (admin)
25. ✅ **GET** `/soroban/admin` - Get admin info

---

## 🔐 **Authentication Requirements**

All endpoints (except `/user/soroban/call`) require authentication:

```javascript
headers: {
  'x-auth-token': 'JWT_TOKEN_FROM_LOGIN',
  'Content-Type': 'application/json'
}
```

To get the JWT token, users must first login via:
- **POST** `/user/login/local`
- **POST** `/user/login/google`
- **POST** `/user/login/apple`

---

## 🌟 **Frontend Integration Examples**

### **Example 1: Invest in Crowdsale (Complete Flow)**

```javascript
async function investInCrowdsale(crowdsaleId, amount) {
  const authToken = localStorage.getItem('authToken');
  
  try {
    // Step 1: Get XDR to sign
    const response1 = await fetch('/user/invest/soroban', {
      method: 'POST',
      headers: {
        'x-auth-token': authToken,
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({
        xdr: false,
        xdr_value: '',
        amount,
        crowdsaleId
      })
    });
    
    const result1 = await response1.json();
    
    if (!result1.success) {
      throw new Error(result1.message);
    }
    
    const { xdr, publickey } = result1;
    
    // Step 2: Sign with Freighter wallet
    const signedXDR = await window.freighter.signTransaction(xdr, {
      network: 'TESTNET',
      accountToSign: publickey
    });
    
    // Step 3: Submit signed transaction
    const response2 = await fetch('/user/invest/soroban', {
      method: 'POST',
      headers: {
        'x-auth-token': authToken,
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({
        xdr: true,
        xdr_value: signedXDR,
        amount,
        crowdsaleId
      })
    });
    
    const result2 = await response2.json();
    
    if (result2.success) {
      console.log('Investment successful!');
      return result2;
    } else {
      throw new Error(result2.message);
    }
  } catch (error) {
    console.error('Investment failed:', error);
    throw error;
  }
}

// Usage
investInCrowdsale('64abc123def456789', 100)
  .then(result => console.log('Success:', result))
  .catch(error => console.error('Error:', error));
```

---

### **Example 2: Browse Active Crowdsales**

```javascript
async function fetchActiveCrowdsales() {
  const authToken = localStorage.getItem('authToken');
  
  try {
    // Get active sale IDs
    const response1 = await fetch('/soroban/sale/active', {
      headers: {
        'x-auth-token': authToken
      }
    });
    
    const { value: activeSaleIds } = await response1.json();
    
    // Fetch details for each sale
    const salesPromises = activeSaleIds.map(saleId =>
      fetch(`/soroban/sale/${saleId}`, {
        headers: { 'x-auth-token': authToken }
      }).then(res => res.json())
    );
    
    const salesData = await Promise.all(salesPromises);
    
    return salesData.map(sale => sale.value);
  } catch (error) {
    console.error('Failed to fetch crowdsales:', error);
    throw error;
  }
}

// Usage
fetchActiveCrowdsales()
  .then(sales => {
    sales.forEach(sale => {
      console.log(`Sale ${sale.sale_id}: ${sale.token_name}`);
      console.log(`Available shares: ${sale.percent_share}`);
      console.log(`Price per share: ${sale.per_percent_amt}`);
    });
  });
```

---

### **Example 3: Check User's MTO Balances**

```javascript
async function getUserMTOBalances(tokenNames) {
  const authToken = localStorage.getItem('authToken');
  
  try {
    const balancePromises = tokenNames.map(tokenName =>
      fetch('/user/soroban/mto/balance', {
        method: 'POST',
        headers: {
          'x-auth-token': authToken,
          'Content-Type': 'application/json'
        },
        body: JSON.stringify({ token_name: tokenName })
      })
      .then(res => res.json())
      .then(data => ({ tokenName, balance: data.value }))
    );
    
    return await Promise.all(balancePromises);
  } catch (error) {
    console.error('Failed to fetch MTO balances:', error);
    throw error;
  }
}

// Usage
getUserMTOBalances(['SONG123', 'SONG456', 'SONG789'])
  .then(balances => {
    balances.forEach(({ tokenName, balance }) => {
      console.log(`${tokenName}: ${balance} shares`);
    });
  });
```

---

### **Example 4: View User's Music Collection**

```javascript
async function getUserMusicCollection(publicKey) {
  const authToken = localStorage.getItem('authToken');
  
  try {
    // Get user's NFT data with metadata
    const response = await fetch(
      `/soroban/music/user/${publicKey}/data`,
      {
        headers: { 'x-auth-token': authToken }
      }
    );
    
    const { value: nfts } = await response.json();
    
    return nfts;
  } catch (error) {
    console.error('Failed to fetch music collection:', error);
    throw error;
  }
}

// Usage
const userPublicKey = 'GXXX...XXX';
getUserMusicCollection(userPublicKey)
  .then(collection => {
    collection.forEach(nft => {
      console.log(`NFT #${nft.id}: ${nft.token_name}`);
      console.log(`URI: ${nft.base_uri}`);
      console.log(`Issuer: ${nft.issuer}`);
    });
  });
```

---

### **Example 5: Transfer MTO Tokens**

```javascript
async function transferMTOTokens(from, to, mto, amount) {
  const authToken = localStorage.getItem('authToken');
  
  try {
    const response = await fetch('/soroban/mto/transfer', {
      method: 'POST',
      headers: {
        'x-auth-token': authToken,
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({ from, to, mto, amount })
    });
    
    const result = await response.json();
    
    if (result.success) {
      console.log('Transfer successful!');
      console.log('Transaction hash:', result.tx);
      return result;
    } else {
      throw new Error(result.message);
    }
  } catch (error) {
    console.error('Transfer failed:', error);
    throw error;
  }
}

// Usage
transferMTOTokens(
  'GXXX...XXX',  // from
  'GYYY...YYY',  // to
  'SONG123',     // token name
  50             // amount
)
.then(result => console.log('Transfer complete:', result));
```

---

### **Example 6: Fetch Transaction History with Pagination**

```javascript
async function fetchTransactionHistory(page = 1, limit = 10, filters = {}) {
  const authToken = localStorage.getItem('authToken');
  
  // Build query params
  const params = new URLSearchParams({
    page: page.toString(),
    limit: limit.toString(),
    sortBy: 'created_at',
    order: '-1',
    ...filters
  });
  
  try {
    const response = await fetch(
      `/user/soroban/txn/history?${params}`,
      {
        headers: { 'x-auth-token': authToken }
      }
    );
    
    const result = await response.json();
    
    return {
      transactions: result.data,
      total: result.totalRecords,
      hasMore: result.nextPage
    };
  } catch (error) {
    console.error('Failed to fetch transaction history:', error);
    throw error;
  }
}

// Usage - Get first page
fetchTransactionHistory(1, 10, { action: 'invest' })
  .then(({ transactions, total, hasMore }) => {
    console.log(`Showing ${transactions.length} of ${total} transactions`);
    transactions.forEach(tx => {
      console.log(`${tx.action} - ${tx.hash} - ${tx.createdAt}`);
    });
    if (hasMore) {
      console.log('More transactions available...');
    }
  });
```

---

## 📊 **API Summary**

**Total Endpoints**: 29 Soroban-related APIs

### **By Category**:
- 👤 User Management: 4 endpoints
- 🎵 Music NFT Operations: 6 endpoints
- 💰 Sale Operations: 7 endpoints
- 🛒 Investment/Purchase: 4 endpoints
- 💸 MTO Balance/Transfer: 3 endpoints
- 📜 Transaction History: 1 endpoint
- 💵 Token Operations: 2 endpoints
- 🔧 Admin Operations: 2 endpoints

### **By Priority**:
- ⭐⭐⭐ Phase 1 (MVP): 9 endpoints
- ⭐⭐ Phase 2 (Core): 6 endpoints
- ⭐ Phase 3 (Advanced): 14 endpoints

---

## 🚀 **Getting Started Checklist**

### **Backend Setup**
- [ ] Ensure Soroban testnet is configured
- [ ] Verify contract ID is correct in config
- [ ] Test all endpoints with Postman/Insomnia

### **Frontend Setup**
- [ ] Install Freighter wallet extension
- [ ] Set up authentication flow
- [ ] Implement wallet connection
- [ ] Create API service layer
- [ ] Handle XDR signing flow
- [ ] Implement error handling

### **Phase 1 Implementation**
- [ ] User onboarding (create user)
- [ ] Browse crowdsales
- [ ] View sale details
- [ ] Investment flow
- [ ] Check MTO balance
- [ ] View NFT details

### **Testing**
- [ ] Test on Stellar testnet
- [ ] Verify wallet signing works
- [ ] Test error scenarios
- [ ] Check transaction history displays correctly
- [ ] Validate balance updates

---

## 📞 **Support & Resources**

- **Stellar Documentation**: https://developers.stellar.org/
- **Soroban Documentation**: https://soroban.stellar.org/docs
- **Freighter Wallet**: https://www.freighter.app/
- **Backend API Base URL**: `https://your-domain.com/api/v1`

---

**Document Version**: 1.0  
**Last Updated**: 2025-01-13  
**Maintained By**: Backend Team
