[toc]



# Anforderungsdokument: Regalini

**Version:** 1.0 **Status:** Entwurf **Pilotbibliothek:** Bibliothek Wila

**Weitere Dokumente**: [umsetzung.md](umsetzung.md)  // [architektur.md](architektur.md) 

------

## 1. Einleitung

### 1.1 Zweck des Dokuments

Dieses Dokument fasst die funktionalen und nicht-funktionalen Anforderungen an eine offene, datenschutzkonforme Plattform zur Vernetzung von öffentlichen, privaten und institutionellen Bibliotheksbeständen in der Schweiz zusammen. Es dient als Grundlage für Architektur, Datenmodell und Implementierung.

### 1.2 Vision

„Viele kleine Bibliotheken – ein grosses Ganzes." Die Plattform vernetzt über 2.000 Schweizer Bibliotheken sowie private Buchbestände zu einem dezentralen Netzwerk. Bücher werden geteilt statt weggeworfen, Nischenliteratur wird zugänglich, und Bibliotheken werden in ihrer Rolle als „dritte Orte" gestärkt.

### 1.3 Ziele

- **Nachhaltigkeit:** Wiederverwendung statt Aussortierung; Sharing-Konzepte gemäss Biblio2030 / SDG.
- **Zugänglichkeit:** Bibliotheksübergreifender Katalog mit lokaler Abholung – keine langen Transportwege.
- **Niederschwelligkeit:** Vertrauensprinzip statt Hardware-Lock-in; einfache Bedienung per App und QR-Code.
- **Datenschutz:** Schweizer Hosting, CH-DSG/DSGVO-konform, Open Source (AGPLv3).
- **Skalierbarkeit:** Mandantenfähig von einer Pilotbibliothek bis hin zu hunderten Standorten.

### 1.4 Nicht-Ziele (Abgrenzung)

- Keine digitale Ausleihe von E-Books/Hörbüchern (Onleihe-Ersatz).
- Keine Online-Bezahlung; Mahngebühren werden vor Ort beglichen (Bar/Twint).
- Keine Überwachung oder Hardware-basierte Zugangskontrolle.

------

## 2. Stakeholder & Rollen

| Rolle                 | Beschreibung                                                 |
| --------------------- | ------------------------------------------------------------ |
| **Admin**             | Systemverwaltung, Mandantenkonfiguration                     |
| **Bibliothekar:in**   | Bestands-, Ausleih- und Freiwilligenverwaltung einer Bibliothek |
| **Freiwillige:r**     | Übernimmt Aufgaben/Schichten in einer Bibliothek oder als "human help admin" (Nestandspflege, Schreiben von Blog-Artikeln, Erstellen neuer Themen-Bibliotheken, …) |
| **Nutzer:in**         | Sucht, reserviert, leiht und gibt Bücher zurück              |
| **Privatanbieter:in** | Stellt eigene Bücher zum Verleih, Tausch oder zur Weitergabe zur Verfügung |

------

## 3. Funktionale Anforderungen

### 3.1 Benutzer- & Zugriffsverwaltung

- Registrierung und Login via Passkeys (WebAuthn) und/oder E-Mail/Passwort.
- Rollen- und Rechteverwaltung gemäss Stakeholder-Liste.
- Datenschutzkonforme Profilverwaltung (CH-DSG/DSGVO), inklusive Auskunfts- und Löschrecht.
- Mandantenfähigkeit: Skalierbar von einer auf hunderte Bibliotheken.
- Mehrsprachige Oberfläche: DE / FR / IT / EN.
- Hinterlegung von Nutzerinteressen und Präferenzen als Basis für KI-Empfehlungen.

### 3.2 Bestandsverwaltung & Indizierung

