---
title: "Mai — Logos, Abnahmeprotokolle und ein Plan für später"
date: "2026-05-21T00:00:00Z"
draft: false
description: "ElektraLog bekommt ein Gesicht. CityBuilder ist fertig. Und aus einer Idee auf einem Zettel wird ein neues Projekt."
isStarred: false
layout: ""
---

# Mai — Logos, Abnahmeprotokolle und ein Plan für später

Elf Tage. Der letzte Post war am 10. Mai. Das fühlt sich nicht nach viel an — und doch ist einiges passiert.

---

## ElektraLog — Vom Prototyp zur App

Wer meinen letzten Post gelesen hat, weiß: ElektraLog ist meine Flutter-App für digitale VDE-Prüfprotokolle. Sie existiert seit einer Weile, läuft auch, aber hatte noch diesen typischen "arbeitet-noch-daran"-Charakter.

Das hat sich geändert.

### Ein Logo

Klingt nach einer Kleinigkeit. Ist es nicht. Solange eine App kein Icon hat, fühlt sie sich nach Werkzeug an — nützlich, aber nicht fertig. Das neue Logo zeigt eine Lupe mit einem Blitz darin. Schlicht, sofort lesbar, passt zur Domäne: elektrische Prüfungen, genau hinschauen. Das Icon steckt jetzt in der Navigation, im Favicon, im Auth-Screen.

Manchmal braucht es so etwas, damit man selbst merkt: Das ist eine echte App.

### Android

Ja, sie läuft jetzt auch auf Android. Das war keine Selbstverständlichkeit — die CI/CD-Pipeline hat ein paar Tage Debuggen gekostet. Das Problem war bezeichnend: `${{ github.repository_owner }}` gibt den Namen in Originalschreibweise zurück. GHCR akzeptiert nur Kleinbuchstaben. Die Fehlermeldung war dabei irreführend genug (`permission denied`), dass man erst mal an den Tokens und Repository-Einstellungen sucht — und nicht beim simpelsten möglichen Problem landet.

Fix war ein einzelner Shell-Befehl. Dafür hat er einige Stunden gedauert, bis er gefunden war.

### Designentscheidungen

In dieser Woche haben sich auch ein paar grundlegende Dinge für das Geschäftsmodell geklärt, die ich zu lange offengelassen hatte:

- **Kein Self-Sign-up.** Wer ElektraLog im Company-Modus nutzen will, bekommt den Zugang nicht über ein Registrierungsformular. Ich lege Mandanten manuell an. Das klingt weniger skalierbar — ist aber in der frühen Phase bewusste Kontrolle: Ich kenne jeden Kunden, kann schnell reagieren, und vermeide von Anfang an Support-Aufwand für Konten, die niemand bezahlt.
- **Flat Pricing.** Statt eines gestaffelten Tier-Modells (Team / Business / Enterprise) gibt es eine einzige Preisstufe: 20 € im Monat oder 200 € im Jahr pro Firma. Unbegrenzte Nutzer. Das ist einfacher zu kommunizieren, einfacher zu verwalten, und für kleine Elektrobetriebe — die Zielgruppe — ein fairer Preis.
- **Zugangssperrung.** Wenn eine Zahlung ausbleibt, sperre ich den Mandanten. Nicht automatisch, sondern manuell — aber das Superadmin-Portal hat dafür jetzt den Schalter.

Das sind keine technischen Features. Das sind Entscheidungen. Und manchmal sind die wichtiger als der nächste Commit.

---

## CityBuilder — Fertig

Das ist das Projekt, über das ich am wenigsten geschrieben habe — und das vielleicht am lautlosesten ins Ziel gekommen ist.

CityBuilder ist ein SimCity-ähnliches Spiel in Flutter/Flame. 19 Meilensteine. Alle abgehakt. v1.0.0 ist der Release-Kandidat.

Ich werde das nicht weiter ausführen — wer wissen will, was drin steckt, wird sehen, wenn es draußen ist. Aber der Moment, in dem man den letzten Haken setzt, ist eigenartig ruhig. Kein Feuerwerk. Nur: *Fertig.*

---

## miniERP — Wächst weiter

miniERP ist das Projekt, das ich nie für realistisch gehalten habe — und das trotzdem einfach läuft.

