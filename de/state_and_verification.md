# Zustandsmodell: On-Chain-State & verifizierbare Kompression

LEA führt einen neuartigen Ansatz zur Verwaltung des State-Wachstums ein, eine der größten Herausforderungen für skalierbare Layer-1-Blockchains. Die Architektur ist darauf ausgelegt, die Rechenlast für Nodes auf ein Minimum zu beschränken und gleichzeitig die volle Datenverfügbarkeit und die kryptografische Verifizierbarkeit der gesamten Chain-Historie zu gewährleisten. Dies wird durch die Kombination von On-Chain-State-Speicherung mit einem leistungsstarken Mechanismus zur **verifizierbaren State-Kompression** mittels zk-STARKs erreicht.

---

## On-Chain-State: Die Quelle der Wahrheit

Im Gegensatz zu einigen State-Pruning-Modellen schreibt das Basisprotokoll von LEA vor, dass **der gesamte endgültige Vertrags-State on-chain gespeichert wird**. Jeder Smart Contract, einschließlich Benutzerkonten und des nativen Coin-Vertrags, hat seinen eigenen dedizierten Speicherplatz.

Der State eines Vertrags wird durch einen `state_hash` (typischerweise eine Merkle Root) in seinen on-chain Kontodaten repräsentiert. Dieser Hash verpflichtet sich auf den gesamten internen State des Vertrags (z. B. Guthaben, Eigentum, Metadaten).

### Lösung des Datenverfügbarkeitsproblems
Indem der vollständige State on-chain gehalten wird, löst LEA das Problem der Datenverfügbarkeit inhärent.
- **Zugänglichkeit:** Jeder Benutzer oder Node kann die Blockchain abfragen, um den vollständigen, aktuellen State eines beliebigen Smart Contracts abzurufen. Es besteht keine Abhängigkeit von Off-Chain-Akteuren, um diese Daten bereitzustellen.
- **Trustless Rekonstruktion:** Ein neuer Node kann jederzeit die Chain synchronisieren und den aktuellen World State unabhängig rekonstruieren, indem er alle historischen Transaktionen verarbeitet.

Die Anforderung, dass neue Nodes jede Transaktion seit Genesis erneut ausführen müssen, ist jedoch ein großer Skalierbarkeits-Engpass. Dies ist das Problem, das das verifizierbare Kompressionsmodell von LEA löst.

---

## Verifizierbare State-Kompression mittels zk-STARKs

Die Kerninnovation im State-Modell von LEA ist nicht die Löschung von Daten, sondern die **Kompression ihres Verifizierungsprozesses**.

Wenn ein Vertrag für einen definierten Zeitraum (z. B. N Blöcke) inaktiv war, wird er für die "Dormancy-Kompression" qualifiziert. Dieser Prozess entfernt den State des Vertrags nicht von der Chain. Stattdessen erzeugt er einen **zk-STARK-Proof**, der als kryptografische Quittung dient und die Gültigkeit der gesamten Historie des Vertrags bis zu seinem aktuellen Zustand beweist.

### Der Kompressionsprozess:
1.  **Prover-Netzwerk:** Ein dezentrales Netzwerk von Provers identifiziert einen ruhenden Vertrag.
2.  **Proof-Erzeugung:** Ein Prover ruft die Transaktionshistorie des Vertrags ab (verknüpft durch die `prev_tx_hash`-Kette). Er erzeugt einen zk-STARK, der Folgendes beweist:
    - **Historische Integrität:** Jede Transaktion in der Signaturkette ist gültig und korrekt signiert.
    - **Korrektheit der Ausführung:** Der endgültige `state_hash` des Vertrags ist das korrekte Ergebnis der Anwendung dieser gültigen Transaktionshistorie auf seinen Anfangszustand.
3.  **On-Chain-Attestierung:** Der Prover übermittelt diesen STARK-Proof an die LEA-Chain. Der Proof wird den Kontodaten des ruhenden Vertrags beigefügt.
4.  **Log-Pruning (Optional):** Sobald der Proof on-chain ist, können Full Nodes die detaillierten historischen Transaktionsprotokolle für diesen spezifischen Vertrag löschen, da ihre Gültigkeit nun dauerhaft und kompakt durch den STARK belegt ist. Die endgültigen Zustandsdaten bleiben erhalten.

### Vorteile der verifizierbaren Kompression:
- **Ultraschnelle Node-Synchronisierung:** Ein neuer Node, der dem Netzwerk beitritt, muss nicht die Millionen von Transaktionen eines ruhenden Vertrags erneut ausführen. Er muss nur zwei Schritte ausführen:
    1.  Laden Sie die endgültigen Zustandsdaten des Vertrags herunter (die on-chain gespeichert sind).
    2.  Verifizieren Sie den einzelnen, kompakten zk-STARK-Proof, der damit verbunden ist.
    Dies reduziert die Zeit zur Synchronisierung und Validierung der Chain von Tagen oder Wochen auf Minuten, unabhängig vom Alter des Netzwerks.
- **Rechnerische Entlastung für Validatoren:** Validatoren müssen keine CPU-Zyklen verschwenden, um die Historien inaktiver Verträge erneut zu validieren. Sie können sich auf die kryptografische Sicherheit der STARKs verlassen.
- **Permanente Datenverfügbarkeit:** Da der endgültige State niemals gelöscht wird, vermeidet das System jedes Risiko, dass Daten zurückgehalten werden, und stellt sicher, dass jeder Vertrag jederzeit reaktiviert und verwendet werden kann.

