---
title: "Ein ERP, ein Lektorat, eine tote Sprache und ein Android-Crash der schweigt"
date: "2026-06-22T00:00:00Z"
draft: false
description: "Zwei Wochen in Kurzfassung: saasERP durchläuft sechs Meilensteine in sieben Tagen, ein stiller Android-Crash hat drei Ursachen, ein Roman bekommt 18 Korrekturen — und nebenbei beginnt ein Schwedisch-Ordner im Vault."
isStarred: false
tags: ["Flutter", "Dart", "Android", "saasERP", "ElektraLog", "ERP", "SaaS", "Schreiben", "Lektorat", "Schwedisch"]
---

# Ein ERP, ein Lektorat, eine tote Sprache und ein Android-Crash der schweigt 

Manchmal läuft eine Woche einfach rund. Manchmal liefert sie so viel auf einmal, dass man am Ende kaum glaubt, dass das tatsächlich alles in zwei Wochen passiert ist.

Was zwischen dem 9. und 21. Juni entstand: Ein ERP-System, das von der Idee bis zum sechsten Meilenstein in sieben Tagen lief. Ein Android-Bug, der drei verschiedene Ursachen hatte und keine davon verriet. Ein Roman, der 18 Fehler trug, die niemand sehen konnte, bis jemand genau hinschaute. Und dazwischen: Schwedisch, Fantasie-Heftromane, zwei Sachbücher und ein Lernordner, der einfach wachsen musste.

Los geht's.

---

## saasERP: Von der leeren Repo-Seite zum fertigen ERP-System in sieben Tagen

Ich baue gerade **saasERP** — ein mandantenfähiges Mini-ERP für Handwerksbetriebe, das sowohl als Managed-SaaS als auch zur Selbstinstallation funktioniert. Flutter-Frontend, Dart Frog-Backend, PostgreSQL, Docker. Die Idee war schon länger da. Die Umsetzung startete Mitte Juni — und was dann passierte, hat mich selbst überrascht.

### M1: Das Fundament

Am ersten Tag entstand das Grundgerüst: Monorepo-Struktur mit `shared/`, `backend/` und `app_user/`, eine Multi-Tenant-Architektur wo jeder Mandant seine eigenen Daten hat und kein anderer Mandant je etwas davon sieht, ein Login-Flow mit JWT, eine CI-Pipeline auf GitHub Actions, Tenant-Auswahl für Nutzer mit mehreren Zugängen und Whitelabel-Theming (jeder Mandant kann eine eigene Primärfarbe setzen).

Der interessanteste Baustein in M1 war die **Envelope-Encryption**. Jeder Mandant bekommt bei der Registrierung einen zufälligen 256-Bit Data Encryption Key (DEK), der mit einem globalen Master-Key verschlüsselt und in der Datenbank abgelegt wird. Zum Zeitpunkt von M1 gab es noch keine fachlichen Daten, die verschlüsselt werden mussten — aber die Infrastruktur stand bereit, sodass ab M2 jedes Feld von Anfang an verschlüsselungsfähig designt werden konnte, statt es nachzurüsten.

Warum Envelope-Encryption statt einfach alle Felder direkt mit dem Master-Key verschlüsseln? Weil ein einzelner Mandant so isoliert neu verschlüsselt werden kann: Key-Rotation betrifft einen DEK, nicht alle Mandanten gleichzeitig.

M1 endete mit einem kompletten Neubau des Docker-Stacks, einem end-to-end Test und vier Flutter-Zielplattformen: Web, Android, Linux, Windows. Fünf Tage nach der leeren Repo-Seite.

### M2: Sieben Tage, ein ganzes ERP-Modul

Dann M2 — und da wurde es intensiv.

In drei Tagen entstand der vollständige ERP-Kern:

**Stammdaten:** Kunden, Kontakte, Artikel, Produkte mit Preiskomponenten, Preisimport aus CSV.

**Beleg-Workflow:** Angebote → Aufträge → Rechnungen → Mahnwesen. Jedes Stadium hat seinen eigenen Status-Workflow. Ein Angebot kann freigegeben, abgelehnt oder in einen Auftrag umgewandelt werden. Eine Rechnung kann als Entwurf leben, versendet, bezahlt oder storniert werden.

