# Glossar

Eine Liste von Schlüsselbegriffen und Konzepten, die für die Architektur der LEA-Blockchain von zentraler Bedeutung sind.

---

-   **Consensus Layer**
    -   Der grundlegende Layer 1 der LEA-Blockchain. Seine alleinige Verantwortung besteht darin, die permanente, geordnete und verifizierbare Sequenzierung von Transaktions-Envelopes zu garantieren und sie an den entsprechenden Decoder weiterzuleiten. Er ist zustandslos und agnostisch gegenüber der Ausführungslogik.

-   **Decoder**
    -   Ein spezialisierter Smart Contract, geschrieben in WASM, der für die Interpretation des `Payloads` einer Transaktion verantwortlich ist. Ein Decoder definiert alle Regeln für die Gültigkeit einer Transaktion, einschließlich Signaturverifizierung, Replay Protection, Gebührenlogik und Anweisungen für den State Transition.

-   **Execution Layer**
    -   Die Umgebung, in der Decoders und andere Smart Contracts ausgeführt werden. Auf dieser Schicht finden alle Zustandsübergänge, Geschäftslogiken und Validierungen statt, vollständig entkoppelt vom Basis-Consensus-Layer.

-   **LEA Pulse**
    -   Die offizielle mobile Anwendung für das LEA-Ökosystem. Sie dient als Non-Custodial Wallet und als primäres Werkzeug für das Community-Bootstrapping, mit Bildungsaufgaben, Belohnungen und frühem Zugang zum Netzwerk.

-   **LIP (LEA Improvement Proposal)**
    -   Ein formelles Dokument, das eine Änderung oder einen Standard für das LEA-Protokoll oder sein umgebendes Ökosystem vorschlägt. LIPs sind der primäre Mechanismus für die Protokollebenen-Governance.

-   **Payload**
    -   Der opake Bytestream, der auf die `DecoderID` in einem Transaction Envelope folgt. Seine Struktur und Bedeutung werden vollständig durch die Logik des designierten Decoders definiert.

-   **POD (Programmable Object Domain)**
    -   Ein modulares, anwendungsspezifisches Ökosystem, das auf dem Consensus Layer von LEA aufgebaut ist. Ein POD besteht aus einem oder mehreren Decodern und den zugehörigen Smart Contracts und schafft so eine souveräne Ausführungsumgebung mit eigenen Regeln, Token und Governance.

-   **Post-Quantum Cryptography (PQC)**
    -   Eine Klasse von kryptografischen Algorithmen, die so konzipiert sind, dass sie gegen Angriffe von sowohl klassischen als als auch Quantencomputern sicher sind. LEA unterstützt PQC durch sein flexibles Decoder-System, das es Anwendungen ermöglicht, quantenresistente Signaturschemata zu übernehmen.

-   **Signature Chaining**
    -   LEAs nativer Replay-Protection-Mechanismus. Jede Transaktion von einem Konto muss auf den Hash der vorherigen Transaktion desselben Kontos (`prev_tx_hash`) verweisen, wodurch eine verifizierbare, chronologische Kette von Operationen entsteht.

-   **State (On-Chain)**
    -   Die von einem Smart Contract gespeicherten Daten (z. B. Token-Guthaben, Besitzurkunden). In LEA wird der gesamte endgültige Vertrags-State direkt auf der Blockchain gespeichert, um die Datenverfügbarkeit zu gewährleisten.

-   **Transaction Envelope**
    -   Die rohe Datenstruktur, die an das LEA-Netzwerk übermittelt wird. Sie besteht aus einer 32-Byte `DecoderID` und einem `Payload` variabler Länge.

-   **Verifiable State Compression**
    -   Der Prozess der Verwendung von zk-STARKs, um die Korrektheit der gesamten Transaktionshistorie eines ruhenden Vertrags kryptografisch zu beweisen. Dies ermöglicht es Nodes, den aktuellen Zustand des Vertrags zu validieren, ohne seine Historie erneut ausführen zu müssen, was die Netzwerksynchronisierung drastisch beschleunigt und gleichzeitig die volle Datenverfügbarkeit erhält.

-   **zk-STARK (Zero-Knowledge Scalable Transparent Argument of Knowledge)**
    -   Ein Typ eines kryptografischen Beweissystems, das es einer Partei ermöglicht, die Integrität einer Berechnung gegenüber einer anderen zu beweisen, ohne die zugrunde liegenden Daten preiszugeben. LEA verwendet STARKs für die Verifiable State Compression.
