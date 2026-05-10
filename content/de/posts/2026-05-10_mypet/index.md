---
title: "MyPet — Von Null auf Hundert"
date: "2026-05-10T00:00:00Z"
draft: false
description: "Wie aus einem halbfertigen Login ein vollständiges Veterinär-System mit Android-App wurde."
isStarred: false
layout: ""
---

# MyPet — Von Null auf Hundert

Vor zwei Wochen habe ich hier geschrieben, dass MyPet schlummert. Dass das Grundgerüst steht, der Login funktioniert — und danach nicht viel passiert.

Dann habe ich mich hingesetzt. Ernsthaft. Und die letzten Tage haben das Projekt in etwas verwandelt, das ich selbst kaum wiedererkenne.

---

## Was jetzt steht

Das Projekt ist ein Monorepo unter `/home/kay/git/mypet/`. Sechs Services laufen in Docker-Containern. Das Backend ist in Dart mit Shelf geschrieben, die Frontends in Flutter.

Was früher "Login funktioniert" war, ist jetzt das:

- **Backend**: 38 Datenbankmigrationen, API-Version 0.3.0. Von Benutzerverwaltung über Tierprofile bis hin zu Laborbefunden, Körpertemperatur-Tracking und wiederkehrenden Terminen — das Datenmodell ist gewachsen, ohne auseinanderzufallen.
- **web_owner**: Das Frontend für Tierbesitzer. Globale Suche, ein Gesundheits-Score (0–100) pro Tier, Impfstatus-Ampel im Dashboard, Fütterungs-Streaks, Quick-Actions für den schnellen Alltag. Und Skeleton-Loading-Widgets, weil eine Anwendung, die sich träge anfühlt, sich falsch anfühlt — egal wie gut das Datenmodell ist.
- **web_vet**: Das Frontend für Tierärzte. Zwölf Tabs pro Patient, darunter ein Labor-Tab, Diagnosen-Chips, Druck von Impfzertifikaten und Dossiers. Patientenzuweisung, Wiederholungstermine, Medikamenten-Duplikat-Warnung.
- **web_provider**: Tages-Agenda, Filterchips nach Tierart, Leistungen duplizieren statt neu anlegen.
- **web_admin**: Wachstumsdiagramme, CSV-Export, erweiterte Benutzeransicht.

Und dann ist da noch die Android-App.

---

## Die Android-App

Das war das Ziel, das ich eigentlich erst viel später erwartet hatte.

Sechs Tabs per Bottom-Navigation: Übersicht, Meine Tiere, Medikamente, Erinnerungen, Termine, Profil. Jeder Tab mit eigenem Provider, eigenem State, eigener Logik.

Was mich dabei am meisten beschäftigt hat, war der **PetDetailScreen** — sieben Tabs pro Tier. Impfungen, Medikamente, Akte, Allergien, Laborbefunde, Gewicht, Notizen. Mit einer `SliverAppBar`, die beim Scrollen zusammenklappt, und einem `LinearProgressIndicator`, der den Gesundheits-Score visualisiert.

Das **Gewichts-Tracking** hat einen eigenen Custom-Painter bekommen — eine kleine Sparkline-Kurve direkt in der App, ohne externe Bibliothek. Das war ein Nachmittag, der sich gelohnt hat.

Und dann: Medikamente schnell als "gegeben" markieren, direkt aus der Medikamentenliste heraus, ohne drei Klicks Umweg. Kleine Dinge, aber genau das, was aus einer App etwas Benutzbares macht.

---

## Was das alles bedeutet

Ich habe das Projekt eine Weile vor mir hergeschoben. Zu wenig Zeit, zu viele offene Fragen, zu groß für ein schnelles Nebenbei.

Die letzten Tage haben gezeigt: Wenn man anfängt, fließt es. Nicht immer schnell, nicht immer elegant — aber es fließt. Jede Migration, jede neue Route im Backend, jeder neue Screen in der App baut auf dem auf, was vorher da war.

Das Monorepo-Konzept zahlt sich aus. Gemeinsame Typen zwischen Frontend und Backend, konsistente Datenstrukturen, keine doppelte Logik. Das war am Anfang mehr Aufwand — jetzt ist es der Grund, warum ich schnell vorankomme.

---

## Was als Nächstes kommt

Phase 100 ist abgeschlossen. Die Roadmap geht weiter.

Was als nächstes kommt, werde ich in den nächsten Wochen zeigen. Aber kurz gesagt: Die App soll sich nicht mehr nur nach einem Datenblatt anfühlen, sondern nach einem Werkzeug, das man gerne benutzt.

Das ist eine andere Art von Arbeit. Keine neue Funktion, kein neuer Endpoint — sondern: Was fühlt sich falsch an? Was ist einen Tick zu langsam, einen Tick zu laut, einen Tick zu unübersichtlich?

Das ist die Arbeit, die am Ende den Unterschied macht.

---

# Kaffee

Über einen
[Kaffee](https://www.buymeacoffee.com/snuppedelua)
würde ich mich auf jeden Fall freuen.