Zwei Designentscheidungen haben sich dabei durchgezogen, die ich beim nächsten ERP-Projekt sofort wieder so treffen würde:

**Preise als Schnappschüsse.** Wenn ich ein Angebot anlege, werden `unit_price` und `vat_rate` direkt in der Angebotsposition gespeichert — nicht als Referenz auf den aktuellen Artikelpreis. Das bedeutet: Wenn ich morgen den Artikelpreis ändere, bleibt das gestrige Angebot exakt so erhalten wie es war. Klingt trivial, ist es aber nicht — viele selbst gebastelte ERP-Systeme leben ohne diese Regel und fragen sich dann, warum Altdaten nicht mehr stimmen.

**404 statt 403 für fremde Mandanten-Ressourcen.** Wenn Mandant A versucht, auf eine Rechnung von Mandant B zuzugreifen, bekommt er eine 404, keine 403. Der Unterschied: Eine 403 bestätigt, dass die Ressource existiert. Eine 404 verrät gar nichts. Bei Multi-Tenant-Systemen ist das kein Stilmittel, sondern ein Sicherheitsprinzip.

**Sonstige M2-Bausteine:** Lagerverwaltung, Bestellwesen, Stundenerfassung (Zeiteinträge mit Auswertung), Teilrechnungen und Wartungsverträge mit eigenem Kundenportal.

Ja, Kundenportal. Kunden bekommen einen eigenen Einladungslink, können sich einloggen, ihre Angebote freigeben oder ablehnen, Rechnungen einsehen und PDF-Downloads abrufen — und seit M2b können sie auch Dokumente hochladen (Fotos, Pläne, Vollmachten).

### Dokumenten-Upload: BYTEA statt S3

Bei letzterem gab es eine Entscheidung, die ich kurz erklären möchte: Die Dateien landen als BYTEA-Spalte direkt in PostgreSQL, nicht in einem S3-kompatiblen Objektspeicher.

Für diesen Projekttyp (ein Handwerksbetrieb lädt gelegentlich Fotos und PDFs hoch — typischerweise wenige MB) ist ein externer Objektspeicher unnötige Infrastruktur-Komplexität. PostgreSQL kann das problemlos, und Backups decken die Dateien automatisch mit ab. Wer später auf S3 umstellt, braucht eine Migrationsstrategie — wer von Anfang an mit externem Storage startet, braucht ein laufendes S3-System für die ersten fünf Uploads.

Zusatz: Die gesamte saasERP-API ist reines JSON. Statt für den Datei-Upload als einzige Ausnahme Multipart-Parsing einzuführen, wird der Dateiinhalt Base64-kodiert im JSON-Body übertragen. Der ~33%-Overhead von Base64 ist für diese Dateigrößen vernachlässigbar — eine einheitliche API-Konvention ist mehr wert.

### M3–M6: Abo-Modell, Zahlungsabwicklung, Whitelabel, E-Rechnung

M3 brachte das Abo-Modell für SaaS-Betrieb: Laufzeiten, Anzahlungen, Vertragstrafen — mit einer Formel, die ich schon im letzten Blog-Post erwähnt hatte und die ich trotzdem nochmal aufschreiben möchte, weil sie so schön simpel ist:

```
Strafe = maximale Strafe × (Restlaufzeit / Laufzeit)
```

Wer früh kündigt, zahlt mehr. Wer kurz vor Vertragsende kündigt, zahlt fast nichts. Linear, nachvollziehbar, fair — rechtliche Zulässigkeit in AGB gegenüber Geschäftskunden noch zu prüfen.

M4 legte das Datenmodell für Zahlungsabwicklung. M5 und M6 brachten Onboarding, Support-Kanal, Monitoring, Backup-Strategie, Benutzerhandbuch und — das Highlight — **E-Rechnung nach ZUGFeRD 2.1**.

### ZUGFeRD: Maschinenlesbare Rechnungen nach EN 16931

