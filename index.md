---
layout: default
title: LEA Whitepaper (EN)
---

# The LEA Blockchain: A Whitepaper

This document provides a comprehensive technical overview of the LEA blockchain, a next-generation Layer 1 protocol designed for modularity, security, and verifiable state compression.

## Table of Contents

1.  [**Abstract**](./abstract.md)
    - A high-level executive summary of LEA's architecture and vision.

2.  [**Introduction: The Modular Blockchain Paradigm**](./introduction.md)
    - The core vision and philosophy behind LEA.
    - Principles: Minimal Base Layer, Developer-Centric Execution, and Future-Ready Design.

3.  [**Core Architecture: Consensus, Execution, and PODs**](./architecture.md)
    - Detailed breakdown of the separation of consensus and execution.
    - The role of the LEA Chain as a minimal ordering service.
    - The function of Decoders as programmable transaction interpreters.
    - The concept of Programmable Object Domains (PODs) as modular execution environments.

4.  [**The Transaction Lifecycle: Signature Chaining**](./transaction_lifecycle.md)
    - In-depth explanation of the `prev_tx_hash` model.
    - How signature chaining provides inherent replay protection and verifiable history.
    - The structure of a standard transaction envelope.

5.  [**State Model: On-Chain State & Verifiable Compression**](./state_and_verification.md)
    - How state is stored and managed on-chain within contracts.
    - The role of zk-STARKs for compressing state *verification*, not deleting state data.
    - How this model solves the data availability problem while enabling ultra-fast node synchronization.

6.  [**Security Layer: PQC, Account Abstraction, and Gas**](./security_model.md)
    - **Post-Quantum Cryptography (PQC):** How Decoders enable algorithm agility.
    - **Native Account Abstraction:** The dual-identity system (permanent address vs. on-chain keys) that allows for key rotation and social recovery.
    - **Gas Metering & DoS Prevention:** Ensuring network stability regardless of Decoder complexity.

7.  [**Fee & Governance**](./fee_and_governance.md)
    - The utility of the native $LEA coin.
    - POD-level token autonomy and programmable fee models (including fee sponsorship).
    - The dual governance structure: protocol-level and POD-specific.

8.  [**Tokenomics**](./tokenomics.md)
    - A detailed breakdown of the $LEA token distribution, utility, and economic model.

9.  [**Use Cases: Programmable Object Domains (PODs)**](./programmable_object_domains.md)
    - Detailed examples of PODs: RWA, Privacy, zkApps, DAO Governance, and more.
    - How PODs can interoperate or remain isolated.

10.  [**Developer Ecosystem & Tooling**](./developer_ecosystem.md)
    - The process of building and deploying a POD.
    - The LEA SDK, CLI, and other tools for developers.

11. [**Competitive Positioning**](./competitive_positioning.md)
    - A summary of LEA's advantages over existing blockchain solutions.

12. [**Roadmap**](./roadmap.md)
    - The phased plan for mainnet activation, consensus integration, and ecosystem growth.

13. [**Team & Contributors**](./team.md)
    - Information on the core team driving the project.

14. [**Legal Classification and Compliance**](./legal_classification_and_compliance.md)
    - An analysis of the legal status of the protocol and its assets.

15. [**Risk Factors**](./risk_factors.md)
    - A transparent overview of potential technical, market, and operational risks.

16. [**Glossary**](./glossary.md)
    - Definitions of key technical terms used in the whitepaper.


<div class="nav-buttons">
    <a class="prev toc" href="https://getlea.org">Back to getlea.org</a>
    <a class="next" href="/abstract/">Start →</a>
</div>

## License

This work is licensed under the MIT License. See the [LICENSE](./LICENSE) file for details.

