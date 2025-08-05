# Kernarchitektur: Konsens, Ausführung und PODs

Die LEA-Architektur ist ein zweistufiges System, das die Verantwortlichkeiten von Konsens und Ausführung sauber voneinander trennt. Dieses Design ermöglicht extreme Flexibilität auf dem Application Layer, während die Robustheit und Sicherheit eines minimalen Base Layers erhalten bleibt.

---

## Der Consensus Layer: Ein minimalistischer Ordnungsdienst

Die LEA Base Chain ist kein Weltcomputer; sie ist eine hochoptimierte, zustandslose Ordnungsmaschine. Ihr einziger Zweck ist es, Transaktions-Envelopes aus dem Netzwerk zu empfangen, eine kanonische und manipulationssichere Reihenfolge festzulegen und sie an die Ausführungsschicht weiterzuleiten.

### Hauptverantwortlichkeiten:
- **Transaktionsordnung:** Nutzt einen von Proof of History (PoH) inspirierten Mechanismus, um eingehende Transaktionen effizient und verifizierbar mit Zeitstempeln zu versehen und zu sequenzieren, was eine globale Referenzuhr bereitstellt.
- **Datenpersistenz:** Garantiert, dass alle eingereichten Transaktions-Envelopes dauerhaft aufgezeichnet und verfügbar gemacht werden.
- **Dispatching:** Liest die ersten 32 Bytes jedes Transaktions-Envelopes, um die `DecoderID` zu identifizieren, und leitet den verbleibenden `Payload` zur Verarbeitung an den spezifischen on-chain Vertrag weiter.

### Was der Consensus Layer NICHT tut:
- **Keine State-Interpretation:** Das Basisprotokoll hat keine Kenntnis von Kontoständen, Smart-Contract-State oder Token-Besitz. Der gesamte State wird innerhalb der Ausführungsschicht verwaltet.
- **Keine Signaturverifizierung:** Der Consensus Layer validiert keine Signaturen. Er behandelt den Transaktions-Envelope als einen opaken Bytestream, der vom designierten Decoder interpretiert wird.
- **Keine intrinsische Logik:** Er enthält keine Logik für Überweisungen, Gebühren oder andere anwendungsspezifische Funktionen.

Dieses minimalistische Design macht das Basisprotokoll unglaublich einfach, widerstandsfähig und unwahrscheinlich, dass es umstrittene Upgrades erfordert.

---

## Der Execution Layer: Programmierbare Decoders

Sämtliche Logik und Intelligenz im LEA-Ökosystem befinden sich im Execution Layer, der aus WebAssembly (WASM) Smart Contracts besteht. Die zentrale Komponente dieser Schicht ist der **Decoder**.

Ein **Decoder** ist eine spezielle Art von Smart Contract (gekennzeichnet mit dem `ACCOUNT_FLAG_DECODER`), der für die Interpretation des `Payloads` einer Transaktion verantwortlich ist. Wenn eine Transaktion an das Netzwerk übermittelt wird, ruft der Consensus Layer den Vertrag unter der angegebenen `DecoderID` auf und löst dessen Ausführung aus.

### Verantwortlichkeiten eines Decoders:
Ein Decoder ist ein vollständiger, in sich geschlossener Transaktionsprozessor. Zu seinen Aufgaben gehören:
1.  **Parsen des Payloads:** Definition und Deserialisierung der Bytestruktur des Transaktions-`Payloads`.
2.  **Signaturverifizierung:** Implementierung der Logik zur Überprüfung der Transaktionssignaturen. Hier glänzt die kryptografische Agilität von LEA, da ein Decoder so programmiert werden kann, dass er Ed25519, SPHINCS+ oder jedes andere gewünschte Signaturschema validiert.
3.  **Replay Protection:** Durchsetzung von Regeln, um zu verhindern, dass dieselbe Transaktion zweimal ausgeführt wird. Während das Basisprotokoll über `prev_tx_hash` eine historische Kette bereitstellt, bestimmt der Decoder, wie diese (oder eine interne Nonce) zur Gewährleistung der Gültigkeit verwendet wird.
4.  **Gebührenlogik:** Definition und Verarbeitung von Transaktionsgebühren. Ein Decoder kann Logik implementieren, um Gebühren vom Absender abzuziehen, Zahlungen von einem Drittanbieter-Sponsor (Meta-Transaktionen) zu akzeptieren oder sogar ganz auf Gebühren zu verzichten.
5.  **State Transition:** Aufruf anderer Smart Contracts und Orchestrierung der State-Änderungen, die die Geschäftslogik der Transaktion ausmachen.

