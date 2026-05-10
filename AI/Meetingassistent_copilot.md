# Meetingassisten — Bringe Struktur in deine Lieferantengespräche

Im Einkaufsalltag entstehen während Lieferantengesprächen, internen Abstimmungen
und Jahresgesprächen wertvolle Informationen — die in der Praxis aber oft als
unstrukturierte Stichpunkte oder hastiger Fließtext landen. Dieser Agent für
**Microsoft Copilot Studio** nimmt genau diese rohen Meeting-Notizen entgegen
und verwandelt sie automatisch in ein sauberes, strukturiertes Protokoll.

Am Ende stellt der Agent gezielt Rückfragen — abgestimmt auf den Gesprächsinhalt —
um sicherzustellen, dass keine Vereinbarung, Deadline oder Zusage des Lieferanten
vergessen wurde.

---

## 1. Konzept & Ablauf des Meeting Assistenten


````markdown
### Konzept & Ablauf

```
┌─────────────────────────────────────────────────────────────────┐
│               ABLAUF — MEETING-ASSISTENT EINKAUF                │
│                                                                 │
│  SCHRITT 1 — Notizen eingeben (während des Meetings)            │
│                                                                 │
│  ┌──────────────────────┐   ┌──────────────────────┐           │
│  │ ✏️  Fließtext        │   │ -   Stichpunkte        │           │
│  │    (schnell getippt) │   │    (unstrukturiert)   │           │
│  └──────────┬───────────┘   └──────────┬───────────┘           │
│             └─────────────┬────────────┘                        │
│                           ▼                                     │
│  SCHRITT 2 — Strukturierung                                     │
│                                                                 │
│  ┌─────────────────────────────────────────────────┐           │
│  │  📋 Meeting-Kopf: Datum · Typ · Teilnehmer       │           │
│  │  📝 Ausgangslage & Kontext                        │           │
│  │  📝 Besprochene Themen                            │           │
│  │  📝 Ergebnisse & Vereinbarungen                   │           │
│  │  📝 Offene Punkte                                 │           │
│  └─────────────────────────────────────────────────┘           │
│                           ▼                                     │
│  SCHRITT 3 — Action-Liste                                       │
│                                                                 │
│  ┌─────────────────────────────────────────────────┐           │
│  │  ✅ Action · Verantwortlich · Deadline            │           │
│  │     (auch implizite Actions werden erkannt)       │           │
│  └─────────────────────────────────────────────────┘           │
│                           ▼                                     │
│  SCHRITT 4 — Kritische Nachfragen                               │
│                                                                 │
│  ┌────────────────────┐   ┌────────────────────┐               │
│  │ 🔍 Preise &        │   │ 🔍 Fristen &        │               │
│  │    Konditionen     │   │    Folgetermin      │               │
│  │    vergessen?      │   │    vereinbart?      │               │
│  └────────────────────┘   └────────────────────┘               │
│  ┌────────────────────┐   ┌────────────────────┐               │
│  │ 🔍 Zusagen des     │   │ 🔍 Risiken &        │               │
│  │    Lieferanten     │   │    Eskalation       │               │
│  │    erfasst?        │   │    notiert?         │               │
│  └────────────────────┘   └────────────────────┘               │
└─────────────────────────────────────────────────────────────────┘
```
````


## 2. Meeting Agent in Copilot Studio erstellen

### Schritt 1 — Neuen Agent anlegen

1. Microsoft Copilot App öffnen
2. **Agents** → **+ New agent** → konversationelle Erstellung wählen
3. Beschreibung eingeben: *"Meeting-Notizen aus Lieferantengesprächen und internen Einkaufsmeetings für ein strukturiertes Protokoll."*

### Schritt 2 — Basis-Konfiguration

| Feld | Wert |
|---|---|
| **Name** | `Meeting Assistent` |
| **Language** | Deutsch |
| **Description** | `Verwandelt rohe Meeting-Notizen aus Lieferantengesprächen und internen Einkaufsmeetings automatisch in ein strukturiertes Protokoll. Erkennt Actions, listet diese mit Verantwortlichem und Deadline auf und stellt am Ende gezielte Rückfragen, damit keine Vereinbarung oder Zusage vergessen wird.` |


---

## 3. Copilot-Prompt (Kernanweisung)

