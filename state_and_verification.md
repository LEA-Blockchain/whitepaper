# State Model: On-Chain State & Verifiable Compression

LEA introduces a novel approach to managing state growth, one of the most significant challenges facing scalable Layer 1 blockchains. The architecture is designed to keep the computational burden on nodes to a minimum while ensuring full data availability and cryptographic verifiability of the entire chain history. This is achieved by combining on-chain state storage with a powerful **verifiable state compression** mechanism using zk-STARKs.

---

## On-Chain State: The Source of Truth

Contrary to some state-pruning models, LEA's base protocol mandates that **all final contract state is stored on-chain**. Every smart contract, including user accounts and the native coin contract, has its own dedicated storage space.

A contract's state is represented by a `state_hash` (typically a Merkle root) in its on-chain account data. This hash commits to the contract's entire internal state (e.g., balances, ownership, metadata).

### Solving the Data Availability Problem
By keeping the full state on-chain, LEA inherently solves the data availability problem.
- **Accessibility:** Any user or node can query the blockchain to retrieve the full, current state of any smart contract. There is no reliance on off-chain actors to provide this data.
- **Trustless Reconstruction:** A new node can always sync the chain and independently reconstruct the current world state by processing all historical transactions.

However, requiring new nodes to re-execute every transaction from genesis is a major scalability bottleneck. This is the problem that LEA's verifiable compression model solves.

---

## Verifiable State Compression via zk-STARKs

The core innovation in LEA's state model is not the deletion of data, but the **compression of its verification process**.

When a contract has been inactive for a defined period (e.g., N blocks), it becomes eligible for "dormancy compression." This process does not remove the contract's state from the chain. Instead, it generates a **zk-STARK proof** that serves as a cryptographic receipt, proving the validity of the contract's entire history up to its current state.

### The Compression Process:
1.  **Prover Network:** A decentralized network of provers identifies a dormant contract.
2.  **Proof Generation:** A prover fetches the contract's transaction history (linked by the `prev_tx_hash` chain). It generates a zk-STARK that proves the following:
    - **Historical Integrity:** Every transaction in the signature chain is valid and correctly signed.
    - **Execution Correctness:** The final `state_hash` of the contract is the correct result of applying this valid transaction history to its initial state.
3.  **On-Chain Attestation:** The prover submits this STARK proof to the LEA chain. The proof is attached to the dormant contract's account data.
4.  **Log Pruning (Optional):** Once the proof is on-chain, full nodes are now free to prune the detailed historical transaction logs for that specific contract, as their validity is now permanently and compactly attested to by the STARK. The final state data remains.

### Benefits of Verifiable Compression:
- **Ultra-Fast Node Synchronization:** A new node joining the network does not need to re-execute the millions of transactions belonging to a dormant contract. It only needs to perform two steps:
    1.  Download the contract's final state data (which is stored on-chain).
    2.  Verify the single, compact zk-STARK proof associated with it.
    This reduces the time to sync and validate the chain from days or weeks to minutes, regardless of the network's age.
- **Computational Relief for Validators:** Validators do not need to waste CPU cycles re-validating the histories of inactive contracts. They can rely on the cryptographic security of the STARKs.
- **Permanent Data Availability:** Because the final state is never deleted, the system avoids any risk of data being withheld, ensuring that any contract can be reactivated and used at any time.

---

## Securing the Prover Network

The liveness and integrity of the state compression system depend on a robust, decentralized network of provers. LEA secures this network through a carefully designed economic model that incentivizes participation and penalizes malicious behavior.

### The Prover Economic Model:
1.  **The Prover Subsidy Pool:** A dedicated on-chain fund, fueled by a portion of network transaction fees and protocol inflation, ensures there is always a reward available for generating proofs. This guarantees liveness even during periods of low network activity.
2.  **Staking and Slashing:** To participate, provers must stake a significant amount of $LEA. This stake acts as a bond that is slashed if the prover submits a mathematically invalid STARK or fails to meet uptime requirements. This provides a strong economic guarantee of good behavior.
3.  **The Proof Auction:** An on-chain auction mechanism ensures proofs are generated efficiently. Provers bid on the right to prove a batch of dormant contracts, with the protocol selecting the lowest valid bid. This creates a competitive market that drives down costs and scales with hardware improvements.

This system ensures that the critical function of state compression is economically sustainable, secure, and perpetually available.

---

## State Rent and Hibernation: Ensuring Long-Term Sustainability

To combat state bloat and ensure that the cost of permanent storage is accounted for, LEA implements a **Pre-Paid State Rent** model. This system is designed to be transparent and user-controlled, preventing any automatic draining of funds from user wallets.

### The State Rent Economic Cycle:
The rent mechanism is a complete economic cycle designed to be efficient, transparent, and fair.

1.  **Upfront Payment & The State Rent Fund:** When a user pays rent (either at deployment or via top-up), their $LEA is deposited into a special, protocol-managed **State Rent Fund**. This central pool holds all prepaid rent from across the network.
2.  **The `rent_paid_until` Timestamp:** From the user's perspective, the only thing that matters is the `rent_paid_until` timestamp on their contract. This timestamp is calculated based on the payment amount and the size of the contract's state. As long as this timestamp is in the future, the contract is considered paid for.
3.  **Validator Payouts:** The beneficiaries of the State Rent Fund are the **validators**, who are compensated for the service of storing the chain's data. At the end of each epoch, the protocol calculates the total accrued rent for all state stored on the network during that period. It then distributes the corresponding amount from the State Rent Fund to the active validators, proportional to their stake. This is a single, efficient bulk transaction.
4.  **Hibernation:** If a contract's `rent_paid_until` timestamp expires, it enters a grace period. If the rent is not topped up by the end of this period, the protocol hibernates the contract, moving its state to a long-term archive to free up active state space.

### Closure Rebate:
To incentivize good "garbage collection," LEA offers a **Closure Rebate**. If a developer explicitly closes a contract (freeing its state), the protocol will refund a significant portion (e.g., 30%) of the total rent that contract has ever paid. This rewards developers for keeping the chain clean.

---

## Reactivating a Hibernated Contract

When a new transaction is sent to a hibernated contract, the protocol will require the sender to pay the outstanding rent debt. Once paid, the state is automatically restored from the archive, the contract becomes fully active again, and the transaction proceeds. This ensures that no contract is ever permanently lost, while maintaining a strong economic incentive to keep the active state set clean and efficient.

This model provides the best of both worlds: the immense storage and computational savings of a zk-rollup, combined with the data availability and security guarantees of a traditional Layer 1 blockchain.

<div class="nav-buttons">
  <a class="prev" href="/transaction_lifecycle/">← Transaction Lifecycle</a>
  <a class="next" href="/security_model/">Security Model →</a>
</div>