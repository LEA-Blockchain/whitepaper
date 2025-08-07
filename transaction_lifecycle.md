# The Transaction Lifecycle: Signature Chaining

Transactions in LEA are designed to be inherently secure, ordered, and verifiable. Instead of relying on a globally managed nonce, LEA implements a **per-account signature chain**. Each new transaction cryptographically links to the previous transaction from the same account, creating an unbreakable, linear history of operations for every contract and user.

---

## Transaction Envelope Structure

At the most fundamental level, every transaction submitted to the LEA network is an "envelope" with two components:

| Field | Size (bytes) | Description |
|---|---|---|
| `DecoderID` | 32 | The 32-byte address of the on-chain Decoder contract responsible for interpreting this transaction. |
| `Payload` | Variable | An opaque byte stream containing all the data the Decoder needs to execute the transaction. |

The consensus layer only interacts with the `DecoderID`. The structure and meaning of the `Payload` are defined entirely by the Decoder's logic.

---

## The Signature Chain Model

For a standard Decoder implementing the signature chain model, the `Payload` is structured to include a reference to the account's previous transaction.

A typical `Payload` might be conceptually broken down as follows:

```
Payload = {
  // Core Chaining and Context
  "sender_address": "0x...",       // The address of the account initiating the transaction.
  "prev_tx_hash": "0x...",         // The hash of the sender's previous transaction.

  // Execution Instructions
  "call_data": {
    "target_contract": "0x...",   // The contract to be called.
    "method_signature": "0x...",  // Identifier for the function to execute.
    "parameters": [...]           // Arguments for the function.
  },

  // Authorization
  "signature": "0x..."             // Signature over the hash of all fields above.
}
```

### The `prev_tx_hash` Field
This is the cornerstone of the signature chain.
- For an account's very first transaction, this field is set to a known constant (e.g., the zero hash).
- For every subsequent transaction, it **must** contain the hash of the transaction that last modified the account's state.
- The Decoder is responsible for fetching the account's current `latest_tx_hash` from its on-chain state and verifying that it matches the `prev_tx_hash` in the incoming transaction.

This mechanism provides robust, built-in replay protection without relying on a centralized or sequential nonce manager. An old transaction cannot be replayed because its `prev_tx_hash` will not match the account's current `latest_tx_hash`.

---

## Transaction Lifecycle Stages

1.  **Creation (Client-Side):**
    - A user's wallet constructs the transaction `Payload`.
    - It retrieves the `latest_tx_hash` for the user's account from a node.
    - It sets the `prev_tx_hash` field to this value.
    - It hashes the canonical representation of the `Payload` (excluding the signature field).
    - It signs this hash with the user's private key.
    - The wallet assembles the final envelope: the `DecoderID` followed by the signed `Payload`.

2.  **Submission & Ordering (Consensus Layer):**
    - The transaction envelope is broadcast to the network.
    - The LEA consensus layer receives the envelope, assigns it a sequence number and timestamp via Proof of History, and includes it in a block. No validation of the payload occurs here.

3.  **Dispatch (Consensus -> Execution):**
    - The consensus engine reads the 32-byte `DecoderID` from the envelope.
    - It invokes the `execute` or `decode` function on the smart contract at that address, passing the entire `Payload` as an argument.

4.  **Execution (Decoder Contract):**
    - The Decoder contract receives the `Payload`.
    - It deserializes the `Payload` according to its own defined format.
    - **It performs all critical validation checks:**
        - Verifies that the `prev_tx_hash` matches the `latest_tx_hash` stored in the sender's account.
        - Verifies the `signature` against the transaction data and the public key associated with the sender's account.
        - Processes fee logic.
    - If all checks pass, it executes the `call_data`, invoking the target contract and updating its state.
    - As a final, atomic step, it updates the sender's account state, setting its `latest_tx_hash` to the hash of the current, valid transaction.

This lifecycle ensures that while the base protocol remains simple, the execution logic enforced by the Decoder is comprehensive and secure, with each account's history forming a cryptographically-linked chain.

---

## Receiving the Execution Result

After a transaction is successfully executed, the client that submitted it receives an **`execution_result`**. This is a binary blob of data containing ephemeral, off-chain information returned by the smart contracts that were involved in the transaction.

### Purpose and Characteristics:
-   **Ephemeral Data:** The `execution_result` is designed to return lightweight data like status codes, calculated values (e.g., a trade price), or newly created account addresses directly to the user without bloating the permanent on-chain state.
-   **Not Part of Consensus:** This data is explicitly **not** part of the consensus state. It is metadata returned by the node. This means clients must treat it as **advisory and untrusted**. Any critical data returned in the result should be re-verified against on-chain state before being used in subsequent transactions.
-   **Strictly Limited:** To prevent abuse, the total size of the `execution_result` is capped at 64 KB. If a transaction attempts to return more data than this, the entire transaction is rejected.

This mechanism provides a highly efficient way for smart contracts to communicate information back to the end-user, completing the transaction lifecycle.

<div class="nav-buttons">
  <a class="prev" href="/architecture/">← Architecture</a>
  <a class="next" href="/state_and_verification/">State and Verification →</a>
</div>