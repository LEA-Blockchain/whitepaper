# Sicherheitsschicht: PQC, Account Abstraction und Gas

Sicherheit im LEA-Ökosystem ist ein vielschichtiges Konzept, das durch eine Kombination aus kryptografischer Agilität, einem flexiblen Account-Modell und strenger Ressourcenmessung durchgesetzt wird. Indem die Validierungslogik an den Execution Layer delegiert wird, ermöglicht LEA Entwicklern, Anwendungen mit maßgeschneiderten, zukunftssicheren Sicherheitsgarantien zu erstellen.

---

## Post-Quantum Cryptography (PQC) und der Basisstandard

Das Aufkommen des Quantencomputings stellt eine langfristige existenzielle Bedrohung für klassische kryptografische Schemata dar. LEA ist durch **kryptografische Agilität** so konzipiert, dass es quantenresistent ist, wobei jeder POD sein eigenes Signaturschema definieren kann.

Um einen sicheren Standard zu bieten, implementieren das Standard-LEA-Account-Modell und der Base Decoder ein **Dual-Signature-Schema**:
- **Ed25519:** Ein ausgereifter, hocheffizienter klassischer Algorithmus, der für häufige Transaktionen mit geringem Wert verwendet wird.
- **Falcon-512:** Ein NIST-standardisierter PQC-Algorithmus, der für Operationen mit hohem Wert wie Schlüsselrotation oder große Wertübertragungen verwendet wird. Falcon wurde aufgrund seiner Effizienz und relativ kleinen Signaturgröße ausgewählt, um die On-Chain-Kosten zu minimieren.

Die On-Chain-Logik innerhalb des Account-Vertrags definiert, welcher Schlüssel für welche Aktion erforderlich ist, und stellt sicher, dass eine Kompromittierung des "heißen" Ed25519-Schlüssels nicht die Kernvermögenswerte eines Kontos gefährden kann. Wenn neue PQC-Standards aufkommen, können sie durch die Bereitstellung neuer Decoders übernommen werden, **ohne dass ein netzwerkweiter Hard Fork erforderlich ist**.

---

## Native Account Abstraction & Schlüsselverwaltung

LEA implementiert Account Abstraction auf der grundlegendsten Ebene, indem die permanente Identität eines Benutzers (seine Adresse) von seinen Signaturberechtigungen (seinen Schlüsseln) entkoppelt wird.

- **Permanente Adresse:** Die Adresse eines Kontos wird einmalig generiert und ist permanent.
- **On-Chain-Schlüsselregister:** Die autoritativen öffentlichen Schlüssel (z. B. ein Ed25519-Schlüssel und ein Falcon-512-Schlüssel) werden im On-Chain-State des Kontos gespeichert, nicht in der Adresse selbst.

Diese Trennung ermöglicht leistungsstarke Schlüsselverwaltungsfunktionen:

- **Schlüsselrotation:** Ein Benutzer kann eine Transaktion mit seinem Hochsicherheitsschlüssel autorisieren, um die on-chain gespeicherten öffentlichen Schlüssel zu ersetzen. Dies ist entscheidend, um das Risiko eines kompromittierten Schlüssels zu mindern.
- **Social Recovery & Multi-Sig:** Die Logik zur Aktualisierung von Schlüsseln kann beliebig komplex sein und ermöglicht 3-von-5 Social Recovery, die Integration von Hardware-Sicherheitsmodulen oder unternehmensinterne Multi-Sig-Richtlinien.

---

## Der Account Escape Hatch: Gewährleistung des Benutzerzugriffs

Ein kritisches Sicherheitsversprechen von LEA ist, dass Benutzer **niemals aus ihren Konten ausgesperrt werden können**. Ein POD könnte böswillig oder versehentlich eine Situation schaffen, in der ein Benutzer den erforderlichen Gebühren-Token nicht bezahlen kann, um auf seine Vermögenswerte zuzugreifen. Der Account Escape Hatch verhindert dies.

Es ist eine Garantie auf Protokollebene, dass bestimmte kritische Funktionen eines Kontos **immer mit dem nativen $LEA-Token bezahlt werden können**, unabhängig von den Regeln des PODs.