- Einbuchen via ISBN/EAN-Scan mit automatischem Metadaten-Abruf (siehe §3.12).
- QR-Code-Generierung und -Druck für jedes Exemplar.
- Ausbuchen ausgeschiedener Bücher.
- Standort- und Regalverwaltung inkl. Umbuchung.
- Indizierung nach Ablageort (Bibliothek, Regal, Position), Themen/Tags, Sprachen, Zielgruppen.
- Statusverwaltung: verfügbar, ausgeliehen, reserviert, ausgeschieden.

### 3.3 Gemeinsamer Katalog

- Einheitliche Suche über alle vernetzten Bestände hinweg. 
- Filter nach Standort (Entfernung), Sprache, Thema, Verfügbarkeit
- Virtuelle Themenkataloge (z. B. „Umweltbibliothek", „Island-Krimis", Reiseliteratur "Schweden").
- Kartenansicht der Standorte.
- Sichtbarkeit privater Bestände mit individuellen Bedingungen.

### 3.4 Ausleihe & Vermittlung

- Erfassung der Ausleihvorgänge (wer, was, von wem, bis wann).

- Rückgabe und Verlängerung.

- Konfigurationsoptionen pro Bibliothek/Privatanbieter.

- Übersicht offener Ausleihen pro Nutzer:in und Anbieter.

- Ablaufdatum für Verleih-, Tausch- und Weitergabeangebote.

- Konfigurierbare Templates für Ausleihbedingungen: Frist, Ersatzregelung, Zielgruppe, Abholbedingungen.

  | Medienart                      | Leihfrist | Max. Medienanzahl | Gebühr                        | Verlängerung       | Ersatz bei Verlust                                |
  | ------------------------------ | --------- | ----------------- | ----------------------------- | ------------------ | ------------------------------------------------- |
  | **Buch**                       | 4 Wochen  | 20                | kostenlos                     | 1× um 2 Wochen     | Wiederbeschaffungswert oder gleichwertiger Ersatz |
  | **Kinderbuch / Bilderbuch**    | 4 Wochen  | 20                | kostenlos                     | 1× um 2 Wochen     | Wiederbeschaffungswert                            |
  | **Tonie / Hörfigur**           | 2 Wochen  | 4                 | CHF 1.– pro Ausleihe          | 1× um 1 Woche      | CHF 20.– pauschal                                 |
  | **Bücherkoffer / Themenpaket** | 6 Wochen  | *                 | kostenlos (für Schulen/Kitas) | nicht verlängerbar | Einzelbewertung                                   |

  Allgemeine Regeln (für alle Medienarten), z.B.

  - Maximal 20 Medien gleichzeitig pro Nutzer:in (konfigurierbar pro Mandant).
  - Verlängerung nicht möglich, wenn das Medium reserviert ist.
  - Bei Beschädigung entscheidet die Bibliothek / Verleiher im Einzelfall (kleine Schäden = kulant, grosse Schäden = Ersatz).
  - Alle Gebühren werden vor Ort bezahlt (Bar/Twint) — keine Online-Zahlung.

### 3.5 Reservierungen & Mahnwesen

- Reservierung verfügbarer und ausgeliehener Bücher.
- E-Mail-Benachrichtigung bei Verfügbarkeit.
- Automatische E-Mail-Reminder bei Fristüberschreitung.
- Keine Online-Bezahlung – Gebühren werden vor Ort beglichen.
- Alle Gebühren vor Ort bezahlbar (Bar/Twint) — die Plattform berechnet und kommuniziert sie nur.

Konfigurationsoptionen:

- Sperre des Nutzerkontos ab 3. Mahnung (ja/nein): keine neuen Ausleihen oder Reservierungen möglich, bis offene Posten beglichen sind.
- Eskalation Privat-Angebote (ja/nein): Eskalation läuft über die Plattform als Vermittler; eskaliert nach 2. Mahnung in eine direkte Kontaktaufnahme zwischen Anbieter und Leiher.
- Konfigurierbar pro Mandant: Bibliotheken können Stufen, Tage und Gebühren anpassen oder Stufen weglassen (z. B. kulantere Dorfbibliotheken), z.B.

