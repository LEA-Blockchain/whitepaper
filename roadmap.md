# Roadmap

LEA’s development is structured as a phased rollout, designed to progressively build, test, and decentralize the network. The architecture's modularity allows for parallel development streams, with the core consensus layer evolving alongside the developer tooling and initial ecosystem applications.

---

### **Phase 1: Execution Layer & Core Infrastructure (Completed)**

This foundational phase focused on building the core components necessary for a functional, albeit centralized, execution environment.

-   **[PASS] Launch of the Base Chain:** Deployed the initial version of the LEA chain, capable of accepting and ordering transaction envelopes.
-   **[PASS] Implementation of the Programmable Transaction Format (LIP-0009):** Established the core `DecoderID` + `Payload` structure.
-   **[PASS] WASM Execution Engine:** Integrated the WasmEdge runtime, enabling the deployment and execution of smart contracts and Decoders.
-   **[PASS] On-Chain State & Account Model:** Implemented the on-chain storage system for contract state and the flexible account structure.

*Result: A live, single-node network capable of supporting the deployment and interaction of smart contracts and Decoders, proving the viability of the core architectural concept.*

---

### **Phase 2: Security Hardening & Community Bootstrapping (In Progress)**

This phase focuses on integrating post-quantum security and building a strong initial user base through the LEA Pulse mobile application.

-   **[In Progress] Post-Quantum Cryptography Integration:** Finalizing the integration of SPHINCS+ and other PQC signature schemes into the standard Decoder libraries and wallet tooling.
-   **[In Progress] LEA Pulse Mobile App Launch (Beta):** The LEA Pulse app is live on iOS and Android, serving as the primary vehicle for community onboarding. It includes a non-custodial wallet, educational tasks, and an XP-based reward system to incentivize early engagement and distribute the initial airdrop.
-   **[In Progress] Developer SDK Alpha Release:** Releasing the first version of the Rust-based SDK to allow early partners to begin building and testing their PODs.

*Goal: To create a future-proof and secure platform while simultaneously building a vibrant and engaged community ready for the mainnet launch.*

---

### **Phase 3: Decentralization & Consensus Activation (Upcoming)**

This is the most critical phase, where the network transitions from a centrally-ordered system to a fully decentralized, validator-secured blockchain.

-   **[Upcoming] Deployment of the PoH-based Consensus Layer:** Activating the decentralized consensus engine that will allow a permissionless set of validators to produce blocks.
-   **[Upcoming] Validator Staking and Slashing Mechanics:** Introducing the on-chain logic for validators to stake $LEA, earn rewards for securing the network, and be penalized (slashed) for malicious behavior.
-   **[Upcoming] Mainnet Activation:** The official launch of the fully decentralized LEA blockchain.
-   **[Upcoming] Activation of the zk-STARK Prover Network:** Deploying and incentivizing the decentralized network of provers responsible for generating state compression proofs for dormant contracts.

*Goal: To deliver a production-ready, secure, and fully decentralized Layer 1 protocol.*

---

### **Phase 4: Ecosystem Expansion & Governance (Future)**

With the core protocol decentralized, the focus will shift to fostering a thriving developer ecosystem and transitioning governance to the community.

-   **[Future] Public Launch of Developer Tooling:** Releasing the 1.0 version of the SDK, CLI, and other developer tools.
-   **[Future] Deployment of Reference PODs:** Launching canonical, open-source PODs for RWA, Privacy, and Governance to serve as templates for the community.
-   **[Future] Launch of On-Chain DAO Governance:** Activating the on-chain governance module, allowing $LEA holders to vote on LIPs and control the community treasury.
-   **[Future] Inter-POD Composability Frameworks:** Developing standards and tools to facilitate safe and efficient communication between different PODs.

*Goal: To transition LEA from a protocol into a self-sustaining, community-governed, and ever-expanding ecosystem of sovereign domains.*

<div class="nav-buttons">
  <a class="prev" href="/competitive_positioning/">← Competitive Positioning</a>
  <a class="next" href="/team/">Team & Contributors →</a>
</div>