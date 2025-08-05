# Der Transaktionslebenszyklus: Signature Chaining

Transaktionen in LEA sind so konzipiert, dass sie inhärent sicher, geordnet und verifizierbar sind. Anstatt sich auf eine global verwaltete Nonce zu verlassen, implementiert LEA eine **pro-Account-Signaturkette**. Jede neue Transaktion verknüpft sich kryptografisch mit der vorherigen Transaktion desselben Kontos und schafft so eine ununterbrechbare, lineare Operationshistorie für jeden Vertrag und Benutzer.

---

## Struktur des Transaktions-Envelopes

Auf der grundlegendsten Ebene ist jede an das LEA-Netzwerk übermittelte Transaktion ein "Envelope" mit zwei Komponenten:

| Feld | Größe (Bytes) | Beschreibung |
|---|---|---|
| `DecoderID` | 32 | Die 32-Byte-Adresse des On-Chain-Decoder-Vertrags, der für die Interpretation dieser Transaktion verantwortlich ist. |
| `Payload` | Variabel | Ein opaker Bytestream, der alle Daten enthält, die der Decoder zur Ausführung der Transaktion benötigt. |

Der Consensus Layer interagiert nur mit der `DecoderID`. Die Struktur und Bedeutung des `Payloads` werden vollständig durch die Logik des Decoders definiert.

---

## Das Signature-Chain-Modell

Bei einem Standard-Decoder, der das Signature-Chain-Modell implementiert, ist der `Payload` so strukturiert, dass er einen Verweis auf die vorherige Transaktion des Kontos enthält.

Ein typischer `Payload` könnte konzeptionell wie folgt aufgeschlüsselt werden:

```
Payload = {
  // Kernverkettung und Kontext
  "sender_address": "0x...",       // Die Adresse des Kontos, das die Transaktion initiiert.
  "prev_tx_hash": "0x...",         // Der Hash der vorherigen Transaktion des Absenders.

  // Ausführungsanweisungen
  "call_data": {
    "target_contract": "0x...",   // Der aufzurufende Vertrag.
    "method_signature": "0x...",  // Bezeichner für die auszuführende Funktion.
    "parameters": [...]           // Argumente für die Funktion.
  },

  // Autorisierung
  "signature": "0x..."             // Signatur über den Hash aller obigen Felder.
}
```

### Das `prev_tx_hash`-Feld
Dies ist der Eckpfeiler der Signaturkette.
- Bei der allerersten Transaktion eines Kontos wird dieses Feld auf eine bekannte Konstante gesetzt (z. B. den Null-Hash).
- Bei jeder nachfolgenden Transaktion **muss** es den Hash der Transaktion enthalten, die den Zustand des Kontos zuletzt geändert hat.
- Der Decoder ist dafür verantwortlich, den aktuellen `latest_tx_hash` des Kontos aus seinem On-Chain-State abzurufen und zu überprüfen, ob er mit dem `prev_tx_hash` in der eingehenden Transaktion übereinstimmt.

Dieser Mechanismus bietet einen robusten, integrierten Replay Protection, ohne sich auf einen zentralisierten oder sequenziellen Nonce-Manager zu verlassen. Eine alte Transaktion kann nicht wiederholt werden, da ihr `prev_tx_hash` nicht mit dem aktuellen `latest_tx_hash` des Kontos übereinstimmt.

---

## Phasen des Transaktionslebenszyklus

1.  **Erstellung (Client-seitig):**
    - Das Wallet eines Benutzers konstruiert den Transaktions-`Payload`.
    - Es ruft den `latest_tx_hash` für das Benutzerkonto von einem Node ab.
    - Es setzt das `prev_tx_hash`-Feld auf diesen Wert.
    - Es hasht die kanonische Darstellung des `Payloads` (ohne das Signaturfeld).
    - Es signiert diesen Hash mit dem privaten Schlüssel des Benutzers.
    - Das Wallet stellt den endgültigen Envelope zusammen: die `DecoderID`, gefolgt vom signierten `Payload`.

2.  **Einreichung & Ordnung (Consensus Layer):**
    - Der Transaktions-Envelope wird an das Netzwerk gesendet.
    - Der LEA Consensus Layer empfängt den Envelope, weist ihm über Proof of History eine Sequenznummer und einen Zeitstempel zu und fügt ihn in einen Block ein. Hier findet keine Validierung des Payloads statt.

3.  **Dispatch (Konsens -> Ausführung):**
    - Die Consensus-Engine liest die 32-Byte-`DecoderID` aus dem Envelope.
    - Sie ruft die Funktion `execute` oder `decode` auf dem Smart Contract an dieser Adresse auf und übergibt den gesamten `Payload` als Argument.

4.  **Ausführung (Decoder-Vertrag):**
    - Der Decoder-Vertrag empfängt den `Payload`.
    - Er deserialisiert den `Payload` gemäß seinem eigenen definierten Format.
    - **Er führt alle kritischen Validierungsprüfungen durch:**
        - Überprüft, ob der `prev_tx_hash` mit dem im Konto des Absenders gespeicherten `latest_tx_hash` übereinstimmt.
        - Überprüft die `signature` anhand der Transaktionsdaten und des mit dem Konto des Absenders verknüpften öffentlichen Schlüssels.
        - Verarbeitet die Gebührenlogik.
    - Wenn alle Prüfungen erfolgreich sind, führt er die `call_data` aus, ruft den Zielvertrag auf und aktualisiert dessen Zustand.
    - Als letzten, atomaren Schritt aktualisiert er den Zustand des Absenderkontos und setzt dessen `latest_tx_hash` auf den Hash der aktuellen, gültigen Transaktion.

Dieser Lebenszyklus stellt sicher, dass, während das Basisprotokoll einfach bleibt, die vom Decoder durchgesetzte Ausführungslogik umfassend und sicher ist, wobei die Historie jedes Kontos eine kryptografisch verknüpfte Kette bildet.

---

## Empfangen des Ausführungsergebnisses

Nachdem eine Transaktion erfolgreich ausgeführt wurde, erhält der Client, der sie eingereicht hat, ein **`execution_result`**. Dies ist ein binärer Blob von Daten, der ephemere Off-Chain-Informationen enthält, die von den an der Transaktion beteiligten Smart Contracts zurückgegeben werden.

### Zweck und Eigenschaften:
-   **Ephemere Daten:** Das `execution_result` dient dazu, leichtgewichtige Daten wie Statuscodes, berechnete Werte (z. B. ein Handelspreis) oder neu erstellte Kontoadressen direkt an den Benutzer zurückzugeben, ohne den permanenten On-Chain-State aufzublähen.
-   **Nicht Teil des Konsenses:** Diese Daten sind explizit **nicht** Teil des Konsenszustands. Es handelt sich um Metadaten, die vom Node zurückgegeben werden. Das bedeutet, dass Clients sie als **beratend und nicht vertrauenswürdig** behandeln müssen. Alle kritischen Daten, die im Ergebnis zurückgegeben werden, sollten vor der Verwendung in nachfolgenden Transaktionen anhand des On-Chain-States erneut überprüft werden.
-   **Streng begrenzt:** Um Missbrauch zu verhindern, ist die Gesamtgröße des `execution_result` auf 64 KB begrenzt. Wenn eine Transaktion versucht, mehr Daten als diese zurückzugeben, wird die gesamte Transaktion abgelehnt.

Dieser Mechanismus bietet eine hocheffiziente Möglichkeit für Smart Contracts, Informationen an den Endbenutzer zurückzumelden und den Transaktionslebenszyklus abzuschließen.
