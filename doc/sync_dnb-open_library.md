# Neue Funktion: Sync DNB ↔ Open Library

Diese Funktion macht unsere Regalini Plattform zu einem aktiven Beitrag zur globalen Wissens-Allmende: Jedes neu erfasste Buch kann nicht nur lokal katalogisiert, sondern auch in den freien CC0 ("Creative Commons Zero") -Datenbestand zurückgespielt werden. Damit profitieren weltweit andere Bibliotheken, Forschende und Anwendungen — und unser Projekt wird Teil der "Linked Open Data"-Bewegung statt nur deren Konsument.

[toc]

------

## 1. Grundprinzip & Workflow

Beim Erfassen eines Buches per ISBN-Scan läuft im Hintergrund folgender Prozess:

1. **Lookup-Kaskade**: ISBN → DNB SRU → Open Library → (optional Google Books für Klappentext-Anzeige zur Laufzeit)
2. **Lokale Speicherung** der CC0-Felder in der eigenen Plattform-Datenbank
3. **Sync-Entscheidung** treffen: Existiert der Datensatz schon in Open Library? Reicht seine Qualität? Fehlt etwas?
4. **Falls Lücke erkannt**: Sync-Job in eine Queue stellen (nicht synchron, um den Erfassungs-Workflow nicht zu blockieren)
5. **Bot-Account** der Plattform trägt fehlende Felder in Open Library ein, mit korrektem Quellenvermerk

Der Sync läuft also als **Hintergrunddienst**, nicht als Live-Operation pro Buch. Das schont sowohl die Open-Library-Server als auch unsere eigenen Ressourcen.

------

## 2. Duplikatvermeidung — der kritischste Teil

Duplikate sind das Hauptproblem aller Crowd-Kataloge. Open Library hat bereits viele DNB-Daten, oft mehrfach (verschiedene Editionen, Reprints, ISBN-10 vs ISBN-13). Ihr braucht eine **mehrstufige Match-Strategie**:

**Stufe 1 — Exakter ISBN-Match**:

- Suche in Open Library nach ISBN-13 **und** ISBN-10 (beide Varianten prüfen)
- Wenn Treffer → Datensatz existiert, Sync-Modus = "ergänzen, nicht neu anlegen"

**Stufe 2 — Fuzzy-Match bei fehlender ISBN**:

- Manche Datensätze in Open Library haben keine ISBN. Prüfen via: Titel (normalisiert: Kleinbuchstaben, ohne Sonderzeichen) + Autor + Erscheinungsjahr ± 1
- Schwellwert für Match z.B. Levenshtein-Distanz < 3 auf Titel
- Bei Unsicherheit: **NICHT automatisch zusammenführen**, sondern in eine "Manual Review"-Queue stellen, die ein menschlicher Bibliothekar einmal pro Woche durchgeht

**Stufe 3 — Editions vs. Works**:

- Open Library unterscheidet zwischen `Work` (= das Werk an sich, z.B. "Faust I") und `Edition` (= eine konkrete Ausgabe mit ISBN). Sync immer auf **Edition-Ebene**, da DNB-Daten editionsspezifisch sind.
- Falls ein passendes `Work` existiert, aber die konkrete Edition fehlt → neue Edition unter dem bestehenden Work anlegen, **nicht ein neues Work**.

**Stufe 4 — Konflikt-Logging**:

- Jeder potenzielle Duplikat-Fall wird protokolliert (DNB-IDN, Open-Library-Key, Match-Score, Entscheidung). So ist jederzeit nachvollziehbar, warum etwas (nicht) synchronisiert wurde.

------

## 3. Quellenvermerk

Open Library bietet im Datenmodell das Feld `source_records`, das genau für solche Provenienz-Angaben gedacht ist. Konvention:

- Format: `bibli-wila:dnb-sync:<DNB-IDN>` oder `bibli-wila:<eigene-buch-id>`
- Zusätzlich im Bearbeitungs-Kommentar (Edit Comment) bei jedem Sync: *"Imported/enriched from Deutsche Nationalbibliothek (CC0) via Bibli-Wila Sync Bot, see https://bibli-wila.ch"*
- Bei Bot-Edits über die API: User-Agent korrekt setzen (`BibliWilaSyncBot/1.0 (kontakt@bibli-wila.ch)`) — Open Library erwartet identifizierbare Bots

Rechtlich ist das nicht erforderlich (CC0 → keine Pflicht), ethisch und praktisch aber zentral: Andere Editoren sehen sofort, woher die Daten stammen, und können bei Fragen oder Korrekturen zurückverfolgen.

------

## 4. Datenqualität — Regeln für "Wann überschreiben?"

Goldene Regel: **Bestehende Daten in Open Library nie blind überschreiben.** Stattdessen:

| Situation                                                    | Aktion                                            |
| ------------------------------------------------------------ | ------------------------------------------------- |
| Feld in Open Library leer, in DNB gefüllt                    | ✅ Eintragen                                       |
| Feld in beiden gefüllt, identisch                            | ⏭ Nichts tun                                      |
| Feld in beiden gefüllt, leicht unterschiedlich (Tippfehler, Formatvariation) | ⚠️ Manual-Review-Queue                             |
| Feld in beiden gefüllt, Open-Library-Wert reicher (mehr Schlagwörter, längere Beschreibung) | ⏭ Nichts tun, Open Library hat Vorrang            |
| Feld nur in Open Library gefüllt                             | ⏭ Open Library beibehalten, lokal ggf. übernehmen |
| GND-Schlagwörter fehlen in Open Library                      | ✅ Ergänzen (das ist ein klarer Mehrwert der DNB)  |

Die DNB ist bei **Sprache, Verlag, GND-Schlagwörtern, deutschem Erscheinungsjahr** meist verlässlicher; Open Library bei **internationalen Editionen, Beschreibungen, Covern**.

------

## 5. Cover — die rechtliche Knacknuss

Du hast zwei Ideen vorgeschlagen, und beide sind klug, aber lizenzrechtlich sehr unterschiedlich.

### Variante A: Eigene Foto + AI-Optimizer-Workflow (VERWORFEN)

**Die kurze Antwort**: Nein, leider nicht ohne Weiteres legal, und Open Library wird solche Cover voraussichtlich ablehnen.

**Die längere Antwort**:

Buchcover sind in fast allen Ländern als **eigenständige Werke urheberrechtlich geschützt** — sowohl die Grafik/Illustration (Designer, Künstler) als auch oft der typografische Satz (Verlag). Ein eigenes Foto eines Covers ist eine **Vervielfältigung** des Cover-Werks und damit eine Urheberrechtsverletzung, wenn sie ohne Zustimmung veröffentlicht wird. Der Akt des Fotografierens erzeugt keine neuen Rechte am ursprünglichen Werk — anders als z.B. bei einem kreativen Foto eines Sonnenuntergangs.

KI-Nachbearbeitung ändert daran **nichts** — im Gegenteil: Wenn die Bearbeitung das Original noch erkennbar lässt (was bei Cover-Verbesserung ja der Sinn ist), bleibt es rechtlich eine Bearbeitung des Originals und braucht weiterhin die Zustimmung des Rechteinhabers. Selbst ein "vom Cover inspiriertes" KI-Bild wäre rechtlich heikel, weil moderne Gerichte zunehmend strikt mit KI-generierten Ableitungen sind.

**Ausnahmen**, die für euch teilweise relevant sein könnten:

- **Gemeinfreie Werke**: Bücher, deren Autor seit > 70 Jahren tot ist UND deren Cover von einem ebenfalls > 70 Jahre toten Designer stammt → Cover dürfen frei verwendet werden. Praktisch: alte Klassiker-Ausgaben aus dem frühen 20. Jh. oder davor.
- **Vom Verlag freigegebene Cover**: Manche Verlage (z.B. O'Reilly für viele Titel) erlauben die freie Verwendung — muss pro Verlag geprüft werden.
- **CC-lizenzierte Bücher**: Bei Open-Access-Büchern mit CC-Lizenz ist meist auch das Cover entsprechend lizenziert.

**Was Open Library akzeptiert**: Eigene Fotos sind technisch erlaubt, aber der Hochladende erklärt damit, dass er die Rechte hat, das Bild unter den freien Bedingungen der Plattform zu veröffentlichen. Bei Cover-Fotos ist diese Zusicherung in fast allen Fällen rechtlich falsch — und Open Library hat in der Vergangenheit Cover bei berechtigten Beanstandungen entfernt.

**Empfehlung**: Foto-Workflow nur für **gemeinfreie / freigegebene Titel** verwenden, mit einer ausdrücklichen Whitelist im System. Für alle anderen → Variante B.

### Variante B: Vervollständigungs-E-Mail an Nutzer mit Suchlinks

**Die kurze Antwort**: Ja, das ist lizenzrechtlich völlig in Ordnung — und sogar eine elegante Lösung.

**Begründung**:

Du verwendest selbst kein geschütztes Material, sondern lieferst dem Nutzer nur Werkzeuge zum Suchen. Das ist rechtlich identisch mit "Google die ISBN deines Buches" — keine Vervielfältigung, keine Veröffentlichung, keine Bearbeitung. Der Nutzer entscheidet selbst, was er mit den Suchergebnissen macht.

**Konkrete Ausgestaltung**:

Wöchentliche/monatliche E-Mail an aktive Nutzer der Plattform mit:

- Liste der Bücher, bei denen Cover oder Beschreibung fehlen
- Pro Buch ein vorgenerierter **Brave-Such-Link**: `https://search.brave.com/search?q=<ISBN>+<Verlag>+cover`
- Hinweis-Text: *"Wenn du beim Verlag oder auf der Verlagsseite ein Cover findest, das ausdrücklich zur freien Nutzung freigegeben ist (z.B. CC-Lizenz oder Pressebereich), kannst du den Link hier melden: [Formular]"*
- Brave gewählt wegen Privacy-Fokus, kein Tracking — passt zum Open-Source-Ethos

**Warum das gut funktioniert**:

1. Eure Plattform vervielfältigt keine geschützten Werke
2. Nutzer sind aktiv eingebunden → Community-Building (passt zur Vision "Dritter Ort")
3. Nur ausdrücklich freigegebene Cover landen im System → rechtlich sauber
4. Skaliert mit der Community-Grösse, nicht mit dem Bestand

**Erweiterung**: Statt nur Cover suchen zu lassen, kannst du dem Nutzer die Möglichkeit geben, **direkt beim Verlag per vorgefertigter E-Mail anzufragen**: *"Sehr geehrter Verlag X, wir betreiben eine gemeinnützige Open-Source-Bibliotheksplattform und würden gerne das Cover von ISBN Y unter freier Lizenz verwenden. Wäre das möglich?"* — Erstaunlich viele Verlage sagen ja, vor allem kleinere und unabhängige.

### Hybrid-Empfehlung

- **Verlage mit Whitelist: ** → Cover direkt vom Verlag laden
- **Standard**: Variante B (Community-Suche)
- **Fallback**: Generiertes Platzhalter-Cover aus Titel + Autor (rein typografisch, neutrale Farbe) — rechtlich völlig unproblematisch und ästhetisch sogar charmant für Open-Source-Projekte

------

## 6. Massenimports — Absprache mit Open Library

Für die ersten ~100 Bücher der Pilotbibliothek Bibli-Wila läuft alles über die normale API ohne weitere Absprache. Sobald aber grössere Mengen synchronisieret werden müssen — also Bestände kompletter Bibliotheken oder Regalini wächst auf hunderte Standorte — gilt:

**Schwellwert**: Ab ca. 500–1000 Datensätzen pro Tag oder pro Bibliothek wird's "Bulk-Import-Territorium".

**Vorgehen**:

1. **Vorab-Kontakt** mit dem Open-Library-Team über deren Mailingliste (`ol-tech@archive.org`) oder GitHub-Repo (`internetarchive/openlibrary`). Vorstellung des Projekts, Beschreibung des Datenformats, Quelle, geschätzte Mengen, Frequenz.
2. **Bot-Account anlegen** mit klarem Namen (z.B. `BibliWilaBot`) und in der User-Page transparent dokumentieren, was der Bot tut.
3. **Bot-Policy einhalten**: Open Library erwartet, dass Bots eine niedrige Edit-Rate haben (z.B. max. 1 Edit/Sekunde), sich an Robots-Regeln halten und auf manuelle Stop-Anfragen reagieren.
4. **Test-Lauf** mit kleiner Stichprobe (10–20 Bücher), Review durch das OL-Team, dann Freigabe für grösseren Lauf.
5. **Dokumentation öffentlich**: Ein Wiki-Eintrag oder GitHub-README, der den Sync-Prozess beschreibt. Das schafft Vertrauen und erleichtert spätere Audits.
6. **Rate-Limiting clientseitig**: Auch ohne formale Vereinbarung den Sync-Job so bauen, dass er nie mehr als ein paar Edits pro Sekunde macht und über Nacht / am Wochenende läuft, wenn die Server weniger belastet sind.

Diese Absprache ist keine bürokratische Hürde, sondern ein Zeichen guten Bürgersinns in der Open-Data-Community. Open Library wird unser Projekt Regalini vermutlich begeistert begrüssen — gemeinnützige Bibliotheks-Plattformen sind genau ihre Zielgruppe.

------

## 7. Architektur-Übersicht des Sync-Subsystems

Auf Komponentenebene besteht der Sync aus:

- **Fetcher**: Holt DNB-Daten per SRU bei Bedarf
- **Matcher**: Implementiert die mehrstufige Duplikat-Logik gegen Open Library
- **Diff Engine**: Vergleicht Felder, entscheidet Aktion (eintragen / ignorieren / Review)
- **Queue**: Persistente Job-Queue (z.B. Redis/PostgreSQL-basiert)
- **Bot Worker**: Führt API-Calls gegen Open Library mit Rate-Limiting aus
- **Review UI**: Webinterface für menschliche Bibliothekare zur Konfliktauflösung
- **Audit Log**: Protokolliert jede Sync-Aktion für Nachvollziehbarkeit
- **Notifier**: Erstellt die wöchentlichen Cover-Vervollständigungs-Mails (Variante B)

------

