[toc]

---

**Version:** 1.0 **Status:** Entwurf 

**Weitere Dokumente: **

*  [vision.md](vision.md)  // [anforderungen.md](anforderungen.md)  //  [roadmap_finanzierung.md](roadmap_finanzierung.md) 
*  [architektur.md](architektur.md) 

---

# Funktionsliste Regalini

## 1. Benutzer- & Zugriffsverwaltung

- Registrierung/Login mit **Passkeys** und/oder E-Mail/Passwort
- Rollen- und Rechteverwaltung (Admin, Bibliothekar, Freiwilliger, Nutzer, Privatanbieter)
- Datenschutzkonforme Profilverwaltung (CH-DSG/DSGVO)
- **Mandantenfähigkeit**: skalierbar von 1 auf hunderte Bibliotheken
- **Mehrsprachige Oberfläche** (DE/FR/IT/EN)
- Nutzerinteressen/Präferenzen im Profil hinterlegbar (Basis für KI-Empfehlungen)

## 2. Bestandsverwaltung & Indizierung

- Einbuchen per ISBN/EAN mit automatischem Metadaten-Abruf

- QR-Code-Generierung und -Druck

- Ausbuchen ausgeschiedener Bücher

- Standort-/Regalverwaltung, Umbuchen

- Indizierung/Klassifizierung

   nach:

  - Ablageort (Bibliothek, Regal, Position)
  - Themen/Kategorien (Tags, Schlagwörter)
  - Sprachen
  - Zielgruppen / Nutzerinteressen

- Statusverwaltung (verfügbar, ausgeliehen, reserviert, ausgeschieden)

## 3. Gemeinsamer Katalog (bibliotheksübergreifend)

- **Einheitliche Suche über alle vernetzten Bestände**
- Filter nach Standort, Sprache, Thema, Verfügbarkeit, Entfernung
- **Virtuelle Themenkataloge** (z.B. „Umweltbibliothek", „Island-Krimis")
- Kartenansicht der Standorte
- Sichtbarkeit privater Bestände (mit individuellen Bedingungen)

## 4. Ausleihe & Vermittlung

- Ausleihvorgang erfassen (Wer, Was, Von wem, Bis wann)

- Rückgabe und Verlängerung

- Templates für Ausleihbedingungen

   (vordefiniert + anpassbar):

  - Ausleihfrist
  - Ersatzregelungen bei Verlust/Beschädigung
  - Zielgruppe (öffentlich, Mitglieder, privat)
  - Abholbedingungen

- Konfigurationsoptionen pro Bibliothek/Privatanbieter

- Übersicht offener Ausleihen pro Nutzer:in und Anbieter

- Ablaufdatum für Verleih-/Tausch-/Weitergabeangebote

## 5. Reservierungen & Mahnwesen

- Reservierung verfügbarer/ausgeliehener Bücher
- E-Mail-Benachrichtigungen bei Verfügbarkeit
- **Automatische E-Mail-Reminder** (keine Online-Bezahlung — Mahngebühren vor Ort: Bar/Twint)
- Eskalationsstufen konfigurierbar

## 6. Self-Service (Vertrauensprinzip)

- **QR-Code an Rückgabebox** → Scan via App = automatische Rückbuchung
- Self-Checkout für Ausleihe (QR-Scan)
- Kein Hardware-Lock-in, keine Kontrolle — Vertrauen statt Überwachung

## 7. Mobile App (React Native / Expo)

- QR-Scanner für Bücher und Rückgabebox
- Katalog-Suche, Reservierung, Ausleihverwaltung
- Push-Benachrichtigungen (optional, ergänzend zu E-Mail)
- Offline-Basisfunktionen (Bestand einsehen)

## 8. Freiwilligen-Koordination

- Freiwilligen-Profile und Verfügbarkeiten
- Schicht- und Aufgabenplanung
- Zuweisung und Bestätigung von Aufgaben

## 9. CMS / Blog / Veranstaltungen

- Beiträge erstellen (Lesekoffer, Erzählnacht, Leserallye etc.)
- Veranstaltungskalender mit Anmeldung
- Medien-Uploads (Bilder, PDFs)
- Mehrsprachige Inhalte

## 10. KI-Unterstützung

- Personalisierte Empfehlungen

   basierend auf:

  - Nutzerprofil und Interessen
  - Ausleihhistorie
  - Themen-Communities

