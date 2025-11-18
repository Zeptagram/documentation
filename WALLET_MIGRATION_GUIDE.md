# Wallet Migration Guide: EOSIO to Stellar/Soroban

## Overview
The wallet system has been migrated from EOSIO to Stellar/Soroban blockchain. This document outlines the changes and how to use the new wallet system.

## Key Changes

### 1. **Wallet Connection Method**
- **Old (EOSIO)**: Users create wallets with private keys stored on the backend
- **New (Stellar)**: Users connect existing wallets via browser extensions (Freighter or Lobstr)

### 2. **Private Key Management**
- **Old (EOSIO)**: Backend stores encrypted private keys
- **New (Stellar)**: Private keys remain in user's wallet extension (non-custodial)

### 3. **Transaction Signing**
- **Old (EOSIO)**: Backend signs transactions
- **New (Stellar)**: Users sign transactions in their wallet extension

## Updated API Endpoints

### ✅ **Working with Stellar Wallets**

#### 1. **GET `/user/wallet/details`**
Fetches wallet details for Stellar users.

**Response:**
```json
{
  "success": true,
  "status": 200,
  "data": {
    "publicKey": "GXXX...XXX",
    "walletType": "freighter",
    "isAvailable": true,
    "createdOn": "2024-01-01T00:00:00Z",
    "sorobanUser": {
      "kyc_level": 1,
      "balance": 1000
    },
    "balance": 1000,
    "rate": 1,
    "account_created": true
  },
  "message": "Stellar wallet details fetched successfully."
}
```

#### 2. **GET `/user/wallet/balances`**
Gets all token balances for Stellar users.

**Response:**
```json
{
  "success": true,
  "status": 200,
  "data": [],
  "baseBalance": 1000,
  "publicKey": "GXXX...XXX",
  "walletType": "stellar"
}
```

#### 3. **GET `/user/wallet/singleTokenBalance?tokenName=SONG123`**
Gets balance for a specific token.

**Query Parameters:**
- `tokenName` (optional): Token name. Defaults to "BASE"

**Response:**
```json
{
  "success": true,
  "status": 200,
  "data": {
    "balance": 150,
    "tokenName": "SONG123",
    "walletType": "stellar"
  }
}
```

### ⚠️ **Deprecated for Stellar (EOS Only)**

#### 1. **POST `/user/wallet/import`**
Returns error for Stellar users. Stellar wallets are connected via browser extensions.

#### 2. **POST `/user/wallet/privateKey`**
Returns error for Stellar users. Private keys are managed by wallet extensions.

#### 3. **PUT `/user/wallet/resetPin`**
Not applicable for Stellar wallets (no backend-stored keys).

## Frontend Integration

### Connecting a Stellar Wallet

Users must connect via Freighter or Lobstr browser extensions. The backend detects the wallet type from the user model:

```javascript
// User model structure
{
  freighter: {
    publickey: "GXXX...XXX",
    createdOn: "2024-01-01T00:00:00Z"
  },
  // OR
  lobstr: {
    publickey: "GYYY...YYY",
    createdOn: "2024-01-01T00:00:00Z"
  }
}
```

### Checking Wallet Type

The backend automatically detects wallet type:
- If `freighter.publickey` or `lobstr.publickey` exists → Stellar wallet
- If `wallet.accountName` exists and length ≤ 12 → EOS wallet
- Otherwise → No wallet

## New Stellar Wallet Service

A new service class `StellarWalletManager` has been created in:
`app/v1/modules/wallet/stellarWalletService.js`

### Available Methods:

```javascript
const StellarWallet = require('../wallet/stellarWalletService');

// Get wallet details
await StellarWallet.getWalletDetails(userId);

// Get MTO balance
await StellarWallet.getMTOBalance(publicKey, tokenName);

// Get all token balances
await StellarWallet.getTokenBalances(userId);

// Check wallet exists
await StellarWallet.checkWalletExists(userId);

// Get single token balance
await StellarWallet.getSingleTokenBalance(userId, tokenName);
```

## Soroban Contract Integration

All Stellar wallet functions use the Soroban smart contract module:

```javascript
const soroban = require('../soroban/index');

// Fetch user data from contract
await soroban.fetch_user_data(publicKey);

// Get balance
await soroban.balance(publicKey);

// Get MTO balance
await soroban.get_mto_balance(publicKey, tokenName);

// Get user's NFT data
await soroban.fetch_user_nft_data(publicKey);
```

## Migration Path for Existing Users

### For Users with EOS Wallets:
1. Old EOS wallet endpoints continue to work
2. Users can optionally connect a Stellar wallet (Freighter/Lobstr)
3. Once Stellar wallet is connected, Stellar endpoints take precedence

### For New Users:
1. Must connect Freighter or Lobstr wallet
2. Backend creates Soroban user via `/soroban/user` endpoint
3. All operations use Soroban smart contracts

## Testing

### Test Stellar Wallet Endpoints:

```bash
# 1. Get wallet details
curl -X GET http://localhost:3000/api/v1/user/wallet/details \
  -H "x-auth-token: YOUR_JWT_TOKEN"

# 2. Get token balances
curl -X GET http://localhost:3000/api/v1/user/wallet/balances \
  -H "x-auth-token: YOUR_JWT_TOKEN"

# 3. Get single token balance
curl -X GET "http://localhost:3000/api/v1/user/wallet/singleTokenBalance?tokenName=SONG123" \
  -H "x-auth-token: YOUR_JWT_TOKEN"
```

## Security Considerations

### Stellar Wallets (Non-Custodial):
✅ Private keys never leave user's device  
✅ Users control their own keys  
✅ Transactions signed in wallet extension  
✅ Backend only reads public data from blockchain  

### EOS Wallets (Legacy, Custodial):
⚠️ Private keys stored encrypted on backend  
⚠️ Backend signs transactions on behalf of users  
⚠️ Consider migrating to Stellar for better security  

## Troubleshooting

### Error: "No Stellar wallet connected"
**Solution**: User must connect Freighter or Lobstr wallet extension first.

### Error: "Stellar wallet found but user not registered on Soroban contract"
**Solution**: Call `POST /soroban/user` to register the user on-chain.

### Error: "This endpoint supports EOS wallets only"
**Solution**: Use the Soroban-specific endpoints for Stellar wallet operations.

## Support

For questions or issues:
1. Check the Soroban API guide: `SOROBAN_API_FRONTEND_GUIDE.md`
2. Review the Stellar wallet service: `app/v1/modules/wallet/stellarWalletService.js`
3. Review the Soroban module: `app/v1/modules/soroban/index.js`

---

**Last Updated**: 2025-01-13  
**Migration Status**: ✅ Complete  
**Backward Compatibility**: ✅ EOS wallets still supported
