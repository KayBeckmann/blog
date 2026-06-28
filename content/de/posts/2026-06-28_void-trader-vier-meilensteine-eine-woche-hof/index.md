---
title: "Vier Meilensteine, ein Maklertermin und Rührei vom Grill"
date: "2026-06-28T00:00:00Z"
draft: false
description: "Eine Woche in zwei Hälften: Hof, Hitze und Stromausfall auf der einen Seite — und auf der anderen ein Weltraum-Handelsspiel, das von Null bis zu spielbarer Basis-Ökonomie in drei Tagen wächst."
isStarred: false
tags: ["Flutter", "Flame", "Dart", "Gamedev", "Void-Trader", "Procedural", "KI", "Wirtschaft", "Offline", "Leben"]
---

# Vier Meilensteine, ein Maklertermin und Rührei vom Grill

Es gibt Wochen, die laufen ausschließlich in einem Modus. Diese Woche nicht.

Die erste Hälfte: Mistgabel statt Tastatur. Ställe, Außenbereiche, Haus. Maklerbesuch. Hitze. Ein Stromausfall, der am Sonntag ohne Vorwarnung zwei Stunden lang die gesamte Infrastruktur abwürgt.

Die zweite Hälfte: Vier Flutter-Meilensteine für ein Weltraum-Handelsspiel — von der Roadmap zum spielbaren Basis-Bau-System. In drei Tagen, mit Commits die sich inzwischen auf fast zwanzig aufsummieren.

Beides gehört dazu. Los geht's.

---

## Die andere Seite des Bildschirms

Montag und Dienstag standen vollständig im Zeichen von körperlicher Arbeit. Ställe ausgemistet, Außenbereiche auf Vordermann gebracht, durchs Haus gewerkelt. Die Sorte Arbeit, die keine Commit-History hinterlässt, aber genauso erschöpft — dafür riecht alles danach wieder ordentlich.

Mittwoch kam der Makler mit einem Interessenten. Das Gespräch lief gut. Jetzt heißt es warten — und auf eine Kaufentscheidung zu warten ist eine eigene Disziplin. Man hat alles getan was möglich war, und dann liegt die Entscheidung bei jemand anderem.

Donnerstag und Freitag waren privat verplant. IT-Projekte bekamen kaum Aufmerksamkeit — und das war in Ordnung so.

Samstag: Temperaturen, bei denen Körper und Kopf gemeinsam streiken. Kein sinnvoller Gedanke, keine sinnvolle Zeile Code. Manchmal ist Nichtstun die einzig vernünftige Entscheidung, und man sollte aufhören, sich dafür zu rechtfertigen.

Sonntag: Fast zwei Stunden kein Strom. Überraschend entspannt — kein Bildschirm, keine Ablenkung. Stattdessen Rührei vom Grill. Grillrührei schmeckt sowieso besser. Und wenn das Netz ausfällt, findet man immer Wege.

**Erkenntnis der Woche:** Offline-Zeit ist keine verlorene Zeit. Eine Woche ohne IT-Fortschritt fühlt sich seltsam an, aber Hof, Haus und Privates sind kein Rückschritt.

---

## Void Trader: Von der Konzept-Notiz zum spielbaren Wirtschaftssystem

Parallel zu allem anderen entstand in den letzten Tagen — verteilt über Donnerstag bis Sonntag abend, in den Stunden zwischen den Verpflichtungen — ein neues Spielprojekt: **Void Trader**.

Flutter, Flame, ein Handelsspiel im Weltraum. Die Prämisse: Spieler steuert ein Schiff durch eine Galaxis aus prozedural generierten Systemen, handelt Waren, kämpft gegen Piraten, baut eine Heimatbasis auf einem beanspruchten Planeten.

Repo: https://github.com/KayBeckmann/Void-Trader

Was in dieser Woche entstand, erkläre ich Meilenstein für Meilenstein.

---

### M0: Vom Brainstorm zum fliegenden Schiff

Ausgangspunkt waren Konzeptnotizen ohne Struktur. Ideen, Tech-Stack-Überlegungen, Datenmodell-Fragmente, ein paar Fraktions-Namen.