- Beispiel: „Island-Krimis", „Bücher wie X"

- Semantische Suche (natürlichsprachliche Anfragen)

- Automatische Verschlagwortung neuer Bücher (Vorschläge)

- Übersetzungsunterstützung für mehrsprachige Inhalte

## 11. Community

- Themen-Communities / Interessengruppen
- Empfehlungen und Kommentare
- Datenschutzkonforme Kontaktmöglichkeit zwischen Nutzer:innen

## 12. Sync DNB ↔ Open Library

Katalogdaten / Stammdaten der Bücher werden von Open Library bezogen. Falls nicht vorhanden, werden diese durch Nutzer bereitgestellt.  [sync_dnb-open_library.md](sync_dnb-open_library.md) 

* **Lookup-Kaskade & Hintergrund-Sync**: ISBN-Scan triggert DNB-Abfrage, lokale Speicherung, asynchroner Sync-Job prüft Open Library und ergänzt fehlende CC0-Daten ("Creative Commons Zero") via Bot-Account; Bei fehlenden Daten: manuelle Eingabe durch Nutzer!

* **Duplikatvermeidung**: Mehrstufiger Match (ISBN-13/10 → Fuzzy auf Titel+Autor+Jahr → Edition unter bestehendem Work), Unsicheres landet in Manual-Review-Queue

* **Datenqualität**: Niemals blind überschreiben — leere Felder ergänzen, reichere OL-Daten respektieren, GND-Schlagwörter aus DNB als klarer Mehrwert eintragen

* **Quellenvermerk**: `source_records`-Feld + Edit-Comment "via Bibli-Wila Sync Bot (DNB CC0)" + identifizierbarer User-Agent

* **Cover**: Eigene Fotos urheberrechtlich problematisch (auch mit KI-Bearbeitung) → stattdessen Community-Mail mit Brave-Suchlinks, Verlags-Anfrage-Templates und typografische Platzhalter; Eigenfotos nur für gemeinfreie Klassiker

* **Massenimports**: Ab ~500 Datensätzen vorab Kontakt mit `ol-tech@archive.org`, dokumentierter Bot, Rate-Limit ≤ 1 Edit/Sek., Test-Lauf vor Vollimport

## 13. Statistiken & Reporting

- Dashboards (Ausleihen, Bestand, Nutzung)
- Nachhaltigkeits-Kennzahlen (eingespartes Papier, Wiederverwendung)

## 14. Erweiterungen V2 (vorbereitet)

- Tausch-Netzwerk für Spiele, Werkzeuge, Geräte
- Digitales Dorf-Archiv (Zeitungen, Chroniken)
- Privater Buchbestand-Manager

------

# Entscheidungen

**Tech-Stack-Empfehlung**: Web (z.B. AstroJS/React) + React Native (Expo) mit gemeinsamer API (REST/GraphQL)

**i18n** (DE/FR/IT/EN)

**WebAuthn/Passkeys** als Auth-Standard

**Skalierungspfad**: Mandantenfähige Architektur

**Keine Bezahl-Integration** → reduziert Komplexität und Compliance-Aufwand erheblich

**KI-Modell**: Offenes Modell auf Infomaniak (Schweizer DSG-konform). Infomaniak bietet seit 2024 LLM-Hosting in der Schweiz mit klaren Datenschutzgarantien. Passt zu Open Source und zur Vision "lokale Anlaufstelle". Embedding-Modelle für die semantische Suche und Empfehlungs-KI können dort ebenfalls gehostet werden.

**Hosting**: Infomaniak. Konsistent mit dem Datenschutz-Anspruch und schweizerische Wertschöpfung. 

**Open Source**: Ja,  **AGPLv3** , da so auch SaaS-Forks ihre Änderungen offenlegen müssen (wichtig bei einer vernetzten Plattform). Code-Hosting auf [Codeberg](https://codeberg.org/) (gemeinnützig, EU-basiert).

**Pilotbibliothek**: Bibliothek Wila. 

## Nächste Schritte

1. ~~Visuelle Architektur-Übersicht des Gesamtsystems~~  ->  [architektur.md](architektur.md) 
2. Datenmodell (Entitäten, Beziehungen, Schlüsselattribute).
3. API-Skizze und Mandanten-Konzept.
4. Pilotierung mit der Bibliothek Wila.