---

## Sicherung des Prover-Netzwerks

Die Liveness und Integrität des State-Kompressionssystems hängen von einem robusten, dezentralen Netzwerk von Provers ab. LEA sichert dieses Netzwerk durch ein sorgfältig konzipiertes Wirtschaftsmodell, das Anreize für die Teilnahme schafft und bösartiges Verhalten bestraft.

### Das ökonomische Modell der Prover:
1.  **Der Prover-Subventions-Pool:** Ein dedizierter On-Chain-Fonds, der durch einen Teil der Netzwerk-Transaktionsgebühren und der Protokollinflation gespeist wird, stellt sicher, dass immer eine Belohnung für die Erzeugung von Proofs zur Verfügung steht. Dies garantiert die Liveness auch in Zeiten geringer Netzwerkaktivität.
2.  **Staking und Slashing:** Um teilzunehmen, müssen Provers einen erheblichen Betrag an $LEA staken. Dieser Stake dient als Sicherheit, die bei der Einreichung eines mathematisch ungültigen STARKs oder bei Nichterfüllung der Uptime-Anforderungen per Slashing eingezogen wird. Dies bietet eine starke wirtschaftliche Garantie für gutes Verhalten.
3.  **Die Proof-Auktion:** Ein On-Chain-Auktionsmechanismus stellt sicher, dass Proofs effizient erzeugt werden. Provers bieten auf das Recht, eine Charge ruhender Verträge zu beweisen, wobei das Protokoll das niedrigste gültige Gebot auswählt. Dies schafft einen Wettbewerbsmarkt, der die Kosten senkt und mit Hardware-Verbesserungen skaliert.

Dieses System stellt sicher, dass die kritische Funktion der State-Kompression wirtschaftlich nachhaltig, sicher und dauerhaft verfügbar ist.

---

## State Rent und Hibernation: Gewährleistung langfristiger Nachhaltigkeit

Um State Bloat zu bekämpfen und sicherzustellen, dass die Kosten für die permanente Speicherung berücksichtigt werden, implementiert LEA ein **vorausbezahltes State-Rent-Modell**. Dieses System ist so konzipiert, dass es transparent und benutzergesteuert ist und ein automatisches Abziehen von Geldern aus den Benutzer-Wallets verhindert.

### Der ökonomische Zyklus der State Rent:
Der Rent-Mechanismus ist ein vollständiger ökonomischer Zyklus, der effizient, transparent und fair gestaltet ist.

1.  **Vorauszahlung & Der State Rent Fund:** Wenn ein Benutzer Miete zahlt (entweder bei der Bereitstellung oder durch Aufladen), wird sein $LEA in einen speziellen, vom Protokoll verwalteten **State Rent Fund** eingezahlt. Dieser zentrale Pool hält alle vorausbezahlten Mieten aus dem gesamten Netzwerk.
2.  **Der `rent_paid_until`-Zeitstempel:** Aus Sicht des Benutzers ist nur der `rent_paid_until`-Zeitstempel auf seinem Vertrag von Bedeutung. Dieser Zeitstempel wird basierend auf dem Zahlungsbetrag und der Größe des Vertrags-States berechnet. Solange dieser Zeitstempel in der Zukunft liegt, gilt der Vertrag als bezahlt.
3.  **Auszahlungen an Validatoren:** Die Begünstigten des State Rent Fund sind die **Validatoren**, die für den Dienst der Speicherung der Chain-Daten entschädigt werden. Am Ende jeder Epoche berechnet das Protokoll die gesamte aufgelaufene Miete für alle im Netzwerk gespeicherten States während dieses Zeitraums. Es verteilt dann den entsprechenden Betrag aus dem State Rent Fund an die aktiven Validatoren, proportional zu ihrem Stake. Dies ist eine einzige, effiziente Massentransaktion.
4.  **Hibernation:** Wenn der `rent_paid_until`-Zeitstempel eines Vertrags abläuft, tritt er in eine Gnadenfrist ein. Wenn die Miete bis zum Ende dieser Frist nicht aufgeladen wird, hiberniert das Protokoll den Vertrag und verschiebt seinen State in ein Langzeitarchiv, um aktiven State-Speicherplatz freizugeben.

### Closure Rebate:
Um eine gute "Garbage Collection" zu fördern, bietet LEA einen **Closure Rebate**. Wenn ein Entwickler einen Vertrag explizit schließt (und seinen State freigibt), erstattet das Protokoll einen erheblichen Teil (z. B. 30 %) der gesamten Miete, die dieser Vertrag jemals bezahlt hat. Dies belohnt Entwickler dafür, die Chain sauber zu halten.

---

## Reaktivierung eines hibernierten Vertrags

Wenn eine neue Transaktion an einen hibernierten Vertrag gesendet wird, verlangt das Protokoll vom Absender, die ausstehende Mietschuld zu bezahlen. Sobald dies geschehen ist, wird der State automatisch aus dem Archiv wiederhergestellt, der Vertrag wird wieder voll aktiv, und die Transaktion wird fortgesetzt. Dies stellt sicher, dass kein Vertrag jemals dauerhaft verloren geht, während ein starker wirtschaftlicher Anreiz erhalten bleibt, den aktiven State-Satz sauber und effizient zu halten.

Dieses Modell bietet das Beste aus beiden Welten: die immensen Speicher- und Rechenersparnisse eines zk-Rollups, kombiniert mit den Datenverfügbarkeits- und Sicherheitsgarantien einer traditionellen Layer-1-Blockchain.