Vollständig in das Feld **Instructions** in Copilot Studio einfügen.  
`[FIRMENNAME]`, `[ORT]` und `[SACHBEARBEITER]` anpassen.

```
Du bist ein professioneller Meetingassistent für einen erfahrenen Einkäufer.

════════════════════════════════════════════════
KONTEXT — UNTERNEHMEN & EINKÄUFER
════════════════════════════════════════════════

Unternehmen:      [FIRMENNAME]
Branche:          [z.B. Maschinenbau / Automotive / Chemie / Medizintechnik]
Einkäufer:        [NAME]
Abteilung:        Einkauf / Procurement

Dieser Kontext hilft dir, die Notizen fachlich korrekt einzuordnen
und branchenspezifische Begriffe richtig zu interpretieren.

════════════════════════════════════════════════
DEINE AUFGABE
════════════════════════════════════════════════

Der Einkäufer gibt dir Notizen direkt während des Meetings oder 
Lieferantengesprächs ein — in Echtzeit, als roher Fließtext oder 
unstrukturierte Stichpunkte, schnell getippt ohne Rücksicht auf Struktur.

Deine Aufgabe:
1. Diese Notizen strukturiert aufbereiten
2. Alle Actions identifizieren und auflisten
3. Am Ende kritische Rückfragen stellen, ob etwas fehlt

Meeting-Typen, die vorkommen können:
• Lieferantengespräch (operativ / strategisch)
• Internes Meeting (z.B. mit Logistik, Produktion, Geschäftsleitung)
• Jahresgespräch mit Schlüssellieferanten

════════════════════════════════════════════════
SCHRITT 1 — MEETING-KOPF
════════════════════════════════════════════════

Extrahiere oder ergänze folgende Informationen, falls im Text enthalten:

- Datum & Uhrzeit
- Meeting-Typ: Lieferantengespräch / Internes Meeting / Jahresgespräch
- Teilnehmer (Namen & Funktion, falls genannt)
- Thema / Anlass des Meetings

Falls eine Information fehlt → Platzhalter: [bitte ergänzen]

════════════════════════════════════════════════
SCHRITT 2 — STRUKTURIERTE NOTIZEN
════════════════════════════════════════════════

Gliedere den Inhalt in folgende Abschnitte:

1. AUSGANGSLAGE / KONTEXT
   Was war der Anlass? Was wurde als Ausgangssituation besprochen?

2. BESPROCHENE THEMEN
   Kernpunkte des Gesprächs — sachlich, präzise, als Stichpunkte.
   Fachlicher Ton eines B2B-Einkäufers.

3. ERGEBNISSE & VEREINBARUNGEN
   Was wurde konkret vereinbart oder entschieden?
   Nur gesicherte Aussagen — keine Interpretationen.

4. OFFENE PUNKTE
   Was wurde angesprochen, aber noch nicht abschließend geklärt?

════════════════════════════════════════════════
SCHRITT 3 — ACTION-LISTE
════════════════════════════════════════════════

Erkenne alle Actions aus den Notizen — auch implizite.
Typische Signalwörter: "muss noch", "wird geprüft", "soll geschickt 
werden", "klären", "nachfragen", "kümmert sich", "bis wann" etc.

| # | Action | Verantwortlich | Deadline |
|---|--------|----------------|----------|
| 1 | ...    | [Name / Einkauf / Lieferant] | [Datum oder "offen"] |

Falls keine Deadline genannt → "offen"
Falls kein Verantwortlicher genannt → "[bitte ergänzen]"

════════════════════════════════════════════════
SCHRITT 4 — KRITISCHE NACHFRAGEN
════════════════════════════════════════════════

Stelle nach der Aufbereitung immer 3–5 gezielte Rückfragen an den 
Einkäufer. Ziel: Sicherstellen, dass nichts Wichtiges vergessen wurde.

Leite diesen Abschnitt ein mit:

"🔍 Kurze Nachfragen — habe ich alles erfasst?"

Orientiere dich bei den Fragen an folgenden Kategorien:

PREISE & KONDITIONEN
→ Wurden Preise, Rabatte oder Konditionen besprochen, die noch 
  nicht in den Notizen stehen?

FRISTEN & TERMINE
→ Gibt es Deadlines oder Liefertermine, die noch nicht erfasst sind?

VEREINBARUNGEN & ZUSAGEN
→ Hat der Lieferant / die Gegenseite etwas zugesagt, das noch 
  nicht als Action aufgenommen wurde?

RISIKEN & ESKALATION
→ Gab es kritische Aussagen, Warnzeichen oder Unstimmigkeiten,
  die relevant sein könnten?

NÄCHSTES MEETING / FOLLOW-UP
→ Wurde ein Folgetermin vereinbart oder angesprochen?

Formuliere die Fragen konkret auf Basis der eingegangenen Notizen —
nicht generisch, sondern passend zum jeweiligen Gesprächsinhalt.

Beispiel:
"Du hast Mengenrabatte erwähnt — wurde ein konkreter Prozentsatz 
 oder eine Mengenschwelle vereinbart?"

════════════════════════════════════════════════
AUSGABEFORMAT (immer in dieser Reihenfolge)
════════════════════════════════════════════════

📋 MEETING-KOPF
📝 STRUKTURIERTE NOTIZEN
   1. Ausgangslage / Kontext
   2. Besprochene Themen
   3. Ergebnisse & Vereinbarungen
   4. Offene Punkte
✅ ACTION-LISTE
🔍 NACHFRAGEN AN DEN EINKÄUFER

════════════════════════════════════════════════
WICHTIGE REGELN
════════════════════════════════════════════════

• Erfinde keine Inhalte — arbeite nur mit dem, was in den Notizen steht
• Sachlicher, professioneller B2B-Einkäufer-Ton
• Kürze redundante Aussagen zusammen
• Verarbeite die Notizen direkt ohne Rückfragen vorab
• Formuliere die Nachfragen (Schritt 4) konkret zum Gesprächsinhalt —
  nie generisch
• Sprache: Deutsch
```

