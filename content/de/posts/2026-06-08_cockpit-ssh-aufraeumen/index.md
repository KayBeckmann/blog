---
title: "Cockpit lebt, ein SSH-Bug kapituliert, und das Projektarchiv ist wieder sauber"
date: "2026-06-08T00:00:00Z"
draft: false
description: "Zwei Wochen zwischen Aufräumen, Debuggen und Bauen: ein neues Projekt namens Cockpit erreicht seinen ersten Meilenstein, ein hartnäckiger SSH-Bug auf Android fällt nach drei Fehlerschichten, und ein verstaubtes Projektarchiv wird zum Ausgangspunkt für ein neues SaaS-Konzept."
isStarred: false
tags: ["Flutter", "Dart", "Android", "SSH", "Docker", "Cockpit", "OpenVault", "self-hosted"]
---

# Cockpit lebt, ein SSH-Bug kapituliert, und das Projektarchiv ist wieder sauber

Manche Sessions bauen etwas Neues. Manche reparieren etwas Altes. Und manche räumen einfach nur auf, damit der Kopf wieder frei wird für die nächste Idee. Die letzten zwei Wochen hatten von allem etwas — und am Ende hängt es, wie so oft, zusammen.

Los geht's mit dem Aufräumen, dann ein Bug, der sich nicht kampflos ergeben hat, dann ein komplett neues Projekt, das in derselben Zeitspanne von der Idee zum nutzbaren Werkzeug wurde. Und zum Schluss eine Idee, die aus einem archivierten Projekt einen neuen Plan macht.

---

## Aufräumen zuerst: Wenn die Doku der Realität hinterherhinkt

Bevor irgendetwas Neues entsteht, lohnt sich ab und zu der Blick zurück: Was ist eigentlich liegen geblieben?

Auf dem Server, der unter anderem ein selbst gehostetes SilverBullet (eine Obsidian-ähnliche Notiz-App im Browser) betreibt, war zwischenzeitlich eine neue Verzeichnisstruktur entstanden — mehrere Projekte waren in einen gemeinsamen Ordner umgezogen, ohne dass die Infrastruktur-Dokumentation davon wusste. Klingt nach Kleinkram, ist aber genau die Art von Drift, die sich über Wochen unbemerkt aufbaut, bis eine Pfadangabe in der Doku schlicht nicht mehr stimmt. Vier Dateien aktualisiert, Cross-Links zwischen Server-Übersicht, Dienst-Detailseite und Projekt-READMEs ergänzt — und die Erkenntnis dabei: **Wenn sich eine Verzeichnisstruktur ändert, gibt es fast immer mindestens drei Stellen im Vault, die das wissen wollen.**

Beim Blick auf den Sync-Status des Obsidian-Vaults auf dem Server (er läuft dort als Git-Repo, ein Container pullt und pusht alle 15 Minuten automatisch) tauchten drei verwaiste Dateien auf: `.conflicted:...md`-Artefakte, die SilverBullet bei einem Sync-Konflikt auf einer Testnotiz erzeugt hatte. Bereinigt, vollständigste Version behalten, und eine Auth-Datei nachträglich in die `.gitignore` aufgenommen — wobei sich dabei ein zweiter, fast schon poetischer Bug zeigte: Der `echo`-Befehl zum Anhängen des Eintrags hatte nicht geprüft, ob die Datei mit einem Zeilenumbruch endet. Ergebnis: Der neue Eintrag landete auf derselben Zeile wie der letzte, und aus `.silverbullet.auth.json` wurde ein kurioses `Test.md.silverbullet.auth.json`. Mit `sed` korrigiert, Lehre gezogen: **`echo ... >> datei` ist ohne Zeilenumbruch-Check ein Glücksspiel — `printf` mit explizitem `\n` ist der sicherere Weg.**

Kleines Aufräumen, große Wirkung: Die Doku stimmt wieder mit der Realität überein, und der Autosync läuft seither zuverlässig im 15-Minuten-Takt.

---

## SSH auf Android: drei Fehlermeldungen, drei Ursachen, eine Funktion

Dann der Bug, der sich am hartnäckigsten gewehrt hat: SSH-Clones über GitHub funktionierten in der Android-Version von **OpenVault** (einer selbst gehosteten Alternative zu Obsidian Sync, die ich gerade baue) einfach nicht — trotz korrekt hinterlegtem SSH-Key auf GitHub-Seite.

Die erste Fehlermeldung lautete schlicht `remote hung up unexpectedly`. Wenig hilfreich. Die Diagnose brauchte zwei Anläufe:

**Runde 1 — die Bibliothek ist das Problem, nicht die Konfiguration.** Die App nutzte für SSH bislang Apache MINA SSHD (über JGit). Das Problem: Ed25519-Schlüssel werden über Javas JCA-Provider-Discovery geladen — und die native Ed25519-Unterstützung in Androids JCA gibt es erst ab API 33 (Android 13). Auf älteren Geräten braucht man BouncyCastle als zusätzlichen Provider. Der erste Versuch — BouncyCastle als Dependency registrieren — schlug trotzdem fehl, weil MINA SSHD das Ergebnis der Provider-Abfrage cached und sich nicht so einfach umstimmen lässt.

