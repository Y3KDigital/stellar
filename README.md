# Stellar Proof of Funds Pattern

**Protocol-specific documentation for implementing ledger-native proof-of-funds attestations on Stellar.**

This repository documents how the [Ledger Proof of Funds reference pattern](https://github.com/Y3KDigital/ledger-proof-of-funds-reference) maps to Stellar primitives, constraints, and semantics.

---

## Reference Implementation

For the protocol-neutral pattern specification, see:  
**[ledger-proof-of-funds-reference](https://github.com/Y3KDigital/ledger-proof-of-funds-reference)**

This repository provides Stellar-specific context only.

---

## Stellar-Specific Design

### Transaction Type: Self-Payment

The pattern uses minimal self-payment transactions for on-chain attestation anchoring.

**Why Self-Payment?**

- Canonical, well-understood Stellar pattern
- Produces ledger entry without value transfer
- Allows memo inclusion without semantic overload
- Minimal cost (base fee + negligible amount)

**Transaction Structure:**

```json
{
  "type": "payment",
  "source_account": "GBVOL67TMUQBGL4TZYNMY3ZQ5WGQYFPFD5VJRWXR72VA33VFNL225PL5",
  "destination": "GBVOL67TMUQBGL4TZYNMY3ZQ5WGQYFPFD5VJRWXR72VA33VFNL225PL5",
  "asset": {
    "type": "native"
  },
  "amount": "0.0000001"
}
```

### Memo Format

Attestation data is encoded in transaction memo as compact text.

**Format:**

```
POF:<hash>:<cid>
```

**Components:**

- **Prefix**: `POF` (Proof Of Funds)
- **Hash**: First 32 characters of SHA-256 hash (hex)
- **CID**: Full IPFS Content Identifier

**Example:**

```
POF:a3c5f7e9d2b4f8c1a6e3d7f2b9c4e1a8:QmXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
```

### Memo Size Limits

- Stellar `MEMO_TEXT`: 28 bytes max
- Pattern uses abbreviated format to fit within constraint
- Full hash available via IPFS retrieval
- Trade-off: compactness vs. full hash in memo

---

## Snapshot Contents

Stellar snapshots capture publicly readable account state via Horizon API:

### API Endpoint Used

```
GET /accounts/{account_id}
```

### Snapshot Schema

```json
{
  "address": "GBVOL67TMUQBGL4TZYNMY3ZQ5WGQYFPFD5VJRWXR72VA33VFNL225PL5",
  "balances": [
    {
      "asset_type": "native",
      "balance": "10000.0000000"
    },
    {
      "asset_type": "credit_alphanum4",
      "asset_code": "USDC",
      "asset_issuer": "GA5ZSEJYB37JRC5AVCIA5MOP4RHTM335X2KGX3IHOJAPP5RE34K4KZVN",
      "balance": "5000.0000000",
      "limit": "10000.0000000"
    }
  ],
  "subentry_count": 2,
  "num_sponsoring": 0,
  "num_sponsored": 0
}
```

---

## Fee Considerations

### Base Reserve

- Minimum balance: 1 XLM (2 × 0.5 XLM base reserve)
- Per-subentry reserve: 0.5 XLM
- Self-payment does not increase subentry count

### Transaction Fees

- Base fee: 100 stroops (0.00001 XLM)
- Self-payment amount: 0.0000001 XLM (1 stroop)
- Total cost: ~0.0000101 XLM per attestation

---

## Verification on Stellar

### Step 1: Locate Transaction

Use Horizon API or Stellar explorers:

```bash
# Via Horizon
GET https://horizon.stellar.org/transactions/{tx_hash}
```

### Step 2: Extract Memo

Check transaction memo field:

```json
{
  "memo": "POF:a3c5f7e9d2b4f8c1a6e3d7f2b9c4e1a8:QmXXX...",
  "memo_type": "text"
}
```

### Step 3: Verify Hash

1. Parse memo: `POF:<hash32>:<cid>`
2. Retrieve full snapshot from IPFS using `cid`
3. Recompute SHA-256 hash of snapshot
4. Verify first 32 characters match memo hash

### Step 4: Confirm Self-Payment

- Verify `source_account == destination`
- Confirms no value transfer occurred
- Transaction exists solely for attestation

### Step 5: Confirm Immutability

- Once closed, Stellar ledgers are immutable
- Ledger sequence provides canonical timestamp
- No trust in operator required

---

## Protocol Compatibility

### Current (Protocol 20)

- Works on Stellar mainnet today
- No Soroban contracts required
- Uses standard payment operations + memos

### Future

- **Soroban**: Could embed verification logic in smart contracts
- **Claimable Balances**: Extend snapshot schema if needed
- **Liquidity Pools**: Add pool positions to snapshot
- **Protocol 21+**: Pattern remains valid

Pattern is forward-compatible with protocol evolution.

---

## Stellar-Specific Constraints

| Constraint           | Value                 | Impact                          |
| -------------------- | --------------------- | ------------------------------- |
| Memo size            | 28 bytes (text)       | Abbreviated format required     |
| Base reserve         | 1 XLM                 | Account must exist              |
| Transaction fee      | 0.00001 XLM           | Negligible cost                 |
| Confirmation time    | 5-7 seconds           | Fast finality                   |
| Self-payment limit   | No practical limit    | Can attest repeatedly           |
| Memo encoding        | UTF-8 text            | Simple, no hex conversion       |

---

## Why Not Data Entries or Soroban?

### Data Entries

Avoided because:

- Introduce state management complexity
- Increase reserve requirements
- Couple attestation lifecycle to account state
- Unnecessary for one-way attestation

### Soroban Contracts

Avoided because:

- Pattern works on pre-Soroban Stellar
- Smart contracts add execution and audit overhead
- Increases coupling to evolving contract semantics
- Self-payment is simpler and more portable

**Principle**: Use simplest primitive that satisfies requirements.

---

## Examples

See [examples/](./examples/) for:

- Complete self-payment transaction payloads
- Memo format examples
- Horizon API responses
- Stellar Explorer links to real attestations

---

## Security Model

### Read-Only Access

- Queries use public Horizon endpoints
- No private key access required for snapshot generation
- User signs only the attestation payment

### No Protocol Risk

- Uses existing, well-understood payment primitive
- Does not introduce new operation types
- Does not modify consensus rules
- Does not create liabilities for validators

---

## References

- **Reference Pattern**: [ledger-proof-of-funds-reference](https://github.com/Y3KDigital/ledger-proof-of-funds-reference)
- **Stellar Documentation**: https://developers.stellar.org/docs
- **Payment Operation**: https://developers.stellar.org/docs/fundamentals-and-concepts/list-of-operations#payment
- **Horizon API**: https://developers.stellar.org/api

---

## License

MIT License

This documentation is intentionally protocol-focused and implementation-agnostic. Stellar developers are encouraged to reference, fork, or adapt this pattern.