| Stufe                   | Zeitpunkt                     | Aktion                                                      | Gebühr                           |
| ----------------------- | ----------------------------- | ----------------------------------------------------------- | -------------------------------- |
| **Vorab-Erinnerung**    | Tag 21 (1 Woche vor Ablauf)   | Freundliche E-Mail: „Ihre Leihfrist endet in 7 Tagen"       | –                                |
| **Fälligkeits-Hinweis** | Tag 28 (Ablauftag)            | E-Mail: „Heute ist Rückgabetag"                             | –                                |
| **1. Mahnung**          | Tag 29 (+1 Tag überfällig)    | E-Mail: „Rückgabe überfällig — bitte zeitnah zurückbringen" | –                                |
| **2. Mahnung**          | Tag 36 (+1 Woche überfällig)  | E-Mail mit ernsterem Ton                                    | CHF 2.–                          |
| **3. Mahnung**          | Tag 50 (+3 Wochen überfällig) | E-Mail + ggf. postalisch, Ausleihsperre wird angekündigt    | CHF 5.–                          |
| **Verlustmeldung**      | Tag 84 (+8 Wochen überfällig) | Medium gilt als verloren → Ersatzforderung, Konto gesperrt  | Wiederbeschaffungswert + CHF 5.– |

* Verkürzte Eskalation für Tonies (Leihfrist 2 Wochen / 14 Tage) -> ähnliche Tabelle mit anderen Zeitpunkten

### 3.6 Self-Service (Vertrauensprinzip)

- QR-Code an Rückgabebox → Scan via App löst automatische Rückbuchung aus.
- Self-Checkout für Ausleihe via QR-Scan.
- Kein Hardware-Lock-in.

### 3.7 Mobile App

- Plattform: **React Native / Expo** (iOS & Android).
- QR-Scanner für Bücher und Rückgabebox.
- Katalogsuche, Reservierung, Ausleihverwaltung.
- Push-Benachrichtigungen (optional, ergänzend zu E-Mail).
- Offline-Basisfunktionen (Bestand einsehen).

### 3.8 Freiwilligen-Koordination

- Freiwilligen-Profile mit Verfügbarkeiten.
- Schicht- und Aufgabenplanung.
- Zuweisung und Bestätigung von Aufgaben.

### 3.9 CMS, Blog & Veranstaltungen

- Erstellung von Beiträgen (Lesekoffer, Erzählnacht, Leserallye etc.).
- Veranstaltungskalender mit Anmeldemöglichkeit.
- Medien-Uploads (Bilder, PDFs).
- Mehrsprachige Inhalte.

### 3.10 KI-Unterstützung

- Personalisierte Empfehlungen basierend auf Nutzerprofil, Ausleihhistorie und Themen-Communities.
- Semantische Suche mit natürlichsprachlichen Anfragen.
- Vorschläge zur automatischen Verschlagwortung neuer Bücher.
- Übersetzungsunterstützung für mehrsprachige Inhalte.
- **Hosting:** Offenes Modell auf Infomaniak (CH-DSG-konform).

### 3.11 Community

- Themen-Communities und Interessengruppen.
- Empfehlungen und Kommentare.
- Datenschutzkonforme Kontaktmöglichkeit zwischen Nutzer:innen.

### 3.12 Metadaten-Sync (DNB ↔ Open Library)

- **Lookup-Kaskade:** ISBN-Scan → DNB-Abfrage → lokale Speicherung → asynchroner Sync mit Open Library; bei fehlenden Daten manuelle Eingabe.
- **Duplikatvermeidung:** Mehrstufiger Match (ISBN-13/10 → Fuzzy auf Titel/Autor/Jahr → Edition unter bestehendem Work); Unsicheres in Manual-Review-Queue.
- **Datenqualität:** Keine Überschreibung; nur Ergänzung leerer Felder; GND-Schlagwörter aus DNB integrieren.
- **Quellenvermerk:** `source_records`-Feld, Edit-Comment „via Bibli-Wila Sync Bot (DNB CC0)", identifizierbarer User-Agent.
- **Cover:** Keine Eigenfotos urheberrechtlich geschützter Werke; typografische Platzhalter; Eigenfotos nur für gemeinfreie Klassiker.
- **Massenimports:** Ab ~500 Datensätzen Vorabkontakt mit `ol-tech@archive.org`; Rate-Limit ≤ 1 Edit/Sek.; Test-Lauf vor Vollimport.