Der erste Schritt: alle Notizen durcharbeiten und daraus eine vollständige **Roadmap mit 11 Meilensteinen** bauen — inklusive Abhängigkeitsgraph, Risiko-Register und MVP-Grenze. Damit war klar, was in welcher Reihenfolge gebaut werden muss und wo Abhängigkeiten zwischen Features liegen.

Gleichzeitig wurden alle offenen Designfragen der Konzeptphase beantwortet:

- **Storage: Isar v3** statt auf v4 zu warten (v4 war zu dem Zeitpunkt noch nicht stable genug)
- **Offline-Ticks**: Forschung und Klon-Reife laufen Timestamp-basiert — wenn das Spiel drei Stunden zu war, wird das Delta beim nächsten Start aufgelöst
- **Schwierigkeitsgrade**: variieren Schadens-Multiplikator und NPC-Aggressivität, keine eigene Spielmechanik

Dann: **5 handgefertigte Kernsysteme** für die Galaxis.

| System | Thema |
|--------|-------|
| Helion | Konzern-Megahub — Konsortium-HQ |
| Vega | Neutraler Handelsknotenpunkt der Gilde |
| Auros | Schwerindustrie & Klassenkonflikt |
| Nexara | Alien-Mysterium & Forschungsbasis |
| Skarrath | Frontier-Station — Gateway zum Outer Space |

Dieser Schritt war bewusst Handarbeit. Prozedural generierte Systeme bekommen später per Seed-basiertem Generator ihren Inhalt. Aber die fünf Kern-Systeme sollen Charakter haben, den kein Algorithmus replizieren kann. Das Sprungtor-Netz ist nicht nur Ästhetik — es ist Leveldesign. Nexara ist nur über Vega erreichbar. Skarrath hat kein Tor in den Outer Space (FTL-Antrieb ist Pflicht).

Am Ende des ersten Tages: `flutter run`. Ein Schiff bewegt sich auf einem schwarzen Bildschirm. Flame-Physics, Touch-Joystick, Kamera-Follow, Keyboard-Steuerung. M0 ist abgeschlossen.

---

### M1: Sternsysteme, Piraten und eine Mini-Map

Vier Commits später hatte Void Trader ein spielbares Weltraum-System.

**Was entstand:**

- Alle fünf Sternsysteme als JSON-Datei mit Planeten, Asteroidengürteln und Sprungtoren. Koordinaten, Typ-Tags, Fraktions-Zugehörigkeit — alles datengetrieben.
- **Prozedurale Systemgenerierung** für Frontier-Systeme: Lehmer-Kongruenzgenerator (LCG) mit Seed aus der System-ID produziert deterministisch gleiche Namen und Layouts. Kein `Random()` ohne Seed.
- **Rendering**: Sonne mit Pulse-Animation, Planeten mit typabhängiger Farbe und Atmosphären-Halo, Asteroidenfelder mit ~60 deterministisch platzierten Steinen, Sprungtore als rotierende Diamant-Shapes.
- **Sprungtor-Mechanik**: F-Taste oder Tap öffnet ein Overlay mit Zielauswahl und Fade-Animation.
- **Andockscreen**: Sechs Stations-Dienste (Markt, Scan, Reparatur, Werft, Mission, Fracht) als Grid — je nach Planetentyp aktiv oder gesperrt.

Dann das Kampfsystem. **Piraten-KI als State Machine:**

```
Patrol → Chase (< 450 units) → Attack (< 280 units) → Flee (< 20% Hull)
```

Vier Zustände. Ein `switch`-Statement. Dart's exhaustive pattern matching macht solche State Machines angenehm zu schreiben — der Compiler zwingt zur Vollständigkeit.

Feinde feuern alle 0,65 Sekunden, 12 Schaden pro Treffer. Spieler feuert alle 0,18 Sekunden, 18 Schaden. Schild (100 HP) regeneriert nach 4 Sekunden Pause. Hülle (200 HP) kommt nur über Reparatur zurück. Treffer erzeugen 16 Partikel mit Fading-Alpha und Drag-Physik; Spielertreffer lösen Camera-Shake aus.

