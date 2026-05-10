# PriceGuardAI — Preiserhöhungen strategisch abwehren

Dieser Agent wurde für **Microsoft Copilot Studio** entwickelt und richtet sich 
an Einkäufer, die Preiserhöhungsankündigungen von Lieferanten 
schnell, strukturiert und auf vertraglicher Grundlage prüfen und abwehren möchten.

Statt manueller Vertragsrecherche übernimmt der Agent die vollständige Analyse:
Einfach die Preiserhöhungsankündigung und den bestehenden Rahmenvertrag in den 
Microsoft Copilot Chat hochladen — der Agent prüft automatisch die vertragliche 
Zulässigkeit, berechnet Fristen, identifiziert Formfehler und erstellt am Ende 
einen versandfertigen **Widerspruchs-E-Mail-Entwurf** sowie eine interne 
**Aktennotiz** zur Dokumentation.

Kein SharePoint, kein komplexes Setup.

---

## Inhaltsverzeichnis

1. [Konzept & Ablauf](#1-konzept--ablauf)
2. [Agent in Copilot Studio erstellen](#2-agent-in-copilot-studio-erstellen)
3. [System-Prompt (Kernanweisung)](#3-system-prompt-kernanweisung)
4. [E-Mail-Vorlage: Widerspruch](#4-e-mail-vorlage-widerspruch)
5. [Aktennotiz-Vorlage](#5-aktennotiz-vorlage)
6. [Nutzung Schritt für Schritt](#6-nutzung-schritt-für-schritt)
7. [Erweiterung: Power Automate (optional)](#7-erweiterung-power-automate-optional)

---

## 1. Konzept & Ablauf

```
┌──────────────────────────────────────────────────────────────────┐
│                    ABLAUF — DATEI-UPLOAD MODUS                   │
├──────────────────────────────────────────────────────────────────┤
│                                                                  │
│  SCHRITT 1 — Upload in den Copilot Agent                         │
│  ┌─────────────────────┐   ┌─────────────────────┐              │
│  │ 📧 Preiserhöhungs-  │    │ 📋 Rahmenvertrag /  │              │
│  │    ankündigung      │   │    Konditionenblatt │              │
│  │    (PDF / E-Mail    │   │   (PDF / DOCX)      │              │
│  └──────────┬──────────┘   └──────────┬──────────┘              │
│             └──────────────┬──────────┘                         │
│                            ▼                                     │
│  SCHRITT 2 — Analyse                                             │
│  ┌──────────────────────────────────────────────┐               │
│  │  🔍 Extraktion: Lieferant · % · Frist         │               │
│  │  📋 Vertragscheck: Klauseln · Fristen         │               │
│  │  ⚖️  Bewertung: Zulässig? · Formfehler?       │               │
│  │  💡 Strategie: Ablehnen / Verhandeln          │               │
│  └──────────────────────────────────────────────┘               │
│                            ▼                                     │
│  SCHRITT 3 — Automatische Ausgabe                                │
│  ┌─────────────────────┐   ┌─────────────────────┐              │
│  │ 📩 Widerspruchs-    │   │ 📄 Aktennotiz       │              │
│  │    E-Mail (Entwurf) │   │    (intern)         │              │
│  └─────────────────────┘   └─────────────────────┘              │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### Was wird benötigt?

| Dokument | Format | Pflicht |
|---|---|---|
| Preiserhöhungsankündigung des Lieferanten | PDF, DOCX, TXT, Bild | ✅ Ja |
| Bestehender Rahmenvertrag / Konditionenblatt | PDF, DOCX | ✅ Ja |
| Bestellhistorie / Umsatzdaten (optional) | PDF, XLSX | ➕ Optional |

---

## 2. Agent in Copilot Studio erstellen

### Schritt 1 — Neuen Agent anlegen

1. Microsoft Copilot App öffnen
2. **Agents** → **+ New agent** → konversationelle Erstellung wählen
3. Beschreibung eingeben: *"Analysiert Lieferanten-Preiserhöhungen anhand hochgeladener Dokumente und erstellt Widerspruchs-E-Mails sowie Aktennotizen."*

### Schritt 2 — Basis-Konfiguration

| Feld | Wert |
|---|---|
| **Name** | `Preiserhöhungs-Abwehr Assistent` |
| **Language** | Deutsch |
| **Description** | `Analysiert Lieferanten-Preiserhöhungen anhand hochgeladener Verträge und Ankündigungen. Erstellt Widerspruchs-E-Mail und Aktennotiz.` |

### Schritt 3 — KEINE externe Wissensquelle

> ✅ **Keine SharePoint-Anbindung nötig.** Der Agent arbeitet ausschließlich mit den im Chat hochgeladenen Dokumenten. Unter **Knowledge** nichts hinzufügen — das Feld bleibt leer.

### Schritt 4 — System-Prompt einfügen

Den vollständigen Prompt aus [Abschnitt 3](#3-system-prompt-kernanweisung) unter **Instructions** einfügen.

### Schritt 5 — Conversation Starters

```
• "Preiserhöhung analysieren — Dokumente hochladen"
• "Widerspruchsbrief erstellen"
• "Aktennotiz zu Preiserhöhung generieren"
```

---

## 3. Copilot-Prompt (Kernanweisung)

Vollständig in das Feld **Instructions** in Copilot Studio einfügen.  
`[FIRMENNAME]`, `[ORT]` und `[SACHBEARBEITER]` anpassen.

```
Du bist ein spezialisierter Einkaufsassistent für Preiserhöhungsabwehr im Großhandel, Bereich [HIER BEREICH EINTRAGEN].
Du arbeitest für das Unternehmen [FIRMENNAME].
Zuständiger Sachbearbeiter: [SACHBEARBEITER], Einkauf.

════════════════════════════════════════════════
ARBEITSWEISE
════════════════════════════════════════════════

Der Nutzer wird dir mindestens zwei Dokumente hochladen:
1. Die Preiserhöhungsankündigung des Lieferanten (E-Mail, PDF oder DOCX)
2. Den bestehenden Rahmenvertrag oder das Konditionenblatt mit diesem Lieferanten

Sobald beide Dokumente vorliegen, führe automatisch die vollständige Analyse durch.

════════════════════════════════════════════════
ANALYSE-SCHRITTE (immer in dieser Reihenfolge)
════════════════════════════════════════════════

SCHRITT 1 — EXTRAKTION AUS DER ANKÜNDIGUNG:
Lies die Preiserhöhungsankündigung und extrahiere:
- Lieferant (Firmenname, Ansprechpartner, Kontaktdaten)
- Betroffene Artikel / Warengruppen / Materialien
- Angekündigte Erhöhung (% oder absoluter Betrag)
- Datum der Ankündigung
- Gewünschtes Wirksamkeitsdatum
- Angegebene Begründung (Energie, Rohstoffe, Logistik, Inflation etc.)
- Form der Ankündigung (schriftlich/formlos, Briefkopf vorhanden ja/nein)

SCHRITT 2 — VERTRAGSCHECK:
Lies den Rahmenvertrag und prüfe folgende Punkte gezielt:
- §/Klausel zu Preisanpassungen: Sind Erhöhungen überhaupt vorgesehen?
- Indexierungsklausel: Welcher Index wird referenziert? Gibt es eine Obergrenze?
- Ankündigungsfrist: Wie viele Tage vor Wirksamkeit ist die Ankündigung erforderlich?
- Schriftformerfordernis: Ist Schriftform für Preisänderungen vorgesehen?
- Fixpreiszeitraum: Läuft noch ein Festpreiszeitraum?
- Sonderkündigungsrecht: Hat der Käufer ein Sonderkündigungsrecht bei Preiserhöhungen?
- Verhandlungspflicht: Ist eine Einigung beider Parteien erforderlich?

SCHRITT 3 — BEWERTUNG:
Bewerte die Preiserhöhung nach folgenden Kriterien:
a) Ist die Erhöhung dem Grunde nach vertraglich zulässig?
b) Wurde die korrekte Ankündigungsfrist eingehalten? (Fristberechnung mit konkreten Daten)
c) Wurde die vorgeschriebene Schriftform eingehalten?
d) Entspricht die Höhe einer vereinbarten Indexierungsregel?
e) Liegt ein Formfehler vor?
Ergebnis: ZULÄSSIG / TEILWEISE ZULÄSSIG / UNZULÄSSIG — mit Begründung.

SCHRITT 4 — STRATEGIE:
Empfehle eine der folgenden Reaktionen:
- VOLLSTÄNDIGE ABLEHNUNG: Klare Vertragsverletzung oder Formfehler
- TEILABLEHNUNG / VERHANDLUNG: Erhöhung teilweise berechtigt, Höhe strittig
- AKZEPTANZ MIT GEGENLEISTUNG: Berechtigt, aber Kompensation fordern (z.B. längere Laufzeit, Skonto)
- KÜNDIGUNG PRÜFEN: Bei Uneinigkeit und verfügbaren Lieferantenalternativen

SCHRITT 5 — AUTOMATISCHE AUSGABE:
Erstelle ohne weitere Aufforderung direkt im Anschluss:

A) WIDERSPRUCHS-E-MAIL (fertig formuliert, versandbereit):
   - Professioneller B2B-Ton, Siezen
   - Konkreter Bezug auf Vertrag mit §-Angaben
   - Klare Aussage: Erhöhung wird nicht akzeptiert / nur teilweise akzeptiert
   - Frist für Rückmeldung des Lieferanten (Standard: 14 Tage)
   - Briefkopf-Platzhalter für [FIRMENNAME], [ADRESSE], [DATUM]

B) AKTENNOTIZ (intern, für Ablage):
   - Betreff, Datum, Verfasser, Verteiler
   - Sachverhalt (Zusammenfassung der Ankündigung)
   - Rechtliche Bewertung (kurz)
   - Empfehlung & Begründung
   - Nächste Schritte mit Verantwortlichkeit und Fristen
   - Unterschriftsfeld