---

## PODs: Programmable Object Domains

Während ein Decoder den Einstiegspunkt für eine Transaktion darstellt, repräsentiert ein **Programmable Object Domain (POD)** eine vollständige, zweckgebundene Anwendung oder ein Ökosystem. Ein POD ist eine konzeptionelle Gruppierung von einem oder mehreren Decodern und den zugehörigen Smart Contracts, die alle zusammenarbeiten, um eine bestimmte Funktion zu erfüllen.

PODs sind kein vom Protokoll erzwungenes Konstrukt, sondern ein mächtiges Architekturmuster, das sich aus dem Design von LEA ergibt. Sie ermöglichen es Entwicklern, in sich geschlossene Welten auf der gemeinsamen Konsensschicht von LEA aufzubauen.

### Eigenschaften eines PODs:
- **Geteilte Logik:** Oft zentriert um einen einzigen, kanonischen Decoder oder einen Satz standardisierter Verträge.
- **Domänenspezifische Regeln:** Ein POD für Real-World Assets (RWA) könnte KYC/AML-Prüfungen in seinem Decoder durchsetzen, während ein Privacy-POD Zero-Knowledge Proofs verwenden würde.
- **Souveräne Tokenomics:** Ein POD kann den nativen $LEA-Token, seinen eigenen benutzerdefinierten Token (z. B. `$RWA_STABLE`) verwenden oder ganz ohne Token auskommen.
- **Definierte Schnittstellen:** PODs können vollständig isoliert sein oder öffentliche Funktionen in ihren Smart Contracts bereitstellen, um eine erlaubnisbasierte Interoperabilität mit anderen PODs zu ermöglichen.

Diese modulare Struktur ermöglicht parallele Innovation. Die Entwicklung eines komplexen Finanz-PODs stört oder hängt nicht von der Entwicklung eines dezentralen Social-Media-PODs ab, dennoch können beide von den gleichen zugrunde liegenden Sicherheits- und Datenordnungsgarantien der LEA-Chain profitieren.

---

## Inter-POD-Komponierbarkeit: Der Decoder Handshake

Um eine sichere, synchrone Kommunikation zwischen verschiedenen PODs zu ermöglichen, unterstützt LEA einen **Decoder Handshake**-Mechanismus. Dies ermöglicht eine atomare Komponierbarkeit, bei der eine Transaktion Aufrufe über mehrere PODs ausführen kann, die entweder alle gemeinsam erfolgreich sind oder alle gemeinsam fehlschlagen.

Der Prozess ist wie folgt:
1.  Ein Vertrag in `POD-A` möchte einen Vertrag in `POD-B` aufrufen.
2.  Er verwendet eine spezielle Host-Funktion, `cross_pod_call()`, die einen "Handshake" auslöst.
3.  Die LEA VM führt einen Read-Only-Call auf eine standardisierte Funktion im Decoder von `POD-B` aus und fragt effektiv: "Erlauben Sie einen Aufruf von `POD-A`?"
4.  Der Decoder von `POD-B` kann dann seine eigene Richtlinie durchsetzen, zum Beispiel durch Überprüfung gegen eine On-Chain-Whitelist von vertrauenswürdigen PODs.
5.  Wenn der Handshake genehmigt wird, fährt die VM mit der Ausführung des Aufrufs fort. Wenn er verweigert wird, schlägt die Transaktion fehl, bevor ein State geändert wird.

Dieses Modell bewahrt die Souveränität und Sicherheit jedes PODs, indem es ihnen erlaubt, ihre eigenen Interaktionsrichtlinien zu definieren, während es gleichzeitig die leistungsstarke, atomare Komponierbarkeit ermöglicht, die für ein reichhaltiges DeFi- und Web3-Ökosystem unerlässlich ist.