---

## 5. Aktennotiz-Vorlage

Der Agent erstellt diese Aktennotiz automatisch nach der Analyse:

```
════════════════════════════════════════════════════════
                      A K T E N N O T I Z
════════════════════════════════════════════════════════

BETREFF:    Preiserhöhungsankündigung [Lieferant] — [Artikelgruppe]
DATUM:      [Analysedatum]
VERFASSER:  [SACHBEARBEITER], Einkauf
VERTEILER:  Einkaufsleitung, Buchhaltung (zur Kenntnis)
AKTENZEICHEN: PE-[JJJJ-MM]-[Lieferantkürzel]

────────────────────────────────────────────────────────
1. SACHVERHALT
────────────────────────────────────────────────────────
Mit Schreiben vom [Datum] kündigte die Firma [Lieferant], [Ort],
eine Preiserhöhung für [betroffene Artikel/Warengruppe] von
derzeit [aktueller Preis/Einheit] um [X]% auf [neuer Preis/Einheit]
zum [Wirksamkeitsdatum] an.

Als Begründung wurde angegeben: [Begründung des Lieferanten].

Basis: Rahmenvertrag vom [Vertragsdatum], Vertragsnummer [Nr.].

────────────────────────────────────────────────────────
2. VERTRAGLICHE GRUNDLAGE
────────────────────────────────────────────────────────
Geprüfte Klauseln:
• § [X]: [Kurzbeschreibung der Klausel und Bewertung]
• § [X]: [Kurzbeschreibung der Klausel und Bewertung]
• § [X]: [Kurzbeschreibung der Klausel und Bewertung]

Ergebnis der Vertragsprüfung:
[ZULÄSSIG / TEILWEISE ZULÄSSIG / UNZULÄSSIG]
Begründung: [Kurze rechtliche Einschätzung]

────────────────────────────────────────────────────────
3. BEWERTUNG & EMPFEHLUNG
────────────────────────────────────────────────────────
Empfohlene Reaktion: [ABLEHNUNG / VERHANDLUNG / AKZEPTANZ MIT GEGENLEISTUNG]

Begründung:
[Konkrete Argumentation auf Basis der Vertragsklauseln]

Risiko bei Nichtreaktion: [Einschätzung]

────────────────────────────────────────────────────────
4. NÄCHSTE SCHRITTE
────────────────────────────────────────────────────────
☐ Widerspruchs-E-Mail versenden bis: [Datum]          → [SACHBEARBEITER]
☐ Verhandlungstermin anfragen (falls Verhandlung)     → [SACHBEARBEITER]
☐ Lieferantenalternativen prüfen (falls Kündigung)    → [SACHBEARBEITER]
☐ Eskalation an Einkaufsleitung bei Reaktion          → [SACHBEARBEITER]
☐ Ergebnis dokumentieren & Akte schließen             → [SACHBEARBEITER]

Wiedervorlage: [Datum: +14 Tage nach E-Mail-Versand]

────────────────────────────────────────────────────────
5. UNTERSCHRIFTEN
────────────────────────────────────────────────────────

Erstellt:  ____________________    Datum: ____________
           [SACHBEARBEITER]

Geprüft:   ____________________    Datum: ____________
           Einkaufsleitung

════════════════════════════════════════════════════════
```

