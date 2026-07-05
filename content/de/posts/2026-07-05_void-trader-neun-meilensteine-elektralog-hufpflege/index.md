---
title: "Neun Meilensteine, ein Kamera-Bug und vier Kartons Büro"
date: "2026-07-05T00:00:00Z"
draft: false
description: "Eine Woche intensiver Entwicklung: Void-Trader wächst von M4 bis M9 und wird im Browser spielbar — mit zahlreichen Bugs, die erst beim echten Playtest auftauchen. Dazu: eine neue Hufpflege-App, ElektraLog-Deployment-Tricks und ein VPN-Ausfall, der mit einem abgelaufenen Server-Zertifikat begann."
isStarred: false
tags: ["Flutter", "Flame", "Dart", "Gamedev", "Void-Trader", "ElektraLog", "Hufpflege", "Bugs", "Deployment", "Docker", "Offline", "Leben"]
---

# Neun Meilensteine, ein Kamera-Bug und vier Kartons Büro

Es war eine Woche, in der Projekte gleichzeitig zu wachsen begannen — und in der das echte Leben zwischendurch freundlich daran erinnerte, dass nicht alles Commits sind.

Die Kurzversion: Void-Trader hat neun Meilensteine hinter sich und läuft jetzt als Web-App im Heimnetz. ElektraLog bekam ein granulares Rollenmodell, einen hartnäckigen PDF-Bug ausgemerzt und eine sauberere Deployment-Pipeline. Eine neue App für Hufpfleger ging von null auf vollständige Grundarchitektur. Der VPN-Server lag tot — Ursache: das Server-Zertifikat selbst. Und das Wochenende brachte ein enttäuschendes Dorffest, gepackte Kartons und einen Interessenten, der abgesagt hat.

---

## Das Leben jenseits des Bildschirms

**Donnerstag** kam die Nachricht, dass der Interessent abgesagt hat. Der Verkauf läuft weiter. Wer schon einmal etwas verkauft hat, kennt den Rhythmus: Man wartet, ein Termin kommt, ein Termin geht. Geduld ist Teil des Prozesses — kein Rückschlag, nur ein offenes Kapitel.

**Samstag** war Dorffest. Es war leider ein Reinfall. Der Andrang blieb sehr überschaubar, und das Fußballturnier fand mit vier statt der geplanten acht Mannschaften statt — mehrere Teams hatten kurzfristig abgesagt. Wenig Atmosphäre, viel Luft nach oben. Es gibt eine Lektion darin: Wenn ein Event auf verbindliche Zusagen angewiesen ist, braucht es frühere Verbindlichkeit oder eingebaute Alternativen. Vier Mannschaften können trotzdem ein gutes Turnier spielen — aber dafür muss man das frühzeitig wissen.

**Sonntag** wurde das Büro aufgeräumt. Alte Unterlagen kamen in Kartons — als Vorbereitung auf einen möglichen Umzug. Noch nichts entschieden, aber der erste Schritt ist getan. Früh anfangen kostet nichts. Es spart Zeit, wenn es ernst wird.

---

## Hufpflege: Von Mockup zu laufender App in einem Tag

Am 29. Juni begann ein neues Projekt: **Hufpflege**, eine Flutter-Android-App für selbstständige Hufpfleger.

Ausgangspunkt war ein leeres GitHub-Repository mit Mockups und einem vollständigen DESIGN.md — Hex-Farben, Typografie-Skala, Spacing-System. Endpunkt des ersten Tages: ein lauffähiges Flutter-Projekt mit vollständiger Datenbankschicht, State Management, Navigation und sieben Screens.

Was entstand:

- **Datenbankschicht** mit `drift` (SQLite ORM): fünf Tabellen — `owners`, `horses`, `treatments`, `treatment_photos`, `appointments`
- **Design System** 1:1 aus den Mockups: Material 3, Nunito Sans, Waldgrün als Primärfarbe, Warmamber als Akzent
- **Sieben Screens**: Dashboard, Kundenliste, Kunden-Detail, Pferdeprofil (drei Tabs), Behandlungseintrag, Terminkalender, Termin-Formular

Zwei Entscheidungen waren von Anfang an klar: **Offline-first** — kein Backend, SQLite auf dem Gerät — und **archivieren statt löschen**, weil die Verlaufsakte eines Tieres nie verloren gehen darf.

