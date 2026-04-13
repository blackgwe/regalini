[toc]

---

**Version:** 1.0 **Status:** Entwurf 

**Weitere Dokumente: **

*  [vision.md](vision.md)  // [anforderungen.md](anforderungen.md)  //  [umsetzung.md](umsetzung.md) 
*  [architektur.md](architektur.md) 

---

## 1. Roadmap Regalini (24 Monate)

### Phase 1: Grundkonzept und Pilotierung (Monat 1–3)

**Ziele:**

- Rechtliche Struktur klären:
  - Gründung als Verein (einfache Statuten, demokratische Kontrolle).
  - Juristische Beratung einholen (z. B. über [bibliostschweiz](https://www.bibinfo.ch/) oder https://bibliosuisse.ch/).
- Pilotprojekt Wila starten:
  - Lokale Ansprechpartner:innen identifizieren (z. B. Gemeindebibliothek, Vereine).
  - Interesse wecken: Einfache Demo der Plattform (z. B. mit 10–20 Büchern).
  - Finanzierungsmodell testen:
    - Öffentliche Mittel (Gemeinde Wila).
    - Freiwilligenarbeit (z. B. 5–10 Personen für Organisation/IT).
    - Sponsoring (lokale Unternehmen).

---

### Phase 2: Technische Infrastruktur (Monat 4–12)

**Ziele:**

- AstroJS/PostgreSQL-System entwickeln:
  - Frontend: AstroJS + TypeScript (statisch, schnell, barrierefrei).
  - Backend: PostgreSQL (Datenbank für Bestände, Nutzer:innen, Ausleihen).
  - KI-Integration: Indizierung, Empfehlungsalgorithmen (z. B. „Island-Krimis“).
- Datenschutz finalisieren:
  - Kein Tracking (DSGVO-konform).
  - Anonymisierte Nutzerdaten (z. B. keine IP-Speicherung).
- Sicherheit:
  - Verschlüsselte S3-Buckets (Schlüsselverwaltung via AWS KMS).
  - Hosting bei Infomaniak (CH-Serverstandort).

---

### Phase 3: Netzwerk-Expansion (Monat 13–18)

**Ziele:**

- 3–5 weitere Standorte pilotieren:
  - Kriterien: Engagierte lokale Initiator:innen, Verfügbarkeit von Freiwilligen, Räumliche Infrastruktur (z. B. Gemeindezentren).
  - zunächst im Tösstal
- Freiwilligen-Netzwerk aufbauen:
  - Strukturierte Aufgabenliste (z. B. „IT-Support gesucht“, „Eventmanager:in“).
  - Schul-Kooperationen (z. B. Projekttage in Bibliotheken).

---

### Phase 4: Evaluation und Skalierung (Monat 19–24)

**Ziele:**

- Feedback auswerten, System optimieren:
  - Nutzerumfragen (z. B. „Was gefällt euch nicht?“).
  - Technische Optimierung (z. B. schnellere Suche, bessere KI-Empfehlungen).
- Skalierung auf kantonaler / nationale Ebene vorbereiten:
  - Kooperationen mit grossen Bibliotheken (z. B. Bibliotheken Winterthur).
  - Finanzierungsmodell für nationale Ebene testen 
- Version 2 planen:
  - Erweiterung auf andere Medien (Spiele, Werkzeuge, Elektrogeräte).
  - Neue Zielgruppen (z. B. Unternehmen, Schulen).

---

## 2. Finanzielle Nachhaltigkeit

### Kostenstruktur und Geschäftsmodelle

Das Projekt ist **nicht gewinnorientiert**, aber langfristig tragfähig. Die Kostenstruktur setzt sich aus folgenden Punkten zusammen:

**1. IT-Infrastruktur:**

- Hosting: SaaS bei Infomaniak (CHF 100–300/Monat).
- Datenbank: PostgreSQL (CHF 50–100/Monat).
- Verschlüsselung: S3-Buckets + Schlüsselverwaltung (CHF 20–50/Monat).
- Gesamt (IT): CHF 170–450/Monat (skalierbar mit Nutzer:innen-Zahlen).

**2. Betrieb & Organisation:**

- Freiwilligenkoordination: 0.5–1 FTE (z. B. via Gemeinde).
- Kommunikation: Social Media, lokale Medien (CHF 500–1.500/Jahr).
- Rechtliche Beratung: Einmalig CHF 2.000–5.000 (Verein/Genossenschaft).

**3. Platz & Infrastruktur:**

- Private Bibliotheken: Keine Mietkosten (Nutzen von Wohnräumen).
- Gemeinschaftsräume: Geteilte Kosten (z. B. mit Schulen).

---

### Finanzierungsquellen

Die Finanzierung des Projekts soll **mehrere Quellen** kombinieren, um Unabhängigkeit und langfristige Tragfähigkeit zu sichern:

| **Quelle**                | **Beschreibung**                                             | **Beispiel / Empfehlung**                                    |
| ------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Öffentliche Mittel**    | Kantone, Gemeinden, Bundesprogramme (z. B. für ländliche Entwicklung oder Kultur). | Förderung durch **Biblio2030**  oder **Kulturförderung Bund**. |
| **Freiwilligenarbeit**    | Ehrenamtliche in Bibliotheken, IT, Organisation, Eventmanagement. | Strukturierte **„Taskforce“-Liste** mit Aufgaben (z. B. „IT-Support gesucht“). |
| **Mitgliedschaften**      | Geringe Jahresbeiträge für Nutzer:innen (z. B. CHF 20–50).   | Modell wie **„Stiftung Bibliotheksnetzwerk Schweiz“**.       |
| **Sponsoring**            | Lokale Unternehmen, Stiftungen, Crowdfunding (z. B. über **„wemdo“**). | Kooperation mit **Migros Kulturprozent** oder **Raiffeisen Genossenschaft**. |
| **Öffentliche Förderung** | Wettbewerbe, Projektgelder (z. B. **„Ideenwettbewerb Ostschweiz“**). | Einreichung bei **„Förderfonds für ländliche Projekte“** (Kanton). |

---

### Langfristige Wirtschaftlichkeit

**Modell:**
Ein **Verein oder eine Genossenschaft** (z. B. „Bibliotheksnetzwerk Schweiz“) übernimmt die rechtliche und finanzielle Verantwortung.

**Vorteile:**

- Steuervorteile: Gemeinnützigkeit anerkannt.
- Einfache Verwaltung: Demokratische Kontrolle (Mitglieder entscheiden).
- Langfristige Sicherheit: Durch Kombination mehrerer Finanzierungsquellen.

**Risikomanagement:**

- Reservefonds: 10 % der jährlichen Einnahmen (z. B. CHF 5.000–10.000).
- Kooperationen: Mit bestehenden Bibliotheken oder Kulturinstitutionen.
- Transparenz: Jährlicher Finanzbericht, öffentliche Mittelverwendung.

---

