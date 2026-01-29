# Engineering Build

**Non-normative implementation examples for Stellar proof-of-funds attestations.**

---

## Status and Purpose

This directory contains **example implementations** of the proof-of-funds pattern documented in the [reference repository](https://github.com/Y3KDigital/ledger-proof-of-funds-reference).

**Important Clarifications:**

- ✅ These are **non-normative examples**
- ✅ They demonstrate **one possible implementation**
- ❌ They do **not define protocol behavior**
- ❌ They do **not imply Stellar endorsement**
- ❌ They are **not production-ready without review**

If there is any conflict between this build and the reference documentation, the reference documentation is authoritative.

---

## What This Is

This build exists to help Stellar engineers:

- **Inspect** raw transaction payloads
- **Verify** canonical snapshot generation
- **Test** memo format and size constraints
- **Understand** fee calculations and reserves
- **Experiment** with attestation patterns

It is a **learning and inspection tool**, not a production service.

---

## What This Is Not

This build does **not**:

- Provide custody or key management
- Guarantee correctness or security for production use
- Imply support, maintenance, or SLA commitments
- Represent official Stellar tooling or documentation
- Create liabilities for Stellar validators or protocol maintainers

---

## Intended Audience

- **Protocol Engineers**: Evaluating the pattern's Stellar compatibility
- **Security Researchers**: Auditing snapshot generation and verification
- **Integration Developers**: Understanding transaction structures before building
- **Compliance Teams**: Reviewing attestation properties and limitations

**Not intended for:**

- End users seeking production services
- Developers seeking official Stellar SDK examples
- Organizations requiring production support

---

## Technical Scope

### Included

- Canonical snapshot generation (Stellar-specific)
- SHA-256 hash computation (client-side)
- Self-payment transaction structure examples
- Compact memo format (`POF:<hash>:<cid>`)
- Fee estimation (base fee + stroops)
- Read-only account queries (Horizon `/accounts/{id}`)

### Excluded

- Private key handling (wallet-only)
- Transaction signing (wallet-delegated)
- Persistent storage (ephemeral by design)
- Production IPFS pinning (local node assumed)
- Error handling for production environments

---

## Security Considerations

### Assumptions

This build assumes:

- User has custody of their own keys
- Wallet software handles signing securely
- Horizon endpoints are trusted for read operations
- IPFS node (if used) is locally controlled

### Out of Scope

- Multi-signature coordination
- Key recovery or backup
- Regulatory compliance validation
- Production deployment hardening

### Verification

All attestations produced by this build can be independently verified using the instructions in:

**[VERIFY.md](https://github.com/Y3KDigital/ledger-proof-of-funds-reference/blob/main/VERIFY.md)**

No trust in this build is required for verification.

---

## Usage

### Prerequisites

- Understanding of Stellar transaction structure
- Familiarity with JavaScript/TypeScript
- Access to Horizon API (public or local)
- (Optional) Local IPFS node for content addressing

### Running Locally

```bash
# Clone this repository
git clone https://github.com/Y3KDigital/stellar.git
cd stellar/build

# Open in browser (static files)
# No build step required
```

### Inspecting Payloads

All transaction payloads are human-readable JSON.
Use browser DevTools to inspect:

- Network requests (Horizon API calls)
- Console logs (hash computations)
- Transaction structures (self-payment with memos)

---

## License

**Apache 2.0**

This build is provided under the Apache 2.0 license, which includes an explicit patent grant and is compatible with enterprise and institutional use.

See [LICENSE](../LICENSE) for full terms.

---

## Disclaimers

### No Warranty

This software is provided "AS IS" without warranty of any kind, express or implied.

### No Protocol Authority

This build does not define, extend, or modify Stellar protocol semantics.

### No Endorsement

Use of this build does not imply endorsement by:

- Stellar Development Foundation (SDF)
- Stellar validators or node operators
- Protocol maintainers

### No Production Claims

This build is not:

- Audited for production use
- Supported with SLAs or uptime guarantees
- Guaranteed to be maintained

---

## Contributing

Contributions that improve:

- Correctness of Stellar-specific implementations
- Clarity of transaction structure examples
- Verification reproducibility

...are welcome.

Contributions that add:

- Product features
- Marketing copy
- Non-technical content

...will be declined.

**Philosophy**: Engineering builds should remain inspectable, minimal, and educational.

---

## References

- **Reference Pattern**: [ledger-proof-of-funds-reference](https://github.com/Y3KDigital/ledger-proof-of-funds-reference)
- **Stellar Protocol Documentation**: https://developers.stellar.org/docs
- **Verification Guide**: [VERIFY.md](https://github.com/Y3KDigital/ledger-proof-of-funds-reference/blob/main/VERIFY.md)

---

## Contact

For questions about:

- **The pattern**: Open issue in [ledger-proof-of-funds-reference](https://github.com/Y3KDigital/ledger-proof-of-funds-reference)
- **Stellar-specific implementation**: Open issue in this repository
- **Protocol behavior**: Consult official Stellar documentation

Do not contact Stellar Development Foundation about this build. It is third-party tooling.