- **Standardisierte Wiederherstellungsschnittstelle:** Alle Standard-Account-Verträge müssen eine `IAccountRecovery`-Schnittstelle implementieren. Dazu gehören Funktionen wie `recover_funds()` und `rotate_pqc_key()`.
- **$LEA für Gas:** Wenn ein Benutzer eine Funktion auf dieser Schnittstelle aufruft, erlaubt das LEA-Protokoll, dass die Gas-Gebühr der Transaktion in $LEA bezahlt wird, wodurch die benutzerdefinierte Gebührenlogik des PODs umgangen wird.

Dieser Mechanismus gibt den Benutzern eine permanente, souveräne Hintertür zu ihren eigenen Konten, die es ihnen ermöglicht, Gelder zu retten oder Schlüssel zu rotieren, ohne auf den guten Willen oder die Stabilität eines einzelnen PODs angewiesen zu sein. Er bietet die Vorteile eines flexiblen Gebührensystems, ohne die letztendliche Kontrolle des Benutzers über seine Vermögenswerte zu beeinträchtigen.

---

## Mehrdimensionales Gas-Modell und DoS-Prävention

Um die Netzwerkstabilität zu gewährleisten und Denial-of-Service (DoS)-Angriffe zu verhindern, verwendet LEA ein mehrdimensionales Gas-Modell, das von Transaktionen verlangt, für jede verbrauchte Ressource zu bezahlen. Die gesamte Netzwerkgebühr ist die Summe aus drei Teilen:

1.  **Inclusion Fee:** Eine kleine, pauschale Gebühr basierend auf der Bytegröße der Transaktion. Diese bezahlt die Netzwerkbandbreite und den Speicher, die zur Verbreitung der Transaktion erforderlich sind.
2.  **Invocation Fee:** Eine feste Gebühr für das Laden des WASM-Bytecodes eines Decoders in die VM. Diese Gebühr kann proportional zur Größe des WASM-Moduls sein, was es wirtschaftlich unrentabel macht, das Netzwerk mit Aufrufen an große, komplexe Decoder zu spammen.
3.  **Execution Fee:** Das traditionelle `gasUsed * gasPrice`-Modell, das für die Rechenschritte während der Ausführung bezahlt.

Wenn die Ausführung einer Transaktion ihr angegebenes `gasLimit` überschreitet, wird sie sofort angehalten, und alle Zustandsänderungen werden rückgängig gemacht. Die Inclusion- und Invocation-Gebühren werden jedoch trotzdem an den Block-Produzenten gezahlt.

### Der Auszahlungsmechanismus: Der Block Reward Pool
Gas-Gebühren für die Berechnung werden direkt an den Validator gezahlt, der den Block produziert. Um sicherzustellen, dass dieser Prozess atomar und robust ist, verwendet das Protokoll ein temporäres Haltekonto für jeden Block.

1.  **Sammlung:** Während Transaktionen innerhalb eines Blocks ausgeführt werden, werden ihre entsprechenden Gas-Gebühren in einem temporären, im Speicher befindlichen **"Block Reward Pool"** gesammelt.
2.  **Finalisierung:** Nach der erfolgreichen Validierung und Finalisierung des Blocks wird der gesamte Saldo dieses Pools in einer einzigen, abschließenden Operation an die Adresse des Block-Produzenten überwiesen.

Dieser Alles-oder-Nichts-Ansatz garantiert, dass Validatoren für gültige Arbeit entschädigt werden, vereinfacht die Buchhaltung des Protokolls und bietet zukünftige Flexibilität für die Implementierung fortschrittlicherer Gebührenstrukturen, wie z. B. Fee Burning.

---

## Replay Protection durch Signature Chaining

Wie im Kapitel zum Transaction Lifecycle beschrieben, ist der primäre Replay-Protection-Mechanismus von LEA das Feld `prev_tx_hash`. Indem jede Transaktion auf den Hash der letzten gültigen Transaktion für dieses Konto verweisen muss, stellt das Protokoll eine strikte, verifizierbare Ordnung sicher. Eine von einem böswilligen Akteur erfasste Transaktion kann nicht wiederholt werden, da ihr `prev_tx_hash` nicht mehr mit dem auf dem Konto des Absenders gespeicherten `latest_tx_hash` übereinstimmt, was dazu führt, dass der Decoder sie ablehnt.
