# Gebühren & Governance

Das LEA-Protokoll verfügt über ein zweischichtiges Wirtschafts- und Governance-Modell, das seine Architektur widerspiegelt. Der native **$LEA-Coin** sichert den Basis-Consensus-Layer und regelt protokollweite Standards, während jedes **Programmable Object Domain (POD)** frei ist, seine eigene souveräne Tokenomics und Governance zu implementieren, was ein reichhaltiges und vielfältiges Ökosystem schafft.

---

## Der $LEA-Coin: Sicherung der Grundlage

Der $LEA-Coin ist das native Asset des Consensus Layers, dessen Hauptnutzen auf die Sicherung des Netzwerks und die Steuerung seiner Kernfunktionen ausgerichtet ist.

### Nutzen von $LEA:
- **Staking für den Konsens:** Validators müssen $LEA staken, um am Prozess der Blockproduktion und Transaktionsordnung teilzunehmen. Bösartiges Verhalten (z. B. Double-Signing) führt zum Slashing ihrer gestakten Coins, was die wirtschaftliche Sicherheit des Netzwerks gewährleistet.
- **Protokollebenen-Governance:** Inhaber von $LEA-Coins haben das Recht, **LEA Improvement Proposals (LIPs)** vorzuschlagen und darüber abzustimmen. Dies umfasst Entscheidungen über Kernprotokoll-Upgrades, Änderungen an den Anforderungen für das Validator-Set oder Modifikationen an der Basis-Gebührenstruktur.
- **Anreize für das Prover-Netzwerk:** Das dezentrale Netzwerk von Provers, das für die Erzeugung von zk-STARKs für ruhende Verträge verantwortlich ist, wird in $LEA-Coins belohnt. Ein Teil der Netzwerk-Transaktionsgebühren oder der Protokollinflation kann zur Finanzierung dieses kritischen Ökosystemdienstes verwendet werden.
- **Gebühren für Kerninfrastruktur:** Während PODs ihre eigenen Gebühren-Token definieren können, kann die Interaktion mit bestimmten Kernprotokollfunktionen, wie die Registrierung eines neuen Decoders in einem kanonischen öffentlichen Register, eine in $LEA zu zahlende Gebühr erfordern.

---

## Wirtschaftliche Autonomie auf POD-Ebene

Die Architektur von LEA gewährt jedem POD vollständige wirtschaftliche Souveränität. Diese Flexibilität ermöglicht es Entwicklern, nachhaltige Modelle zu entwerfen, die auf ihre spezifische Anwendung zugeschnitten sind, anstatt durch ein globales, universelles Modell eingeschränkt zu werden.

### Das Value-Capture-Gebührenmodell: Anreize für Entwickler und Nutzer in Einklang bringen
LEA führt ein neuartiges Gebührenmodell ein, das Entwickler für die Schaffung von Mehrwert belohnen soll, ohne Fehlanreize zu schaffen. Anstelle eines Gebührenbeteiligungsmodells auf Basis von Gas (was ineffizienten Code fördern würde), trennt LEA die Netzwerkgebühr von einer optionalen Entwicklergebühr.

1.  **Netzwerk-Gas-Gebühr:** Dies ist die Standardgebühr, die an Validators für die Verarbeitung einer Transaktion gezahlt wird. Sie basiert auf den verbrauchten Rechenressourcen (Gas). 100 % dieser Gebühr gehen an den Validator, um das Netzwerk zu sichern.
2.  **Developer-Value-Capture-Gebühr:** Ein Entwickler kann sich dafür entscheiden, eine kleine, feste Gebühr zu seinen Smart Contracts hinzuzufügen. Diese Gebühr wird vom Entwickler definiert (z. B. 0,01 USDC) und bei Ausführung direkt an seine Adresse gezahlt.

Dieses Modell bringt die Anreize in Einklang:
-   **Entwickler** werden für die Erstellung beliebter, hochfunktionaler Verträge belohnt, die oft ausgeführt werden.
-   **Benutzer** zahlen eine vorhersagbare, transparente Gebühr und profitieren davon, dass Entwickler Anreize haben, effizienten, Low-Gas-Code zu schreiben.
-   **Validators** werden fair für die von ihnen bereitgestellten Ressourcen entschädigt.

Das LEA SDK und das Wallet-Tooling werden für Transparenz sorgen, indem sie den Benutzern sowohl die Netzwerkgebühr als auch die Entwicklergebühr explizit anzeigen, bevor eine Transaktion signiert wird.

### Programmierbare Wirtschaftspolitik:
Über die Value-Capture-Gebühr hinaus wird die Logik für Transaktionsgebühren vollständig innerhalb des Decoder-Vertrags eines PODs definiert, was eine breite Palette innovativer Modelle ermöglicht:
- **Wahl des Gebühren-Tokens:** Ein DEX-POD könnte verlangen, dass Netzwerkgebühren in seinem eigenen Governance-Token bezahlt werden, während ein RWA-POD vorschreiben könnte, dass Gebühren in einem Stablecoin wie USDC für vorhersagbare Betriebskosten bezahlt werden.
- **Gebühren-Sponsoring (Meta-Transaktionen):** Ein Decoder kann so programmiert werden, dass ein Dritter die Transaktionsgebühr im Namen des Benutzers bezahlen kann. Dies ist ein entscheidender Vorteil für das Onboarding von Benutzern, da dApps die ersten Transaktionen eines neuen Benutzers sponsern können, was eine "gasless" Erfahrung schafft.
- **Souveräne Token-Ökosysteme:** Jeder POD kann als seine eigene Mikroökonomie fungieren, mit Token, die verschiedenen Zwecken wie Governance, Zugang zu Dienstprogrammen oder Profit-Sharing dienen, alles unabhängig vom $LEA-Coin.

---

## Zweischichtige Governance

Die Governance auf LEA funktioniert auf zwei verschiedenen Ebenen:

1.  **Protokoll-Governance (LIPs):**
    - **Umfang:** Betrifft das gesamte LEA-Netzwerk. Dies umfasst die Consensus-Engine, die WASM-Runtime, das Staking-Modul und andere Kernkomponenten.
    - **Teilnehmer:** Inhaber von $LEA-Coins und Validators.
    - **Mechanismus:** Ein formeller On-Chain-Abstimmungsprozess auf Basis von gestaktem $LEA zur Genehmigung oder Ablehnung von LIPs.

2.  **POD-Governance (Anwendungsebene):**
    - **Umfang:** Betrifft nur einen einzigen POD. Dies umfasst das Upgrade des Decoders des PODs, die Änderung seines Gebührenmodells oder die Verwaltung seiner Treasury.
    - **Teilnehmer:** Definiert durch die eigenen Regeln des PODs. Teilnehmer könnten Inhaber des nativen Tokens des PODs, Mitglieder einer Multi-Sig oder sogar auf einer Whitelist geführte Adressen sein.
    - **Mechanismus:** Vollständig innerhalb der Smart Contracts des PODs implementiert. Ein POD könnte eine einfache Multi-Sig, eine komplexe DAO mit token-gewichteter Abstimmung verwenden oder gar keine Governance haben (d. h. ein unveränderlicher Vertrag sein).

Diese duale Struktur stellt sicher, dass das Kernprotokoll unter der Obhut der $LEA-Stakeholder stabil und sicher bleibt, während einzelne Anwendungen (PODs) die Autonomie behalten, nach eigenem Ermessen zu innovieren und sich selbst zu verwalten.
