# Security Layer: PQC, Account Abstraction, and Gas

Security in the LEA ecosystem is a multi-faceted concept, enforced through a combination of cryptographic agility, a flexible account model, and strict resource metering. By delegating validation logic to the execution layer, LEA empowers developers to build applications with tailored, future-proof security guarantees.

---

## Post-Quantum Cryptography (PQC) and the Base Standard

The advent of quantum computing poses a long-term existential threat to classical cryptographic schemes. LEA is designed to be quantum-resistant through **cryptographic agility**, where each POD can define its own signature scheme.

To provide a secure default, the standard LEA account model and Base Decoder implement a **dual-signature scheme**:
- **Ed25519:** A mature, highly efficient classical algorithm used for frequent, low-stakes transactions.
- **Falcon-512:** A NIST-standardized PQC algorithm used for high-stakes operations like key rotation or large value transfers. Falcon is chosen for its efficiency and relatively small signature size, minimizing on-chain costs.

On-chain logic within the account contract defines which key is required for which action, ensuring that a compromise of the "hot" Ed25519 key cannot drain an account's core assets. As new PQC standards emerge, they can be adopted by deploying new Decoders **without requiring a network-wide hard fork**.

---

## Native Account Abstraction & Key Management

LEA implements account abstraction at the most fundamental level by decoupling a user's permanent identity (their address) from their signing credentials (their keys).

- **Permanent Address:** An account's address is generated once and is permanent.
- **On-Chain Key Registry:** The authoritative public keys (e.g., an Ed25519 key and a Falcon-512 key) are stored in the account's on-chain state, not in the address itself.

This separation enables powerful key management features:

- **Key Rotation:** A user can authorize a transaction with their high-security key to replace the public keys stored on-chain. This is crucial for mitigating the risk of a compromised key.
- **Social Recovery & Multi-Sig:** The logic for updating keys can be arbitrarily complex, allowing for 3-of-5 social recovery, hardware security module integration, or corporate multi-sig policies.

---

## The Account Escape Hatch: Guaranteeing User Access

A critical security promise of LEA is that users can **never be locked out of their accounts**. A POD could maliciously or accidentally create a situation where a user cannot pay the required fee token to access their assets. The Account Escape Hatch prevents this.

It is a protocol-level guarantee that certain critical functions on an account can **always be paid for using the native $LEA token**, regardless of the POD's rules.

- **Standardized Recovery Interface:** All standard account contracts must implement an `IAccountRecovery` interface. This includes functions like `recover_funds()` and `rotate_pqc_key()`.
- **$LEA for Gas:** When a user calls a function on this interface, the LEA protocol allows the transaction's gas fee to be paid in $LEA, bypassing the POD's custom fee logic.

This mechanism gives users a permanent, sovereign backdoor to their own accounts, allowing them to rescue funds or rotate keys without relying on the goodwill or stability of any single POD. It provides the benefits of a flexible fee system without compromising the user's ultimate control over their assets.

---

## Multi-Dimensional Gas Model and DoS Prevention

To ensure network stability and prevent Denial-of-Service (DoS) attacks, LEA employs a multi-dimensional gas model that requires transactions to pay for every resource they consume. The total network fee is the sum of three parts:

1.  **Inclusion Fee:** A small, flat fee based on the transaction's byte size. This pays for the network bandwidth and storage required to propagate the transaction.
2.  **Invocation Fee:** A fixed fee for loading a Decoder's WASM bytecode into the VM. This fee can be proportional to the size of the WASM module, making it economically non-viable to spam the network with calls to large, complex Decoders.
3.  **Execution Fee:** The traditional `gasUsed * gasPrice` model that pays for the computational steps during execution.

If a transaction's execution exceeds its specified `gasLimit`, it is immediately halted, and any state changes are reverted. However, the inclusion and invocation fees are still paid to the block producer.

### The Payout Mechanism: The Block Reward Pool
Gas fees for computation are paid directly to the validator who produces the block. To ensure this process is atomic and robust, the protocol uses a temporary holding account for each block.

1.  **Collection:** As transactions are executed within a block, their corresponding gas fees are collected into a temporary, in-memory **"Block Reward Pool"**.
2.  **Finalization:** Upon the successful validation and finalization of the block, the entire balance of this pool is transferred to the block producer's address in a single, final operation.

This all-or-nothing approach guarantees that validators are compensated for valid work, simplifies the protocol's accounting, and provides future flexibility for implementing more advanced fee structures, such as fee burning.

---

## Replay Protection via Signature Chaining

As detailed in the Transaction Lifecycle chapter, LEA's primary replay protection mechanism is the `prev_tx_hash` field. By requiring each transaction to reference the hash of the last valid transaction for that account, the protocol ensures a strict, verifiable ordering. A transaction captured by a malicious actor cannot be replayed because its `prev_tx_hash` will no longer match the `latest_tx_hash` stored on the sender's account, causing the Decoder to reject it.

<div class="nav-buttons">
  <a class="prev" href="/state_and_verification/">← State and Verification</a>
  <a class="toc" href="/">Table Of Contents</a>
  <a class="next" href="/fee_and_governance/">Fee and Governance →</a>
</div>