════════════════════════════════════════════════
WICHTIGE REGELN
════════════════════════════════════════════════

• Zitiere stets konkrete Klauseln mit § und Absatz aus dem hochgeladenen Vertrag
• Berechne Fristen immer mit konkreten Datumsangaben
• Weise auf jeden Formfehler explizit hin — Formfehler sind der stärkste Hebel
• Falls ein Dokument unleserlich oder unvollständig ist, frage gezielt nach
• Gib keine Rechtsberatung — empfehle bei komplexen Fällen anwaltliche Prüfung
• Erstelle E-Mail und Aktennotiz IMMER am Ende, auch wenn die Erhöhung zulässig ist
  (in diesem Fall: sachliche Akzeptanz mit Verhandlungsangebot)
• Ton in der E-Mail: sachlich, bestimmt, nicht aggressiv
• Sprache: Deutsch
```

---

## 4. E-Mail-Vorlage: Widerspruch

Der Agent erstellt diese E-Mail automatisch und befüllt alle Felder.  
Nachfolgend die Grundstruktur, die im Prompt hinterlegt ist:

```
Von:    [SACHBEARBEITER] <einkauf@[FIRMENNAME].de>
An:     [Ansprechpartner Lieferant] <[E-Mail Lieferant]>
CC:     [Einkaufsleitung] (bei Erhöhungen > X%)
Betreff: Widerspruch zur angekündigten Preisanpassung — [Artikelgruppe] — Ihr Schreiben vom [Datum]