ZUGFeRD ist ein deutsches Format für elektronische Rechnungen: ein XML nach dem europäischen Standard EN 16931, Factur-X Basic Profile, XRechnung-kompatibel. Buchhaltungssoftware wie DATEV oder Lexoffice kann dieses XML direkt importieren.

Ein paar nicht-offensichtliche Stellen bei der Implementierung:

- **schemeID für die Steuernummer**: Fängt die ID mit zwei Großbuchstaben an (z.B. `DE123456789`)? Dann `VA` (VAT). Sonst `FC` (Fiscal Number). Kein komplexes Parsing, reines Präfix-Matching.
- **UN/ECE Unit Codes**: Deutsche Freitext-Einheiten müssen auf standardisierte Codes abgebildet werden. `h → HUR`, `m² → MTK`, Stück → `C62`. Unbekannte Einheiten defaulten auf `C62`.
- **Kein PDF/A-3b** (bewusst): Ein echtes ZUGFeRD-PDF wäre ein PDF/A-3b-Dokument mit eingebettetem XML. Das erfordert eingebettete Fonts, ein ICC-Farbprofil und eigene XMP-Metadaten. Das Komplexitäts-Nutzen-Verhältnis war zu ungünstig für jetzt. Das XML als eigenständiger Download ist von Buchhaltungssoftware direkt importierbar — das reicht.

### Abschluss: Benutzerverwaltung und GitHub Pages

Zum Schluss: Mitarbeiterverwaltung. Owner können Mitarbeiter einladen, Rollen vergeben, Zugänge entziehen. Jeder Nutzer kann sein Passwort ändern.

Kleine, aber lehrreiche technische Eigenheit dabei: `result.affectedRows` im `postgres`-Dart-Package ist für DELETE-Statements unzuverlässig — es liefert 0 auch bei erfolgreichem Löschen. Die zuverlässige Alternative: `RETURNING <col>` im SQL und `result.isNotEmpty` in Dart. Das Projekt nutzt dieses Muster jetzt konsequent überall.

Und der letzte Schritt: Die vorhandenen Docs-Dateien in `docs/` sind jetzt per GitHub Pages öffentlich zugänglich — mit README, Dokumentationsindex und einem GitHub Actions Workflow, der bei jedem Push auf `master` automatisch deployt.

→ Repo: https://github.com/KayBeckmann/saasERP

---

## ElektraLog: Der Android-Crash der nichts sagt

Parallel zu saasERP lief ElektraLog weiter — und hatte seinen eigenen dramatischen Moment.

**Die Situation:** Nach einem regulären Android-Build startet die App kurz und schließt sich sofort wieder. Auf allen Testgeräten. Ohne Fehlermeldung im normalen Log. Kein Exception-Stack, kein Hinweis.

**Die Ursache** war ein Zusammenspiel zweier Konfigurationsflags, das in der Flutter-Dokumentation nicht dokumentiert ist:

1. Ich hatte `id("kotlin-android")` aus dem Gradle-Build entfernt (empfohlener Flutter-Migrationspfad).
2. Flutter hatte gleichzeitig automatisch `android.builtInKotlin=false` gesetzt, weil zwei Drittanbieter-Plugins (`shared_preferences_android`, `url_launcher_android`) noch den alten Kotlin-Gradle-Plugin-Ansatz verwenden.

Ergebnis: kein Kotlin-Compiler mehr im Build → Kotlin-Runtime fehlt im APK → sofortiger stiller Crash.

Die Lösung war denkbar einfach: `id("kotlin-android")` wieder eintragen, bis die betroffenen Plugins migriert sind. Der Weg dahin war es nicht.

**Was in derselben Session noch behoben wurde:**

- **Multi-User-Sync** mit `autoSyncProvider` — ein Riverpod-Provider, der alle 60 Sekunden im Company-Modus einen Pull auslöst. Startet nach dem Login, stoppt nach dem Logout. Kein manueller Timer, kein `dispose()` von Hand.
- **FutureProvider-Caching**: Gecachte Riverpod-Provider werden nicht automatisch neu geladen, wenn sich SharedPreferences ändern. Nach dem Login müssen `ref.invalidate()` Aufrufe explizit passieren — kein automatisches Refetching.
- **RBAC**: Nur Admins dürfen Kunden und Standorte anlegen. Prüfer sehen alles, dürfen aber nichts an der Strukturebene ändern. Implementiert als UI-Entscheidung: Buttons ausblenden, FABs auf `null` setzen, Checks direkt im `build()`.