Seit dem letzten Post: Phase 10 ist drin. Teil- und Schlussrechnungen laufen jetzt sauber durch den Workflow. Budget-Übernahme aus Angeboten in Aufträge funktioniert — wer ein Angebot schreibt, sieht beim Anlegen des Auftrags automatisch, was budgetiert war, und kann stunden- und materialgenau dagegen buchen.

Das ist die Art Feature, die aus dem echten Alltag kommt. Nicht aus einer Spezifikation, sondern aus dem Moment, in dem man merkt: Das brauche ich eigentlich gerade jetzt.

Docker-Volumes laufen jetzt auf lokale Bind-Mounts — wer Produktionsdaten anfassen will, soll wissen wo sie liegen. Das ist kein Feature. Das ist Kontrolle.

---

## Protokollant — Eine Idee aus der Praxis

Das ist neu.

Im Betrieb gibt es seit Jahren ein Abnahmeprotokoll für Schaltschränke — Papier, Kugelschreiber, Unterschrift, einscannen, ins ERP heften. Das funktioniert. Aber es ist 2026.

Die Idee: eine Flutter-App, die diesen Prozess digitalisiert. Abnahmepunkte abhaken (Bestanden / Durchgefallen / Nicht zutreffend), RCD-Messwerte erfassen, am Ende digital unterschreiben, PDF exportieren. Zwei Seiten: Protokoll auf Seite 1, RCD-Tabelle auf Seite 2.

Das Projekt heißt **Protokollant**. Es ist noch Idee — kein Code, kein Repo. Aber die Anforderungen sind klar, weil sie aus einer echten Lücke kommen. Das ist der beste Ausgangspunkt für jedes Werkzeug.

Was mich daran reizt: Es ist überschaubar. Das ist kein Motivhaus, kein ElektraLog. Es ist ein fokussiertes Problem mit einer fokussierten Lösung. Manchmal ist das genau das, was man braucht.

---

## Ein Plan für später

Zwischen allem hat sich etwas anderes herauskristallisiert — nicht ein Projekt, sondern eine Frage: Wie werden die Dinge, die ich baue, eigentlich sichtbar?

ElektraLog braucht Kunden. Motivhaus braucht ein Publikum. Freelance-Automatisierung funktioniert nur, wenn jemand weiß, dass ich das anbiete.

Das ist keine neue Frage. Aber sie wird konkreter, je weiter die Projekte reifen. Was ich jetzt konkret angehe:

**Plattformen.** Nicht nur YouTube. LinkedIn und Xing für die B2B-Seite — Selbstständige und KMU, die automatisieren wollen. Instagram für den visuellen Teil: App-Screenshots, Fortschrittsmomente, "das ist gerade live gegangen". Und dieser Blog als Heimatbasis — der einzige Ort, wo Inhalte wirklich permanent sind und gefunden werden können.

**Prinzip.** Einmal erstellen, mehrfach verwerten. Ein Blog-Post wie dieser ist der Ausgangspunkt. Daraus entsteht ein LinkedIn-Post, ein Instagram-Bild, vielleicht später ein Video. Das Zeitbudget ist knapp — also muss der Prozess schlank sein, nicht die Qualität.

**Das Werkzeug hinter dem Werkzeug.** Was mich dabei besonders beschäftigt: Ich dokumentiere die Arbeit ohnehin — im Vault, in Git-Commits, in Notizen. Der Schritt von "dokumentiert für mich" zu "aufbereitet für andere" ist kleiner als gedacht. Was dafür fehlt, ist nicht Stoff. Es ist Routine.

Die ist jetzt im Aufbau. Noch kein Video. Noch kein viraler Post. Aber der Prozess ist beschrieben, die Plattformen sind klar, und der erste Schritt ist immer der gleiche: einfach anfangen.

---

# Schlussgedanke

Elf Tage. Ein Logo, ein abgeschlossenes Spiel, ein neues Projekt in der Schublade, ein Sichtbarkeitsplan auf Papier — und ein bisschen mehr Klarheit darüber, wohin das alles führen soll.

Das Haus steht noch. Schweden wartet noch. Aber der Norden wird schon WLAN haben.

---

# Kaffee

Über einen
[Kaffee](https://www.buymeacoffee.com/snuppedelua)
würde ich mich auf jeden Fall freuen.
