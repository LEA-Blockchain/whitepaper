# Core Architecture: Consensus, Execution, and PODs

The LEA architecture is a two-tiered system that cleanly separates the responsibilities of consensus from execution. This design enables extreme flexibility at the application layer while maintaining the robustness and security of a minimal base layer.

---

## The Consensus Layer: A Minimalist Ordering Service

The LEA base chain is not a world computer; it is a highly optimized, stateless ordering machine. Its sole purpose is to receive transaction envelopes from the network, establish a canonical and tamper-proof sequence, and pass them to the execution layer.

### Key Responsibilities:
- **Transaction Ordering:** Utilizes a Proof of History (PoH) inspired mechanism to efficiently and verifiably timestamp and sequence incoming transactions, providing a global reference clock.
- **Data Persistence:** Guarantees that all submitted transaction envelopes are permanently recorded and made available.
- **Dispatching:** Reads the first 32 bytes of every transaction envelope to identify the `DecoderID` and dispatches the remaining `Payload` to that specific on-chain contract for processing.

### What the Consensus Layer Does NOT Do:
- **No State Interpretation:** The base protocol has no knowledge of account balances, smart contract state, or token ownership. All state is managed within the execution layer.
- **No Signature Verification:** The consensus layer does not validate signatures. It treats the transaction envelope as an opaque byte stream to be interpreted by the designated Decoder.
- **No Intrinsic Logic:** It does not contain logic for transfers, fees, or any other application-specific function.

This minimalist design makes the base protocol incredibly simple, resilient, and unlikely to require contentious upgrades.

---

## The Execution Layer: Programmable Decoders

All logic and intelligence in the LEA ecosystem reside in the execution layer, which is composed of WebAssembly (WASM) smart contracts. The central component of this layer is the **Decoder**.

A **Decoder** is a special type of smart contract (marked with the `ACCOUNT_FLAG_DECODER`) responsible for interpreting the `Payload` of a transaction. When a transaction is submitted to the network, the consensus layer invokes the contract at the specified `DecoderID`, triggering its execution.

### Decoder Responsibilities:
A Decoder is a complete, self-contained transaction processor. Its duties include:
1.  **Parsing the Payload:** Defining and deserializing the byte structure of the transaction `Payload`.
2.  **Signature Verification:** Implementing the logic to verify the transaction's signatures. This is where LEA's cryptographic agility shines, as a Decoder can be programmed to validate Ed25519, SPHINCS+, or any other desired signature scheme.
3.  **Replay Protection:** Enforcing rules to prevent the same transaction from being executed twice. While the base protocol provides a historical chain via `prev_tx_hash`, the Decoder determines how this (or an internal nonce) is used to ensure validity.
4.  **Fee Logic:** Defining and processing transaction fees. A Decoder can implement logic to deduct fees from the sender, accept payment from a third-party sponsor (meta-transactions), or even waive fees entirely.
5.  **State Transition:** Calling other smart contracts and orchestrating the state changes that constitute the transaction's business logic.

---

## PODs: Programmable Object Domains

While a Decoder provides the entry point for a transaction, a **Programmable Object Domain (POD)** represents a complete, purpose-driven application or ecosystem. A POD is a conceptual grouping of one or more Decoders and their associated smart contracts, all working together to serve a specific function.

PODs are not a protocol-enforced construct but a powerful architectural pattern that emerges from LEA's design. They allow developers to build self-contained worlds on top of LEA's shared consensus.

### Characteristics of a POD:
- **Shared Logic:** Often centered around a single, canonical Decoder or a set of standardized contracts.
- **Domain-Specific Rules:** A POD for Real-World Assets (RWA) might enforce KYC/AML checks within its Decoder, while a privacy POD would use zero-knowledge proofs.
- **Sovereign Tokenomics:** A POD can use the native $LEA token, its own custom token (e.g., `$RWA_STABLE`), or operate without any token at all.
- **Defined Interfaces:** PODs can be completely isolated or can expose public functions in their smart contracts to allow for permissioned interoperability with other PODs.

This modular structure allows for parallel innovation. The development of a complex financial POD does not interfere with or depend on the development of a decentralized social media POD, yet both can benefit from the same underlying security and data ordering guarantees of the LEA chain.

---

## Inter-POD Composability: The Decoder Handshake

To allow for safe, synchronous communication between different PODs, LEA supports a **Decoder Handshake** mechanism. This enables atomic composability, where a transaction can execute calls across multiple PODs that either all succeed or all fail together.

The process is as follows:
1.  A contract in `POD-A` wishes to call a contract in `POD-B`.
2.  It uses a special host function, `cross_pod_call()`, which triggers a "handshake."
3.  The LEA VM makes a read-only call to a standardized function on `POD-B`'s Decoder, effectively asking, "Do you permit a call from `POD-A`?"
4.  `POD-B`'s Decoder can then enforce its own policy, for example, by checking against an on-chain whitelist of trusted PODs.
5.  If the handshake is approved, the VM proceeds to execute the call. If it is denied, the transaction fails before any state is changed.

This model preserves the sovereignty and security of each POD, allowing them to define their own interaction policies, while still enabling the powerful, atomic composability that is essential for a rich DeFi and Web3 ecosystem.
