---
title: "Viel los — MyPet, miniERP und ein wachsender Vault"
date: "2026-05-10T00:00:00Z"
draft: false
description: "Drei Dinge, die sich in den letzten Wochen verändert haben: MyPet ist fertig gedacht, miniERP ist fast fertig gebaut, und Obsidian ist nicht mehr wegzudenken."
isStarred: false
layout: ""
---

# Viel los

Vor zwei Wochen habe ich hier geschrieben, dass MyPet schlummert, das ERP wartet, und Schweden noch ein bisschen Geduld braucht.

Dann habe ich mich hingesetzt. Ernsthaft. Und die letzten Tage haben gleich drei Dinge auf einmal verändert.

---

## MyPet — Von Null auf Hundert

Das Projekt ist ein Monorepo unter `~/git/mypet/`. Sechs Services laufen in Docker-Containern. Das Backend ist in Dart mit Shelf geschrieben, die Frontends in Flutter.

Was früher "Login funktioniert, danach hört es auf" war, ist jetzt das:

- **Backend**: 38 Datenbankmigrationen, API-Version 0.3.0. Tierprofile, Laborbefunde, Körpertemperatur-Tracking, wiederkehrende Termine — das Datenmodell ist gewachsen, ohne auseinanderzufallen.
- **web_owner**: Globale Suche, ein Gesundheits-Score (0–100) pro Tier, Impfstatus-Ampel im Dashboard, Fütterungs-Streaks, Quick-Actions. Und Skeleton-Loading-Widgets, weil eine App, die sich träge anfühlt, sich falsch anfühlt — egal wie gut das Datenmodell dahinter ist.
- **web_vet**: Zwölf Tabs pro Patient, darunter ein Labor-Tab, Diagnosen-Chips, Druck von Impfzertifikaten und Dossiers. Patientenzuweisung, Wiederholungstermine, Medikamenten-Duplikat-Warnung.
- **web_provider** und **web_admin**: Tages-Agenda, Filterchips, CSV-Export, Wachstumsdiagramme.

Und dann ist da noch die **Android-App**.

Sechs Tabs per Bottom-Navigation: Übersicht, Meine Tiere, Medikamente, Erinnerungen, Termine, Profil. Jeder Tab mit eigenem Provider, eigenem State, eigener Logik. Der `PetDetailScreen` allein hat sieben Tabs — Impfungen, Medikamente, Akte, Allergien, Laborbefunde, Gewicht, Notizen.

Das Gewichts-Tracking hat einen eigenen Custom-Painter bekommen — eine kleine Sparkline-Kurve direkt in der App, ohne externe Bibliothek. Das war ein Nachmittag, der sich gelohnt hat.

Phase 100 ist abgeschlossen. Das Monorepo-Konzept zahlt sich aus: gemeinsame Typen zwischen Frontend und Backend, konsistente Datenstrukturen, keine doppelte Logik.

---

## miniERP — Ein ERP, das echte Arbeit abbildet

Das ist das Projekt, das ich selbst nicht kommen gesehen habe.

Die Idee: Ein kleines ERP für ein Einzelunternehmen mit zwei Sparten — Bauunternehmen und Hufbearbeitung. Angebote, Aufträge, Rechnungen, Stundenerfassung, Lieferantenrechnungen, Steuerberater-Export. Nicht mehr, nicht weniger.

**Stack**: FastAPI + SQLAlchemy + Alembic im Backend, Vue 3 + Vuetify 3 im Frontend, PostgreSQL als Datenbank. PDF-Erzeugung über **Gotenberg** — das Backend rendert Jinja-Templates zu HTML, Gotenberg liefert saubers PDF/A zurück. Für Dokumentenverwaltung hängt **Paperless-ngx** dran, für KI-Unterstützung **Ollama** lokal.

Was mich dabei am meisten beschäftigt hat, war der Grundsatz: **Freitext vor Stammdaten**. In der Praxis gibt es bei einem Bauunternehmen selten identische Positionen. Wer Mitarbeiter zwingt, erst einen Stammartikel anzulegen, bevor sie ein Angebot schreiben können, baut Reibung ein — keine Software. Also: freie Eingabe als Standard, Stammdaten wachsen organisch nach, wenn sich etwas wirklich wiederholt.