Die **Mini-Map** ist ein `CustomPainter`-Widget oben rechts in der Flutter-UI — völlig außerhalb der Flame-Engine. Spielerposition kommt via `ValueNotifier<Vector2>` rüber. Das ist die elegante Lösung für das Flame+Flutter-Hybrid-Problem: Flame rendert die Spielwelt, Flutter-Widgets liegen als Stack darüber, und der `ValueNotifier` ist die Brücke dazwischen. Kein Riverpod, kein Overhead.

**Technische Fußnote:** `Vector2 +=` in Dart erstellt eine neue Instanz. Auf einem `final`-Feld führt das zu einem LateInitializationError. Fix: `_velocity.add(delta)` statt `_velocity += delta`. Ein Dart-Eigenheit, die man einmal sehen und dann nie wieder vergessen muss.

7 Commits, gepusht.

---

### M2: Eine Spielwirtschaft in 200 Zeilen Dart

M2 brachte das Handelssystem.

**20 Waren in 6 Kategorien** — alle als JSON, ohne eine hardcodierte Zahl im Code:

- Rohstoffe: Eisenerz, Wassereis, Kristallsplitter, Organik
- Industriegüter: Module, Munition, Energiezellen, Reparatursätze
- Luxuswaren
- Illegale Waren: Verbotene Biotech, Waffenschmuggel, Neu-Stoff
- Forschungsgüter: Datenkristalle bis Alien-Techfragmente
- Verbrauchsgüter: Treibstoff, Nahrung, Klon-Basis

**Das Markt-Modell** basiert auf einer einzigen Preisformel:

```
preis = basePrice × clamp(1 + demand − supply, 0.3, 3.5) × eventMultiplier
```

Drei Faktoren decken 99% aller Marktszenarien ab. Produzenten restocken täglich 15% ihres Lagers, Konsumenten verbrauchen 10%. Alle zwei Minuten Echtzeit läuft ein "Tag" Spielzeit durch — mit 2% Wahrscheinlichkeit spawnt ein Markt-Event: Krieg, Epidemie, Tor-Sperrung, Boom oder Engpass. Der `eventMultiplier` bewegt sich zwischen 0,7× und 2,8×.

**Systemspezifische Märkte** werden deterministisch aus `system.id.hashCode` generiert. Mining-Planeten produzieren Roherz und kaufen Module. Handelswelten kaufen alles und haben hohe Lagerbestände. Illegale Waren sind in Kernwelten gesperrt — außer in Piratensystemen, wo sie völlig normal gehandelt werden. Das schafft natürliche Handelsmotivation: risikoreicher Lauf durch Kernwelten für 3× Marge.

**Schmuggel**: Wer illegale Waren transportiert, kann bei Andocken gescannt werden (60% Basis-Chance, ×0.3 mit Tarnladung). Erwischt: 3× Warenwert als Strafe, Konfiszierung, -15 Ruf beim System. Piratensysteme scannen nie.

**Markt-UI**: Zwei-Tab-Screen (Kaufen / Verkaufen), Kategorie-Farbpunkt, Live-Preis mit Event-Warnung, Mengenregler. Alles via `Navigator.push` als Flutter-Route — kein Overlay auf dem GameWidget, kein State-Leak.

5 Commits, gepusht.

---

### M3: Eine Heimatbasis auf einem fremden Planeten

M3 brachte das Basis-Bau-System.

Der Ablauf aus Spielerperspektive: Planeten scannen → Claim gegen 500 Credits und Ruf ≥ -30 → auf dem Planeten landen → Gebäude auf einem 12×10-Grid platzieren.

**12 Gebäude in drei Tiers** — wieder als JSON, alle mit Energiebilanz, Baukosten, Produktionsrate:

| Tier | Gebäude |
|------|---------|
| T1 | Solarpanel (+10E), Bohrturm (-4E), Wasserextraktor (-3E), Schmelze (-8E, 2×2), Munitionsfabrik (-5E), Lagerhaus (-1E, 2×2), Verteidigungsturm (-3E), Reparaturbucht (-4E) |
| T2/T3 | Forschungszentrum, Werft, Kommandozentrum, Klon-Kammer (als Stubs — existieren im JSON, erscheinen aber nicht im Bau-Menü bis M6/M8) |

**Das Energie-System** ist die zentrale Spielmechanik von M3. Produzenten werden immer aktiviert; Konsumenten werden in Reihenfolge der Platzierung aktiviert, solange Energie vorhanden ist. Wer zuerst gebaut wurde, bekommt zuerst Strom. Das erzeugt ohne jeden Extra-Parameter eine klare Spieler-Entscheidung: Erst Solarpanele bauen, dann Verbraucher platzieren. Oder bewusst riskieren, dass ältere Gebäude deaktiviert werden.

**Crew**: Vier Rollen (Arbeiter, Forscher, Pilot, Wache) mit Tageslohn (80–200 Credits/Tag). Arbeiter geben +20% Produktionsbonus pro zugewiesenem Gebäude. Lohnkosten werden täglich automatisch abgezogen.

**Marauder-Events**: In der Nachtphase gibt es eine 12%-Wahrscheinlichkeit pro Spieltag. Angriffsstärke 1–5, Verteidigungstürme fangen `count × 1.5` Einheiten ab. Jede durchbrechende Einheit: 15 Hull-Schaden. Eine Basis ohne Türme wird statistisch alle 8–9 Spieltage angegriffen — selten genug, um die frühe Spielphase nicht zu frustrieren, häufig genug, um Türme sinnvoll zu machen.

**Planeten-Scan-UI**: 2 Sekunden Scan-Animation, dann Anzeige von Rohstoff-Vorkommen, Klima, Tektonik, Strahlung, Marauder-Bedrohung und politischer Lage.

**Technische Randnotiz:** Dart erlaubt keine Getter mit Parametern. `get cell(int x, int y)` ist kein gültiger Dart-Code. Die Lösung ist trivial — `cellAt(int x, int y)` als Methode — aber man stolpert genau einmal darüber.

5 Commits, gepusht.

---

## Was die vier Meilensteine verbindet

Void Trader ist in dieser Woche von einer Konzept-Datei zu einem Spiel mit spielbarer Wirtschaft, Kampfsystem, Handelsmechanik und Basis-Bau geworden.

Das Gemeinsame hinter allen vier Meilensteinen ist ein Prinzip, das sich bewährt hat: **Datengetrieben von Anfang an.** Waren, Gebäude, Sternsysteme, Markt-Events — alles steckt in JSON-Dateien. Balance-Änderungen brauchen keinen Code-Commit. Neue Waren kommen ohne Klassenänderung hinzu. Stub-Gebäude existieren im Datensatz und erscheinen erst im UI wenn das entsprechende Feature fertig ist.

Das ist keine Philosophie — das ist pragmatisches Handwerk. Wenn in drei Wochen der erste Playtest zeigt, dass Marauder zu stark oder zu schwach sind, ändere ich zwei Zahlen in einer JSON-Datei und bin fertig.

---

## Schlussgedanke

Vier Meilensteine in drei Tagen. Eine Hofwoche ohne eine sinnvolle Zeile Code. Rührei vom Grill.

Die Woche hat gezeigt, dass beides möglich ist — und beides seinen Platz hat. Offline-Zeit ist keine Strafe. Manchmal kommt man mit klarerem Kopf zurück, und dann laufen drei Tage Implementierung überraschend rund.

Void Trader ist jetzt ein echtes Spiel. Noch ein Rohling, noch nicht schön, noch lange nicht fertig — aber man kann es fliegen, kämpfen, handeln und eine Basis bauen. Das ist mehr als die meisten Projekte je erreichen.

Die Roadmap hat noch sieben Meilensteine vor sich.

---

# Kaffee

Über einen
[Kaffee](https://www.buymeacoffee.com/snuppedelua)
würde ich mich auf jeden Fall freuen.