Die App wird mit Arbeitshandschuhen und Metallraspeln in der Hand bedient. Das beeinflusst Design-Entscheidungen konkret: 48px-Mindest-Touch-Targets, Off-White statt Weiß (weniger Blendung im Freien), große Akkordeon-Buttons.

Am 30. Juni kam die erste Erweiterungsrunde — aus echtem Nutzer-Feedback:

- **Mehr als Pferde**: Tierart-Feld (Pferd, Pony, Esel, Ziege, Schaf, Sonstiges) — Terminologie in der ganzen App überarbeitet
- **Termine pro Besitzer statt pro Tier**: In der Praxis behandelt ein Hufpfleger alle Tiere eines Kunden auf einmal, nicht ein einzelnes — das Datenmodell wurde entsprechend umgehängt. Eine scheinbar kleine Anforderung, die den zentralen Foreign Key der App verschob.
- **Einnahmen pro Termin**: Anfahrt, Betrag pro Tier, Trinkgeld als eigene Felder
- **Kategorie-Badges A/B/C**: Kunden und Tiere lassen sich als freundlich, neutral oder schwierig einstufen — als kleine farbige Badges in allen Listenansichten sichtbar
- **Zwei Bugfixes**: Verlaufsakte-Einträge ohne Tap-Handler, Fotos-Tab als Platzhalter ohne echte Datenbankanbindung — beide behoben, inklusive einfacher Vollbild-Galerie

Repo: https://github.com/KayBeckmann/hoofcare

---

## ElektraLog: Deploy-Tricks, Rollen und ein hartnäckiger PDF-Bug

### Deployment auf einem Server mit 1,4 GB RAM

Am 29. Juni war ElektraLog nach einem Pull nicht mehr web-buildbar — drei Compile-Fehler blockierten den Release. Alle drei waren klassische Refactor-Fallen:

1. **Fehlende `DashboardScreen`-Klasse** — Ein Refactor hatte alle Dashboard-Widgets in private Klassen aufgeteilt, aber die öffentliche Einstiegsklasse vergessen. Der Router verwies ins Leere.
2. **Fehlender Import in `pdf_service.dart`** — `Pruefprotokoll` wurde verwendet, aber nicht importiert.
3. **Nullable `FontWeight`** — `null` statt `FontWeight.w500` in einem non-nullable Parameter.

Danach folgte der komplette Deployment-Zyklus. Eine Besonderheit: Der Produktionsserver hat ~1,4 GB freien RAM — zu wenig für `flutter build` oder `dart compile`. Alles muss lokal entstehen und dann übertragen werden.

Eine interessante Falle dabei: **Server-MOTD als Deployment-Blocker**. Der Server gibt für jede SSH-Verbindung einen neofetch-Output aus — auch für nicht-interaktive Sessions. SCP interpretiert das als Protokolldaten und bricht mit `Received message too long` ab. Fix: `ssh "cat > /ziel" < lokal` überträgt die Datei durch stdin und umgeht das Problem vollständig.

### Granulares Rollenmodell und Server-Vorrang

Am 30. Juni wurde eine Inbox-Notiz mit Anforderungen zu Rollen und Synchronisation abgearbeitet.

Drei Rollen — Monteur, Projektleiter, Firmenadmin — bekommen jetzt fein abgestufte Rechte. Ein Monteur darf überall neue Standorte und Strukturpunkte anlegen, aber nur dann bearbeiten oder löschen, wenn keine abhängigen Daten daranhängen. Diese Logik sitzt zentral in einem Berechtigungs-Provider — vorher war sie uneinheitlich über mehrere Stellen verteilt.

Beim Offline-Sync galt bisher: der neueste Zeitstempel gewinnt. Das wurde durch eine einfachere Regel ersetzt — **Server gewinnt immer**. Das klingt nach einem Rückschritt, ist aber für eine Handwerker-App mit gelegentlicher Konnektivität tatsächlich robuster: Uhren auf Mobilgeräten laufen nicht immer synchron. Ein vorhersagbares "der Server hat recht" schlägt einen Zeitstempel-Wettlauf.

### Der PDF-Bug in drei Ebenen

Am 1. Juli zeigte sich ein Bug, der aus drei überlagerten Problemen bestand:

**Ebene 1** — `layoutPdf` vs. `sharePdf`: Das `printing`-Paket bietet zwei Wege. `layoutPdf` öffnet den nativen Druckdialog, `sharePdf` erzeugt einen echten Datei-Download (Web) bzw. Share-Sheet (Android). Der bisherige Code nutzte überall `layoutPdf` — auf Web kein Download, auf Android ein leeres Druckfenster.

**Ebene 2** — `Uint8List` als PostgreSQL-Integer-Array: Nach dem Download-Fix öffneten sich PDFs nicht. Dateiinhalt: `{37,80,68,70,...}` — das Dart-`List.toString()`-Format. Ursache: Beim INSERT wurde `Uint8List` ohne explizite Typangabe als postgres-Parameter übergeben. Die postgres-Bibliothek 3.5.x erkennt `Uint8List` als `List<int>` und kodiert es als PostgreSQL-Array-Literal. Fix: `TypedValue(Type.byteArray, pdfBytes)`.

**Ebene 3** — Falscher Docker-Tag: Der Fix wurde korrekt auf den Server übertragen. Aber `docker-compose.yml` nutzte `build:` ohne explizites `image:`-Feld — docker compose generiert dabei intern einen eigenen Tag, der auf das zuletzt lokal gebaute Image zeigt, nicht auf das extern importierte. Fix: `image:` explizit ins `docker-compose.yml` eintragen.

Diagnosemethode für Ebene 2: `file datei.pdf` → `ASCII text with very long lines` → `head -c 30` → `{37,80,...}` → Dart-Array-Notation → postgres-Encoder-Fallback → ungetypter Parameter. Mit diesem Wissen war der Ursprung sofort klar.

Repo: https://github.com/KayBeckmann/ElektraLog

---

## OpenVPN: Wenn das Zertifikat beim Server abläuft

Am 30. Juni war der eigene OpenVPN-Server von keinem Gerät mehr erreichbar. Alle Clients zeigten nur "service destroyed". Der erste Verdacht: ein einzelnes Client-Zertifikat. Die Server-Logs zeigten: abgelaufenes Zertifikat — aber beim Abgleich mit der Konfiguration stellte sich heraus, dass es das **Server-Zertifikat selbst** war. Das erklärte, warum auf einmal niemand mehr reinkam.

Bei der Erneuerung: Das eingebaute "Zertifikat erneuern"-Kommando von Easy-RSA scheiterte an einer simplen Inkompatibilität — das schlanke Docker-Image bringt nur eine abgespeckte `date`-Implementierung mit, die das erwartete Datumsformat nicht versteht. Umweg: Zertifikat widerrufen und neu ausstellen — robuster und funktionierte einwandfrei.

Alle Ablaufdaten sind jetzt zentral dokumentiert. Beim nächsten Mal kein Komplettausfall nötig, um es zu merken.

---

## Void-Trader: Ein Sprint durch neun Meilensteine

Der letzte Blog-Beitrag endete mit M3 — Basis-Bau-System, Handelsmechanik, Kampfsystem. Was seither passiert ist:

### Design System und vollständiges UI (1. Juli)

Bevor M4 begann, bekam Void-Trader seine erste vollständige UI-Ebene. Das "Tactical Cyber-Industrial"-Designsystem aus den Mockups wurde in Flutter übertragen: Primary Cyan `#8aebff`, Secondary Amber `#ffb95f`, Background `#0e1416`, Archivo Narrow für Headlines, JetBrains Mono für Daten.

Die App-Shell hat jetzt glassmorphe Navigation mit `BackdropFilter` über dem laufenden Flame-GameWidget — kostenloser Frosted-Glass-Effekt, weil `BackdropFilter` auch den darunter liegenden Canvas blurred. Wichtig für Tabs: `IndexedStack` statt Standard-`BottomNavigationBar` — Flame darf nicht bei jedem Tabwechsel neu initialisiert werden.

Die 10-Segment-Bars für Shield und Hull sind das bessere Feedback als ein einfacher Balken: Spieler sehen "3 Segmente Schaden" statt einen Prozentsatz zu interpretieren.

### M4: Fraktionen und Quests

Sechs Fraktionen (Konsortium, Gilde, Piraten, Forschungs-Kult, Freie Kolonien, Wächter) mit gegenseitigen Reputations-Effekten: Wer bei den Piraten Ruf gewinnt, verliert automatisch bei Konsortium und Wächtern. Diese Logik steckt in einer `counterRepFactors`-Map — eine klassische Adjazenz-Map, nur auf Sozial-Systeme angewendet.