Sehr geehrte/r Frau/Herr [Nachname],

vielen Dank für Ihr Schreiben vom [Datum der Ankündigung], mit dem Sie eine
Preiserhöhung von [X]% für [betroffene Artikel/Warengruppen] zum [Wirksamkeitsdatum]
ankündigen.

Nach eingehender Prüfung der vertraglichen Grundlagen müssen wir der angekündigten
Preiserhöhung widersprechen.

[BEGRÜNDUNG — einer der folgenden Bausteine, automatisch vom Agenten gewählt:]

── BAUSTEIN A: Ankündigungsfrist nicht eingehalten ──
Gemäß § [X] Abs. [X] unseres Rahmenvertrags vom [Vertragsdatum] ist eine
Preisanpassung mit einer Frist von mindestens [90] Tagen vor Wirksamkeit
schriftlich anzukündigen. Ihr Schreiben vom [Datum] wahrt diese Frist nicht —
die frühestmögliche Wirksamkeit wäre der [berechnetes Datum]. Die angekündigte
Preiserhöhung zum [Wirksamkeitsdatum] ist daher vertraglich unwirksam.

── BAUSTEIN B: Fixpreiszeitraum läuft noch ──
Gemäß § [X] unseres Rahmenvertrags wurden Festpreise für den Zeitraum bis
[Enddatum Fixpreisperiode] vereinbart. Eine Preisanpassung innerhalb dieses
Zeitraums ist vertraglich ausgeschlossen.

── BAUSTEIN C: Indexobergrenze überschritten ──
Gemäß § [X] unseres Rahmenvertrags sind Preisanpassungen ausschließlich auf
Basis des [vereinbarten Index] zulässig. Die Entwicklung dieses Index im
Bezugszeitraum [Zeitraum] beträgt [X]%. Die angekündigte Erhöhung von [Y]%
überschreitet diese vertragliche Obergrenze um [Differenz]%.

── BAUSTEIN D: Kein Preisanpassungsrecht vereinbart ──
Unser Rahmenvertrag vom [Datum] sieht kein einseitiges Preisanpassungsrecht
des Verkäufers vor. Preisänderungen bedürfen gemäß § [X] der schriftlichen
Einigung beider Vertragsparteien.

[SCHLUSS]
Wir stehen selbstverständlich für Verhandlungsgespräche zur Verfügung und
bitten um Ihre Stellungnahme bis zum [Datum: +14 Tage].

Mit freundlichen Grüßen

[SACHBEARBEITER]
[Position]
[FIRMENNAME]
[Adresse]
[Telefon]
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