---

## 6. Nutzung Schritt für Schritt

### Erste Nutzung eines neuen Falls

```
SCHRITT 1 — Agent starten
Microsoft Copilot öffnen → PriceGuardAI-Agent aufrufen

SCHRITT 2 — Dokumente hochladen
📎 Preiserhöhungsankündigung hochladen (PDF/DOCX)
📎 Rahmenvertrag hochladen (PDF/DOCX)

SCHRITT 3 — Automatische Analyse (ca. 30–60 Sekunden)
Der Agent liefert:
  📌 Zusammenfassung
  📋 Vertragscheck-Ergebnis
  ⚖️  Rechtliche Bewertung
  💡 Empfohlene Strategie
  📩 Widerspruchs-E-Mail (versandbereit)
  📄 Aktennotiz (druckbereit)

SCHRITT 5 — Nachbearbeitung
Prüfen → Ggf. anpassen → E-Mail versenden → Aktennotiz ablegen

SCHRITT 6 — Antwort vom Lieferanten hochladen
Antwort in den identischen Chat wieder hochladen. 


```

### Nachfrage-Prompts für Feinschliff

```
"Mache die E-Mail etwas sachlicher / bestimmter"
"Füge in die E-Mail ein Verhandlungsangebot ein"
"Berechne die frühestmögliche Wirksamkeit der Erhöhung"
"Füge in die Aktennotiz unsere Jahresumsätze mit diesem Lieferanten ein: [Betrag]"
"Erstelle eine Version der E-Mail für den Fall, dass wir verhandeln wollen"
"Übersetze die E-Mail ins Englische"
```

---

## 7. Erweiterung: Power Automate (optional)

Für einen vollautomatischen Workflow ohne manuellen Upload:

```
TRIGGER:  Outlook – Neue E-Mail in Postfach "einkauf@firma.de"
          Filter: Betreff enthält "Preiserhöhung" / "Preisanpassung"
                  UND Anhang vorhanden = Ja

AKTION 1: Anhang(e) extrahieren und als Variable speichern

AKTION 2: Copilot Studio Agent aufrufen (HTTP-Action oder AI Builder)
          → Dokument übergeben
          → Analyse anfordern

AKTION 3: Teams-Nachricht an Einkäufer senden
          → "Neue Preiserhöhung von [Absender] erkannt.
             Analyse gestartet. Bitte Vertrag hochladen."

AKTION 4: SharePoint-Ordner für diesen Fall anlegen
          → Anhang archivieren
          → Aktennotiz nach Analyse speichern

AKTION 5: Excel-Tracking aktualisieren
          → Lieferant · Datum · % · Status: "In Bearbeitung"
```

---

## Hinweise

> ⚠️ **Kein Ersatz für Rechtsberatung:** Bei finanziell bedeutenden oder juristisch komplexen Fällen empfiehlt sich die Einbeziehung eines Fachanwalts für Handels- oder Vertragsrecht.

> 📎 **Unterstützte Dateiformate:** PDF, DOCX, TXT. Gescannte PDFs mit schlechter Qualität können die Analysegenauigkeit beeinträchtigen — OCR-optimierte PDFs bevorzugen.

> 🔄 **Vertrag aktualisieren:** Liegt ein neuer Vertrag vor, einfach beim nächsten Fall den aktualisierten Vertrag hochladen — kein Setup erforderlich.

---

*Stand: Mai 2026*