Das Quest-System generiert beim Andocken 2–4 Aufträge aus sieben Vorlagen: Lieferung, Jagd, Erkundung, Schmuggel, Patrouille, Artefakt. Kills zählen direkt gegen aktive Jagd-Quests. Belohnungen fließen in Credits und Ruf.

### M5: Skill-Tree und Charakterentwicklung

12 Skills in drei Säulen: Pilot, Händler, Kommandant. XP aus Kills und Trades, Skill-Punkte aus Levelups. Das Säulen-Konzept erlaubt organische Spezialisierung ohne Zwang — wer kämpft, entwickelt den Pilot-Zweig, wer handelt den Händler-Zweig, beide Spielstile fühlen sich richtig an.

### M6: Tech-Tree lebt

Der Tech-Tree war ein reines Dekorationsobjekt — Buttons existierten, aber nichts änderte sich. Jetzt gibt es einen echten `TechState`, Forschungspunkte aus Crew-Arbeit (10 FP pro Forscher pro Spieltag) und echte Unlock-Logik mit Prerequisite-Ketten. Erst Refinery und Logistik freischalten, dann Shields, dann Weapons, dann Fleet AI — das ist eine Entscheidungsstruktur, kein linearer Fortschritt.

### M7: Eigene Infrastruktur

Außenposten, Speedways, Orbital-Stationen und Sprungtore lassen sich jetzt im eigenen Namen bauen. Jede Anlage hat Bauzeiten, Kosten, tägliche Einnahmen und ein 5%-Piratenangriffs-Risiko pro Spieltag. Das passive Einnahmen-Dreieck ist damit geschlossen: Flotte + Basis + Infrastruktur — das Pattern hinter Spielen wie Elite Dangerous und X4.

### M8: Flotten-System und Permadeath

Drei Schiffsklassen — Kämpfer, Frachter, Bergbauschiff — kaufen, Missionen zuweisen, Tageseinnahmen kassieren. Schiffe auf Patrouille haben 8% Tages-Chance auf Kampfschaden — statistisch etwa 40 Spieltage bis zur Reparatur. Der Fleet-AI-Core-Tech ist Voraussetzung für den Flottenkauf: M6 und M8 sind als echte Spielmechanik verknüpft.

M8b brachte das Permadeath-System: Ein Klon-Pool (start: 1, max: 3), Respawn-Bindung an eine Klon-Klinik, Game-Over-Screen wenn der Pool leer ist. Die gesamte Logik: ~80 Zeilen reines Dart. Permadeath ist opt-in — nicht jeder Spieler will die Bestrafung.

### M9: Speichern und Laden

Ohne Speichersystem ist jede Spielsession ein Neustart. Das JSON-Snapshot-Format in `<documents>/void_trader_save.json` serialisiert alle Domain-Objekte: Credits, Ruf aller Fraktionen, Inventar, Tech-Tree-Level, Flotte, Basis mit Gebäuden und Crew. Das Grid wird beim Laden neu rekonstruiert statt serialisiert — das Gitter ist vollständig aus der Gebäudeliste ableitbar.

Autosave greift täglich (Tageswechsel-Vergleich im Game-Tick) und nach jedem Docking. Spieler sehen nie einen Speicher-Dialog.

Interessante Entscheidung: `restoreBase()` statt `foundBase()` beim Laden — zwei explizite Methoden statt einer mit Boolean-Flag. `foundBase()` prüft Credits und erzeugt eine neue Basis; `restoreBase()` tut das nicht.

### Web Deployment: 30 Minuten bis spielbar

`flutter build web --release` lokal, Web-Dateien via SSH-tar-Pipe auf den Server, nginx-Container gestartet mit Volume-Mount. `curl localhost:8090` → HTTP 200. Das Ergebnis ist ein statischer Ordner, den jeder nginx ausliefern kann.

GitHub: https://github.com/KayBeckmann/Void-Trader

---

## Die Bugs: Vier aufeinanderfolgende Fixes, von denen drei am falschen Symptom lagen

Der erste echte Playtest auf Mobile und im Browser brachte eine Reihe an Bugs — manche interessant, einer besonders lehrreich.