Die Lehre aus dem Kotlin-Crash: Ein stiller Crash ist die schlimmste Art von Fehler — er gibt keine Information. Der Weg zum Fix beginnt mit der Frage, was sich zuletzt geändert hat, nicht mit der Frage, was der Fehler bedeutet. Denn der Fehler sagt gar nichts.

→ Repo: https://github.com/KayBeckmann/ElektraLog

---

## Magier Band 1: Was ein Lektorat eigentlich tut

Nebenstrang mit Gewicht: Der erste Band der "Der Magier aus dem All"-Reihe bekam ein systematisches Lektorat.

Ein Lektorat ist keine Rechtschreibprüfung. Es ist der Versuch, den Text so zu lesen, wie ihn ein Leser liest — der nichts weiß, sich nichts gemerkt hat und kein Autoren-Wohlwollen mitbringt. Mit diesem Blick entstanden 18 konkrete Findings in 13 Dateien.

Ein Beispiel für die kritischste Kategorie: Eine Figur stirbt eindeutig in einem Kapitel. Kapitel später taucht sie lebend auf — ohne Erklärung. Das ist kein Stilmittel, das ist ein Fehler. Ein anderes: Ein Protagonist kennt Gedanken über Menschen, die er noch nie getroffen hat. Und: Zeitangaben zwischen drei aufeinanderfolgenden Kapiteln ergeben zusammengerechnet keinen schlüssigen Tag.

Diese Fehler entstehen nicht durch Nachlässigkeit. Sie entstehen durch organisches Wachstum: Jedes Mal, wenn eine Szene umgeschrieben oder eine Entscheidung geändert wurde, blieb irgendwo ein Echo der alten Version übrig. Das Lektorat macht diese Echos sichtbar.

Alle 18 Findings wurden direkt im Manuskript behoben — kein separates Dokument, keine ToDo-Liste. Danach wurde das EPUB neu gebaut. Das ist der eigentliche Qualitätscheck: Wenn das Skript durchläuft und das Ergebnis ein lesbares Buch ist, ist die Überarbeitung sauber abgeschlossen.

Und dann gab es noch eine zweite Lektorat-Session zwei Tage später, die Timeline-Entflechtung: Zwei Hauptfiguren, die nach einer gemeinsamen Katastrophe getrennte Wege gehen und sich räumlich und zeitlich kreuzen. Bei parallelen Perspektiven braucht man eine taggenaue Timeline-Matrix — und ein Widerspruch (eine Warnmeldung über eine herannahende Gruppe, die in einer Szene ausgeschlossen, in der nächsten aber ignoriert wird) löste sich durch das Vertauschen zweier Kapitel auf einen Schlag: Die charakterliche Entwicklung, der Spracherwerb, die Motivation des ersten Auftritts — alles wurde plausibler, weil die Anordnung stimmte.

---

## Aether-Saga: Wie plant man 1.000 Jahre Geschichte?

Eine Heftroman-Serie schreiben, die 1.000 Jahre Weltzeit überspannt. Klingt machbar. Bis man rechnet.

1.000 Jahre. Ein Monat pro Heft. 52 Hefte im Jahr. Das sind **230 Jahre Realzeit**.

Das ursprüngliche Phasensystem — erste X Hefte langsam, dann beschleunigen — fühlte sich künstlich an. Die Geschichte selbst hat ein eigenes Tempo. In einer Schlachtszene kann kein Tag übersprungen werden. In einer Wiederaufbauphase nach einem Krieg? Da vergehen Jahre, und das Wichtigste ist nicht jede Woche, sondern das Muster des Wandels.

Die Lösung: **Vier Takte** statt fester Phasen.

