# Roadmap

Die Entwicklung von LEA ist als stufenweiser Rollout strukturiert, der darauf ausgelegt ist, das Netzwerk schrittweise aufzubauen, zu testen und zu dezentralisieren. Die Modularität der Architektur ermöglicht parallele Entwicklungsströme, bei denen sich der Kern-Consensus-Layer parallel zu den Entwickler-Tools und den ersten Ökosystem-Anwendungen entwickelt.

---

### **Phase 1: Execution Layer & Kerninfrastruktur (Abgeschlossen)**

Diese grundlegende Phase konzentrierte sich auf den Aufbau der Kernkomponenten, die für eine funktionale, wenn auch zentralisierte, Ausführungsumgebung notwendig sind.

-   **[PASS] Start der Base Chain:** Bereitstellung der initialen Version der LEA-Chain, die in der Lage ist, Transaktions-Envelopes anzunehmen und zu ordnen.
-   **[PASS] Implementierung des programmierbaren Transaktionsformats (LIP-0009):** Etablierung der Kernstruktur `DecoderID` + `Payload`.
-   **[PASS] WASM Execution Engine:** Integration der WasmEdge-Runtime, die die Bereitstellung und Ausführung von Smart Contracts und Decodern ermöglicht.
-   **[PASS] On-Chain-State & Account-Modell:** Implementierung des On-Chain-Speichersystems für den Vertrags-State und die flexible Account-Struktur.

*Ergebnis: Ein live Single-Node-Netzwerk, das die Bereitstellung und Interaktion von Smart Contracts und Decodern unterstützt und die Machbarkeit des architektonischen Kernkonzepts beweist.*

---

### **Phase 2: Sicherheits-Härtung & Community-Bootstrapping (In Arbeit)**

Diese Phase konzentriert sich auf die Integration von Post-Quantum-Sicherheit und den Aufbau einer starken anfänglichen Benutzerbasis durch die mobile Anwendung LEA Pulse.

-   **[In Arbeit] Integration von Post-Quantum-Kryptografie:** Abschluss der Integration von SPHINCS+ und anderen PQC-Signaturschemata in die Standard-Decoder-Bibliotheken und das Wallet-Tooling.
-   **[In Arbeit] Start der LEA Pulse Mobile App (Beta):** Die LEA Pulse App ist live auf iOS und Android und dient als primäres Vehikel für das Onboarding der Community. Sie umfasst ein Non-Custodial Wallet, Bildungsaufgaben und ein XP-basiertes Belohnungssystem, um frühes Engagement zu fördern und den initialen Airdrop zu verteilen.
-   **[In Arbeit] Alpha-Release des Entwickler-SDKs:** Veröffentlichung der ersten Version des Rust-basierten SDKs, um frühen Partnern den Beginn des Baus und Tests ihrer PODs zu ermöglichen.

*Ziel: Eine zukunftssichere und sichere Plattform zu schaffen und gleichzeitig eine lebendige und engagierte Community aufzubauen, die für den Mainnet-Start bereit ist.*

---

### **Phase 3: Dezentralisierung & Konsens-Aktivierung (Anstehend)**

Dies ist die kritischste Phase, in der das Netzwerk von einem zentral geordneten System zu einer vollständig dezentralisierten, Validator-gesicherten Blockchain übergeht.

-   **[Anstehend] Bereitstellung des PoH-basierten Consensus Layers:** Aktivierung der dezentralen Consensus-Engine, die es einer erlaubnisfreien Gruppe von Validatoren ermöglichen wird, Blöcke zu produzieren.
-   **[Anstehend] Validator-Staking- und Slashing-Mechanismen:** Einführung der On-Chain-Logik, mit der Validatoren $LEA staken, Belohnungen für die Sicherung des Netzwerks verdienen und für bösartiges Verhalten bestraft (geslasht) werden.
-   **[Anstehend] Mainnet-Aktivierung:** Der offizielle Start der vollständig dezentralisierten LEA-Blockchain.
-   **[Anstehend] Aktivierung des zk-STARK-Prover-Netzwerks:** Bereitstellung und Incentivierung des dezentralen Netzwerks von Provers, die für die Erzeugung von State-Compression-Proofs für ruhende Verträge verantwortlich sind.

*Ziel: Ein produktionsreifes, sicheres und vollständig dezentralisiertes Layer-1-Protokoll zu liefern.*

---

### **Phase 4: Ökosystem-Erweiterung & Governance (Zukunft)**

Nach der Dezentralisierung des Kernprotokolls wird der Fokus darauf liegen, ein blühendes Entwickler-Ökosystem zu fördern und die Governance an die Community zu übergeben.

-   **[Zukunft] Öffentlicher Start der Entwickler-Tools:** Veröffentlichung der Version 1.0 des SDKs, der CLI und anderer Entwickler-Tools.
-   **[Zukunft] Bereitstellung von Referenz-PODs:** Start von kanonischen Open-Source-PODs für RWA, Privacy und Governance, die als Vorlagen für die Community dienen.
-   **[Zukunft] Start der On-Chain-DAO-Governance:** Aktivierung des On-Chain-Governance-Moduls, das es $LEA-Inhabern ermöglicht, über LIPs abzustimmen und die Community-Treasury zu kontrollieren.
-   **[Zukunft] Frameworks für Inter-POD-Komponierbarkeit:** Entwicklung von Standards und Werkzeugen, um eine sichere und effiziente Kommunikation zwischen verschiedenen PODs zu erleichtern.

*Ziel: LEA von einem Protokoll in ein selbsterhaltendes, von der Community gesteuertes und ständig wachsendes Ökosystem souveräner Domänen zu verwandeln.*
