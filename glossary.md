# Glossary

A list of key terms and concepts central to the LEA blockchain architecture.

---

-   **Consensus Layer**
    -   The foundational Layer 1 of the LEA blockchain. Its sole responsibilities are to guarantee the permanent, ordered, and verifiable sequencing of transaction envelopes and dispatch them to the appropriate Decoder. It is stateless and agnostic to execution logic.

-   **Decoder**
    -   A specialized smart contract, written in WASM, that is responsible for interpreting the `Payload` of a transaction. A Decoder defines all the rules for a transaction's validity, including signature verification, replay protection, fee logic, and state transition instructions.

-   **Execution Layer**
    -   The environment where Decoders and other smart contracts run. This layer is where all state transitions, business logic, and validation occur, fully decoupled from the base Consensus Layer.

-   **LEA Pulse**
    -   The official mobile application for the LEA ecosystem. It serves as a non-custodial wallet and the primary tool for community bootstrapping, featuring educational tasks, rewards, and early access to the network.

-   **LIP (LEA Improvement Proposal)**
    -   A formal document proposing a change or standard for the LEA protocol or its surrounding ecosystem. LIPs are the primary mechanism for protocol-level governance.

-   **Payload**
    -   The opaque byte stream that follows the `DecoderID` in a transaction envelope. Its structure and meaning are defined entirely by the logic of the designated Decoder.

-   **POD (Programmable Object Domain)**
    -   A modular, application-specific ecosystem built on LEA's consensus layer. A POD consists of one or more Decoders and their associated smart contracts, creating a sovereign execution environment with its own rules, tokens, and governance.

-   **Post-Quantum Cryptography (PQC)**
    -   A class of cryptographic algorithms designed to be secure against attacks from both classical and quantum computers. LEA supports PQC through its flexible Decoder system, allowing applications to adopt quantum-resistant signature schemes.

-   **Signature Chaining**
    -   LEA's native replay protection mechanism. Each transaction from an account must reference the hash of the previous transaction from that same account (`prev_tx_hash`), creating a verifiable, chronological chain of operations.

-   **State (On-Chain)**
    -   The data stored by a smart contract (e.g., token balances, ownership records). In LEA, all final contract state is stored directly on the blockchain to ensure data availability.

-   **Transaction Envelope**
    -   The raw data structure submitted to the LEA network. It consists of a 32-byte `DecoderID` and a variable-length `Payload`.

-   **Verifiable State Compression**
    -   The process of using zk-STARKs to cryptographically prove the correctness of a dormant contract's entire transaction history. This allows nodes to validate the contract's current state without re-executing its history, dramatically speeding up network synchronization while preserving full data availability.

-   **zk-STARK (Zero-Knowledge Scalable Transparent Argument of Knowledge)**
    -   A type of cryptographic proof system that allows one party to prove the integrity of a computation to another without revealing the underlying data. LEA uses STARKs for verifiable state compression.
    

<div class="nav-buttons">
  <a class="prev" href="/risk_factors/">← Risk Factors</a>
  <a class="toc" href="/">Table Of Contents</a>
  <a class="next" href="/">End (Back to Overview)</a>
</div>