### 3.13 Statistiken & Reporting

- Dashboards für Ausleihen, Bestand und Nutzung.
- Nachhaltigkeits-Kennzahlen (eingespartes Papier, Wiederverwendungsquote).

### 3.14 Erweiterungen V2 (vorbereitet)

- Tausch-Netzwerk für Spiele, Werkzeuge und Geräte (Leihothek-Konzept).
- Digitales Dorf-Archiv (Zeitungen, Chroniken, Ortsmuseum).
- Privater Buchbestand-Manager.

------

## 4. Nicht-funktionale Anforderungen

### 4.1 Datenschutz & Compliance

- Vollständige Konformität mit CH-DSG und DSGVO.
- Datenhaltung ausschliesslich in der Schweiz (Infomaniak).
- Datensparsamkeit, Auskunfts- und Löschrecht, Privacy by Design.

### 4.2 Sicherheit

- Passkeys/WebAuthn als bevorzugter Auth-Standard.
- Verschlüsselte Übertragung (TLS) und At-Rest-Verschlüsselung sensibler Daten.
- Rollen- und mandantenbasierte Zugriffstrennung.

### 4.3 Skalierbarkeit & Verfügbarkeit

- Mandantenfähige Architektur (1 → mehrere hundert Bibliotheken).
- Horizontale Skalierung der API.
- Asynchrone Sync-Jobs für Metadaten.

### 4.4 Usability & Barrierefreiheit

- Mehrsprachige UI (DE/FR/IT/EN).
- Mobile-first; Self-Service per QR-Code.
- Orientierung an WCAG 2.1 AA.

### 4.5 Offenheit & Wartbarkeit

- **Open Source unter AGPLv3**, Code-Hosting auf Codeberg.
- Saubere API-Schnittstellen (REST oder GraphQL).
- Dokumentation für Betreiber und Beitragende.

### 4.6 Nachhaltigkeit

- Schweizer Hosting (Infomaniak), ökologisch und rechtlich konsistent.
- Förderung von Wiederverwendung als Kern-Designziel.

------

## 5. Technische Rahmenbedingungen

| Bereich           | Entscheidung                                         |
| ----------------- | ---------------------------------------------------- |
| Web-Frontend      | AstroJS / React                                      |
| Mobile            | React Native (Expo)                                  |
| API               | REST oder GraphQL, gemeinsam für Web & Mobile        |
| Auth              | WebAuthn / Passkeys (+ E-Mail/Passwort als Fallback) |
| KI / LLM          | Offenes Modell auf Infomaniak (CH)                   |
| Hosting           | Infomaniak (CH)                                      |
| Lizenz            | AGPLv3                                               |
| Code-Hosting      | Codeberg                                             |
| Metadaten-Quellen | DNB (CC0), Open Library                              |

------

## 6. Annahmen & Einschränkungen

- Nur physische Vermittlung von Büchern; keine digitale Ausleihe.
- Haftung erfolgt analog (vergleichbar mit Videoverleih / Leihothek).
- Trägerschaft als gemeinnütziger Verein oder Genossenschaft (Prüfung läuft).
- Finanzierung über Mitgliedschaften, Sponsoring, öffentliche Förderung und Freiwilligenarbeit.

------

## 7. Technologie Entscheide

- AstroJS mit drizzle / kysely und PostgreSQL Anbindung
- REST + OpenAPI 3.1;  JSON, versioniert via URL-Präfix (/v1/...) mit Auto-generierte TypeScript-Clients für Web und Mobile
