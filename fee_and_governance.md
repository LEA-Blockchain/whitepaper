# Fee & Governance

The LEA protocol features a dual-layered economic and governance model that mirrors its architecture. The native **$LEA** coin secures the base consensus layer and governs protocol-wide standards, while each **Programmable Object Domain (POD)** is free to implement its own sovereign tokenomics and governance, creating a rich and diverse ecosystem.

---

## The $LEA Coin: Securing the Foundation

The $LEA coin is the native asset of the consensus layer, with its primary utility focused on securing the network and governing its core functions.

### Utility of $LEA:
- **Staking for Consensus:** Validators must stake $LEA to participate in the block production and transaction ordering process. Malicious behavior (e.g., double-signing) results in the slashing of their staked coins, ensuring the economic security of the network.
- **Protocol-Level Governance:** $LEA coin holders have the right to propose and vote on **LEA Improvement Proposals (LIPs)**. This includes decisions on core protocol upgrades, changes to the validator set requirements, or modifications to the base fee structure.
- **Prover Network Incentivization:** The decentralized network of provers responsible for generating zk-STARKs for dormant contracts is rewarded in $LEA coins. A portion of network transaction fees or protocol inflation can be allocated to fund this critical ecosystem service.
- **Core Infrastructure Fees:** While PODs can define their own fee tokens, interacting with certain core protocol functions, such as registering a new Decoder in a canonical public registry, may require a fee paid in $LEA.

---

## POD-Level Economic Autonomy

LEA's architecture grants complete economic sovereignty to each POD. This flexibility allows developers to design sustainable models tailored to their specific application, rather than being constrained by a global, one-size-fits-all model.

### The Value-Capture Fee Model: Aligning Developer and User Incentives
LEA introduces a novel fee model designed to reward developers for creating value without creating perverse incentives. Instead of a fee-sharing model based on gas (which would incentivize inefficient code), LEA separates the network fee from an optional developer fee.

1.  **Network Gas Fee:** This is the standard fee paid to validators for processing a transaction. It is based on the computational resources (gas) consumed. 100% of this fee goes to the validator to secure the network.
2.  **Developer Value-Capture Fee:** A developer can choose to add a small, fixed fee to their smart contracts. This fee is defined by the developer (e.g., 0.01 USDC) and is paid directly to their address upon execution.

This model aligns incentives:
-   **Developers** are rewarded for creating popular, high-utility contracts that are executed often.
-   **Users** pay a predictable, transparent fee and benefit from developers being incentivized to write efficient, low-gas code.
-   **Validators** are compensated fairly for the resources they provide.

The LEA SDK and wallet tooling will enforce transparency, explicitly showing users both the network fee and the developer fee before a transaction is signed.

### Programmable Economic Policies:
Beyond the Value-Capture fee, the logic for transaction fees is defined entirely within a POD's Decoder contract, enabling a wide range of innovative models:
- **Fee Token Choice:** A DEX POD could require network fees to be paid in its own governance token, while an RWA POD could mandate fees be paid in a stablecoin like USDC for predictable operational costs.
- **Fee Sponsorship (Meta-Transactions):** A Decoder can be programmed to allow a third party to pay the transaction fee on behalf of the user. This is a game-changer for user onboarding, as dApps can sponsor a new user's first few transactions, creating a "gasless" experience.
- **Sovereign Token Ecosystems:** Each POD can function as its own micro-economy, with tokens that serve various purposes like governance, utility access, or profit-sharing, all independent of the $LEA coin.

---

## Dual-Layer Governance

Governance on LEA operates on two distinct levels:

1.  **Protocol Governance (LIPs):**
    - **Scope:** Affects the entire LEA network. This includes the consensus engine, the WASM runtime, the staking module, and other core components.
    - **Participants:** $LEA coin holders and validators.
    - **Mechanism:** A formal, on-chain voting process based on staked $LEA to approve or reject LIPs.

2.  **POD Governance (Application-Level):**
    - **Scope:** Affects only a single POD. This includes upgrading the POD's Decoder, changing its fee model, or managing its treasury.
    - **Participants:** Defined by the POD's own rules. Participants could be holders of the POD's native token, members of a multi-sig, or even whitelisted addresses.
    - **Mechanism:** Implemented entirely within the POD's smart contracts. A POD could use a simple multi-sig, a complex DAO with token-weighted voting, or have no governance at all (i.e., be an immutable contract).

This dual structure ensures that the core protocol remains stable and secure under the stewardship of $LEA stakeholders, while individual applications (PODs) retain the autonomy to innovate and govern themselves as they see fit.