Die Phasen liefen in rasantem Tempo durch:

- **Phase 0–1**: Gerüst, Docker, JWT-Login, Stammdaten (Kunden, Lieferanten)
- **Phase 2**: Angebote mit Positionseditor, PDF-Vorschau, Angebotsgruppen, Status-Workflow
- **Phase 3**: Aufträge, Auftragsbestätigungen, Stundenerfassung mit Wochenansicht
- **Phase 4**: Ausgangsrechnungen — Teil-, Abschlags-, Schlussrechnungen, Gutschriften, E-Rechnung (XRechnung / ZUGFeRD)
- **Phase 5**: Lieferantenrechnungen — Upload, OCR via Paperless, Strukturierung per Ollama
- **Phase 6**: Auswertungen, EÜR-Vorschau, Steuerberater-Export als ZIP-Bundle
- **Phase 7**: KI-Komfort — Stichworte werden zu ausformulierten Positionen, Freitext wird in Einzelpositionen aufgeteilt
- **Phase 8**: Härtung — GoBD-konforme Hashkette, append-only Audit-Log

Heute kam noch dazu: n8n ist raus. Was vorher über n8n-Workflows lief, macht jetzt ein einziges Shell-Script direkt als Paperless-Webhook. Einfacher, robuster, keine extra Moving Parts. Und das Profil-Menü ist fertig — Passwort ändern, Abmelden, alles da.

Was noch fehlt: Mandantendaten vollständig aus `.env` lesen statt hardcodiert, E-Mail-Versand, HBCI-Bankabgleich. Aber das Kern-System läuft.

---

## Obsidian — Der Vault wächst

Im April-Post hatte ich das Notizbuch erwähnt — Ideen zu Papier bringen, Strukturen skizzieren, offline denken.

Inzwischen ist daraus etwas geworden, das ich selbst nicht mehr missen will: ein Obsidian-Vault unter `~/git/obsidian_vault/`, der per Git synchronisiert wird — auch auf dem Android-Handy via Termux.

Der Vault hat eine klare Struktur:

```
00_Inbox      → alles Neue landet erst hier
10_Projects   → aktive Projekte (MyPet, miniERP, Motivhaus, …)
20_Infrastructure → Server, Docker, Konfigurationen
30_Freelance  → Kundenprojekte
40_Family     → Haushalt, Planung
50_Personal   → Persönliches
60_Learning   → Lernmaterialien
90_Daily      → Tagesnotizen
99_Claude_Skills → Skills für Claude Code
```

Was mich daran besonders freut: Claude Code schreibt inzwischen aktiv in den Vault. Es gibt ein sogenanntes **Vault-Capture-Setup** — eine Kombination aus einem globalen `CLAUDE.md`, einem Skill und einem Slash-Command. Wenn Claude und ich zusammen etwas herausfinden, das es wert ist, festgehalten zu werden, landet es strukturiert im Vault. Nicht jeder Zwischenschritt, sondern das, was wirklich zählt: nicht-offensichtliche Lösungen, Architekturentscheidungen, Erkenntnisse, die man sonst neu erarbeiten müsste.

Das klingt nach Details. Aber es verändert, wie ich mit dem Werkzeug arbeite. Wissen verschwindet nicht mehr im Chatverlauf.

---

# Schlussgedanke

Manchmal läuft alles gleichzeitig. Das ist selten bequem, aber meistens fruchtbar.

MyPet hat einen Stand, auf dem man aufbauen kann. miniERP hat Phasen, die ich selbst nicht für realistisch gehalten hätte. Und Obsidian ist vom gelegentlichen Notizbuch zum echten Arbeitswerkzeug geworden.

Das Haus steht übrigens noch. Schweden wartet noch. Aber der Norden wird schon WLAN haben.

---

# Kaffee

Über einen
[Kaffee](https://www.buymeacoffee.com/snuppedelua)
würde ich mich auf jeden Fall freuen.