- **Sturm-Takt** — dichtes, langsames Erzählen. Kein Tag darf fehlen.
- **Drift-Takt** — der mittlere Gang. Reisen, Intrigen, Atempausen.
- **Fluss-Takt** — Sprünge von einem bis zwei Jahren. Wichtige Momente, überbrückt durch kurze Zeitraffer-Prologe.
- **Strom-Takt** — großer historischer Bogen. Jahrzehnte in einem Heft, durch einen fiktiven Chronisten-Auszug eingeleitet.

Der Takt folgt der Geschichte, nicht dem Kalender.

Realistisch durchgemischt: ~2.300 Hefte, ~44 Jahre Realzeit, Serienende etwa mit 85 Jahren. Machbar. Planbar. Ehrlich.

Dazu ein vollständiger Kalender mit 12 Monatsnamen — Namen, die das Leben in einer kalten Nordhalbkugel widerspiegeln: Eismond, Sturmmond, Tauwetter, bis zum Nachtmond im Dezember. Jedes Kapitel bekommt eine kursive Zeitmarkierung direkt nach dem Titel — Ort, Datum, drei Wörter, und der Leser weiß, wo er ist.

In der Softwareentwicklung heißt das technische Schulden. Beim Schreiben ist es dasselbe: Entscheidungen, die man jetzt trifft, um schnell voranzukommen, und die man später teuer zurückzahlt. Das Tempo-System in Heft 80 grundlegend zu überdenken bedeutet, alle bisherigen Outlines anzupassen. Heute war die Investition billiger.

---

## Schwedisch, Sachbücher und ein Lernordner

Am 18. Juni entstand `65_Schwedisch/` im Vault — 14 Markdown-Dateien in 5 Ordnern, intern über Wikilinks verknüpft.

Warum Schwedisch? Weil Schweden wartet. (Das stand schon im letzten Blog-Post.)

Die Struktur folgt einem bewussten Prinzip: Aussprache zuerst (sonst lernt man falsch), dann das Grammatik-Gerüst (`en/ett` ist fundamental für alles weitere), dann Wortschatz, dann Üben. Obsidians Callout-Syntax (`> [!success]-`) versteckt Lösungen hinter einem Klick — ideal für Selbsttests ohne versehentliches Ablesen.

Eine Sache, die ich dabei über Schwedisch gelernt habe: Im modernen Schwedisch gilt „du" für alle. Es gibt kein formelles „Sie". Wer das weiß, hat einen direkten Einstieg in die schwedische Kommunikationskultur — kein Stolpern über Höflichkeitsformen.

Und parallel entstanden zwei Sachbücher: **"Marketing ohne Bullshit"** (6.231 Wörter, sechs Kapitel) und **"KI-Produktivität für Macher"** (6.210 Wörter, fünf Kapitel). Beide als EPUB exportiert. Der gemeinsame rote Faden: Das Marketing-Buch zeigt, *wie* man sein Learning teilt; das KI-Buch liefert das *technische Rüstzeug*, um diese Learnings zu erzeugen.

---

# Schlussgedanke

Sieben Tage saasERP. Zwei Lektorat-Sessions. Vier neue Schreibprojekt-Kapitel. Schwedisch. Ein stiller Android-Crash, der drei Schichten tief lag.

Was diese zwei Wochen verbindet, ist nicht das Thema — es sind die Haltungen dahinter. In saasERP: klein anfangen, konsequent weitermachen, bis aus einem leeren Repository ein nutzbares System wird. Im Lektorat: den eigenen Text so ernst nehmen, dass man ihn mit fremden Augen liest. In der Aether-Saga: investieren, bevor es teuer wird.

Und der stille Android-Crash lehrt dasselbe wie der SSH-Bug aus dem letzten Post: Jede Fehlermeldung hat exakt eine Ursache. Ein Crash der nichts sagt, fängt trotzdem irgendwo an. Der Weg zum Fix beginnt mit der Frage, was sich zuletzt geändert hat.

saasERP lebt jetzt. ElektraLog ist sauberer als je zuvor. Schweden wartet noch — aber das Schwedisch ist in Arbeit.

---

# Kaffee

Über einen
[Kaffee](https://www.buymeacoffee.com/snuppedelua)
würde ich mich auf jeden Fall freuen.