Die robustere Lösung war ein kompletter Bibliothekswechsel: weg von Apache MINA SSHD, hin zum **JSch-Fork von mwiede** (`com.github.mwiede:jsch`). JSch bringt eine eigene Ed25519-Implementierung mit und liest OpenSSH-Keys direkt aus der Datei — keine JCA, keine Provider-Discovery, keine Cache-Tücken. Die Integration blieb minimal:

```kotlin
override fun createDefaultJSch(fs: FS): JSch {
    val jsch = JSch()
    jsch.addIdentity(keyFile.absolutePath)
    return jsch
}
```

**Runde 2 — der Durchbruch nach drei Schichten.** Mit JSch eingebaut, zeigte die App nicht etwa Erfolg, sondern zwei *neue* Fehlermeldungen — von denen jede eine eigenständige Ursache enthüllte:

- `SecurityException: Permission denied (missing INTERNET permission?)` — der App fehlte schlicht die Netzwerk-Berechtigung im Android-Manifest. Sie war von Anfang an nicht gesetzt; es fiel nur nie auf, weil der SSH-Key-Fehler vorher kam und die App nie bis zum Netzwerk vordrang. Eine Zeile im Manifest behoben das.
- `Auth fail for method publickey` — JSch erreichte GitHub, der Handshake lief — aber GitHub lehnte die Signatur ab. Derselbe Grund wie zuvor: Ohne natives Ed25519 in der JCA (Android < API 33) kann JSch keine gültige Signatur erzeugen. Die Lösung diesmal: BouncyCastle nicht als Ersatz, sondern als **Ergänzung** zu JSch registrieren — als reiner JCA-Provider für `KeyFactory` und `Signature`.

Der vollständige, funktionierende Stack besteht jetzt aus drei Teilen: JSch liest die Schlüssel, BouncyCastle liefert die Ed25519-Kryptografie, und das Manifest erlaubt der App, überhaupt ins Netz zu gehen. Jedes Teil für sich nutzlos — zusammen funktioniert der Clone auf allen Android-Versionen ab etwa API 21.

Die Lehre, die bleibt: **Jede Fehlermeldung hatte exakt eine Ursache.** "Remote hung up" → kein passender Schlüssel-Provider. "Permission denied" → keine Internet-Erlaubnis. "Auth fail" → keine Ed25519-Signatur möglich. Drei Schichten, drei Fixes, eine Funktion — und "es funktioniert nicht" ist eben keine Fehlerbeschreibung, sondern der Anfang einer Suche.

→ Repo: https://github.com/KayBeckmann/OpenVault

---

## Cockpit: vom leeren Repository zum ersten täglich nutzbaren Meilenstein

Und dann ist da **Cockpit** — ein komplett neues Projekt, das in derselben Zeitspanne von der reinen Konzeptidee zu einem Werkzeug wurde, das ich ab sofort täglich benutze.

Die Idee: ein selbst gehostetes, datenschutzfreundliches "zweites Gehirn plus Lebens-Cockpit", in dem alle Lebensbereiche vernetzt sind — eine Aufgabe hängt an einem Projekt, einem Kontakt, einer Notiz — aber sich über einen globalen Kontext-Schalter (Privat / Arbeit / Alles) sauber fokussieren lassen.

**Tag 1 — das Grundgerüst.** Innerhalb einer einzigen Session entstand ein lauffähiges Web-App-Skeleton: Clean-Architecture-Struktur mit getrennten Modulen für Aufgaben, Kalender, Kontakte, Finanzen, Projekte, Erinnerungen und eine Wiki-Anbindung. State-Management über Riverpod, Navigation über go_router, Material 3 mit Hell- und Dunkelmodus, dazu ein Docker-Stack für den lokalen Start im Browser. Bemerkenswert dabei: Ein Architekturkonzept, das schon vor der ersten Codezeile als Dokument existierte (inklusive Datenmodell und Entscheidungs-Log), ließ sich fast 1:1 in Ordnerstruktur und Code übersetzen. Die Vorarbeit hat sich direkt ausgezahlt.

**Tag 2 — das Fundament steht.** Eine echte PostgreSQL-Anbindung mit eigenem Migrationsrunner (SQL-Dateien der Reihe nach ausführen, Stand in einer eigenen Tabelle merken — schlank und für diese Projektgröße völlig ausreichend), eine vollständige Login-Funktion mit gesalzenen, wiederholt gehashten Passwörtern und zeitbegrenzten Zugriffstoken, eine wiederverwendbare Middleware für geschützte Routen, ein kompletter Docker-Compose-Stack für Web-App, Datenbank und Backend zusammen — und eine automatische Prüf-Pipeline, die bei jedem Push Code und Tests durchläuft.