### NavBar-Overflow, Joystick und Schiff-Drift

Mit M5 und M7 hatte die App sieben Bottom-Nav-Tabs. Sieben Tabs × ~56 px > 360 px Bildschirmbreite — die gesamte Leiste wurde nicht gerendert. Kein Fehler, keine Warnung — einfach unsichtbar. Fix: zwei kombinierte Screens mit `DefaultTabController`.

Der Joystick lag mit `bottom: 48` direkt hinter der NavBar — NavBar-Taps steuerten das Schiff. Fix: `bottom: 150`.

Die Physik war frame-rate-abhängig: `applyJoystick()` addierte Impulse ohne `dt`-Skalierung. Die elegante Formel für dt-normierten multiplikativen Decay: `math.pow(_drag, dt * 60)` — gleiche Endgeschwindigkeit bei 30 fps und 120 fps.

### Der Kamera-Bug, den drei Fixes nicht lösten

Die Sonne klebte in der oberen linken Bildschirmecke. Das Schiff bewegte sich, wurde aber nie zentriert.

Versuch 1: Zoom anpassen. Keine Wirkung.
Versuch 2: Kamera-Anker setzen. Keine Wirkung — `Anchor.center` war in Flame 1.37 ohnehin der Default. Der "Fix" erzeugte eine byte-identische Binary.
Versuch 3: Startposition verschieben. Keine Wirkung.

Die richtige Frage wurde zu spät gestellt: *Rendert die Kamera überhaupt diese Objekte?*

In Flame 1.37 rendert die `CameraComponent` ausschließlich ihre `camera.world`. Alle Welt-Objekte hingen aber per `game.add(...)` am FlameGame-Root — Root-Komponenten werden im Screen-Space gezeichnet und ignorieren Kamera-Position und Zoom vollständig. Die Sonne bei Welt-Koordinate (0,0) landete fix auf Bildschirm-Pixel (0,0) — oben links in der Ecke.

Fix: alle `add(...)` → `world.add(...)`. Ein Zeiler. Löste alle drei vorangegangenen "Bugs" auf einmal.

**Mentalmodell für Flame:** `game.add` ≠ `world.add`. Alles was die Kamera bewegen soll gehört in `world`. Alles was fix am Bildschirmrand klebt (HUD, Joystick) gehört auf `viewport`.

### Der falsche Entrypoint

Der abschließende Bug war der eleganteste. Ein Nutzer meldete: "Ich sehe nur das Weltall mit Sonne, Planeten und Schiffen. Sonst gar nichts."

Alle Flame-Elemente waren vorhanden — kein einziges Flutter-Overlay. `main.dart` renderte ein nacktes `GameWidget(game: VoidTraderGame())` statt der kompletten `VoidTraderApp`-Shell.

Der Beweis: Nach dem Fix sprang `main.dart.js` von 1,64 MB auf 3,24 MB. Der Tree-Shaker hatte die gesamte Shell — Base-, Fleet-, Research-, Skill-, Infra-, Map-, Settings-Screen — entfernt, weil `VoidTraderApp` von nirgends referenziert war. Toter Code aus Sicht des Compilers, obwohl er das halbe Spiel ausmachte.

Die präzise Nutzer-Beschreibung war mehr wert als jeder weitere Code-Blick an der falschen Stelle.

---

## Schlussgedanke

Neun Meilensteine, ein abgelaufenes VPN-Zertifikat, eine neue App, drei Deployment-Bugs, ein Dorffest mit vier Mannschaften und vier Kartons Büro.

Das Gemeinsame hinter allen technischen Problemen dieser Woche: Die hartnäckigsten Bugs lagen nie dort, wo man zuerst geschaut hat. Der PDF-Bug hatte drei Ebenen. Der Kamera-Bug lag an der Architektur, nicht an Kamera-Parametern. Der größte Showstopper stand in der kürzesten Datei des Projekts.

Void-Trader ist jetzt spielbar im Browser. ElektraLog hat ein robusteres Rollenmodell und ein saubereres Deployment. Hufpflege ist in einer Woche von Konzept zu lauffähiger App geworden. Das ist der Stand — weiter geht's.

---

# Kaffee

Über einen
[Kaffee](https://www.buymeacoffee.com/snuppedelua)
würde ich mich auf jeden Fall freuen.