**Und dann: der erste Meilenstein.** Die komplette Aufgabenverwaltung nach GTD-Prinzip ist fertig — vollwertiger Ersatz für eine ToDo-App. Inbox-Triage für unkategorisierte Aufgaben, eine Tagesansicht, die nach Dringlichkeit *und* Wichtigkeit sortiert (Eisenhower-Prinzip — bei gleicher Fälligkeit entscheidet die Priorität), Energie-Level-Filter (mal ist man für die komplizierte Aufgabe bereit, mal nur für die einfache), wiederkehrende Aufgaben mit konfigurierbarem Rhythmus, Teilaufgaben-Checklisten — und obendrauf ein Projekt-Modul mit Kanban-Board (Spalten für Aktiv / Pausiert / Archiviert) und einer Detailansicht, deren Fortschrittsbalken sich live aus dem Verhältnis erledigter zu offener Aufgaben berechnet, statt aus einem manuell zu pflegenden Zahlenwert.

Eine kleine, aber lehrreiche Designentscheidung am Rande: Ein Kanban-Board mit horizontal scrollbaren Spalten, die selbst wieder vertikal scrollen, ist in Flutter eine bekannte Stolperfalle ("unbounded height"-Fehler). Die Lösung: schlanke, inhaltsgroße Spalten-Layouts in zwei verschachtelten Scrollbereichen — sauber, vorhersehbar, und gut automatisiert testbar.

Und genau das war der rote Faden der ganzen Cockpit-Arbeit: **kleine, nachvollziehbare Schritte statt großem Wurf.** Jeder Roadmap-Punkt ein eigener Commit, jeder fertige Abschnitt getestet, gefixt, dokumentiert — bis am Ende ein kompletter Meilenstein steht, verifiziert über mehr als 170 automatisierte Tests, beide Analyzer fehlerfrei.

→ Repo: https://github.com/KayBeckmann/cockpit

---

## Großreinemachen im Projektarchiv — und eine Geschäftsidee wird konkret

Den Abschluss bildete eine echte Aufräumsession der anderen Art: die zentrale Projektübersicht. Sie war seit Wochen nicht mehr gepflegt — alte Einträge zeigten auf längst archivierte Projekte, neue Vorhaben fehlten komplett. Eine Projektübersicht ist eben wie ein Garten: Wenn man sie ein paar Wochen nicht anfasst, wuchert sie mit toten Links zu, die niemandem mehr auffallen, bis man genau hinschaut.

Im Zuge dessen wanderte ein abgeschlossenes internes ERP-Projekt für ein Einzelunternehmen mit zwei Geschäftsbereichen ins Archiv — und die Produktidee, die daraus entstanden war, bekam einen eigenen Namen und Ordner: **saasERP**, ein Mini-ERP als Multi-Tenant-SaaS-Produkt für Handwerksbetriebe. Die Idee bekam in dieser Session deutlich schärfere Konturen: Neuentwicklung in Flutter statt Weiterentwicklung des bestehenden Web-Frontends (eine Codebasis für Android, Linux, Windows, Web), sowohl Managed-SaaS- als auch Self-Hosting-Angebote, eine eigene mandantenfähige Dokumentenablage statt externer Komponenten.

Der interessanteste neue Baustein: eine Abo-Verwaltung mit fairer Vertragsstrafen-Logik. Bei vorzeitiger Kündigung soll die Strafe linear mit der Restlaufzeit sinken:

```
Strafe = maximale Strafe × (Restlaufzeit / Laufzeit)
```

Wer früh kündigt, zahlt mehr. Wer kurz vor Vertragsende kündigt, zahlt fast nichts. Eine simple Formel, die eine geschäftliche Fairness-Überlegung in etwas Nachvollziehbares übersetzt — wobei die rechtliche Zulässigkeit in AGB gegenüber Geschäftskunden noch geprüft werden muss, bevor sie zum Einsatz kommt.

Zwei kleine Randnotizen aus derselben Session: Ein eigenständiges LaTeX-Projekt für einen Jahresplaner hatte einen unveröffentlichten lokalen Commit, während remote zwischenzeitlich ein neues Layout-Feature entstanden war — beide Stände betrafen unterschiedliche Dateien und ließen sich konfliktfrei zusammenführen. Und: OpenVault, das Projekt aus dem SSH-Kapitel oben, wechselte in der Projektübersicht offiziell von "in Planung" zu "aktiv, ~80 %" — es ist inzwischen produktiv im Einsatz.

---

# Schlussgedanke

Aufräumen, ein zäher Bug, ein neues Projekt, eine Idee, die Konturen bekommt — auf den ersten Blick vier verschiedene Geschichten. Aber sie haben denselben Kern: Ordnung schaffen, bevor sie zur Last wird. Den eigenen Testkontext nicht vertrauen, sondern prüfen. Klein anfangen und konsequent weitermachen, bis aus einem leeren Repository ein Werkzeug wird, das man täglich benutzt.

Cockpit lebt jetzt. SSH funktioniert endlich. Und das Projektarchiv zeigt wieder, was wirklich läuft.

---

# Kaffee

Über einen
[Kaffee](https://www.buymeacoffee.com/snuppedelua)
würde ich mich auf jeden Fall freuen.
