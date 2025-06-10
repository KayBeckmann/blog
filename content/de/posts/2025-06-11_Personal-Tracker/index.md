---
title: "Personal Tracker: Dein ganzes Leben in einer App"
date: "2025-06-11T00:00:00Z"
draft: false
description: ""
isStarred: false
layout: ""
---

# Personal Tracker: Dein ganzes Leben in einer App

In unserer digitalen Welt jonglieren wir ständig mit unzähligen Apps: eine für das Budget, eine für To-Do-Listen, eine weitere für Notizen und vielleicht noch eine, um neue Gewohnheiten zu etablieren. Das kann schnell unübersichtlich werden. Was wäre, wenn es eine einzige, zentrale Anwendung gäbe, die all diese Bereiche abdeckt?

Genau das ist die Idee hinter dem Personal Tracker: eine All-in-One-Lösung, die dir hilft, deine Finanzen, Aufgaben, Gewohnheiten und Notizen an einem Ort zu verwalten. Lass uns einen Blick darauf werfen, was dieses mit Vue.js entwickelte Projekt alles kann.

## Was ist der Personal Tracker?

Der Personal Tracker ist eine Progressive Web App (PWA), die darauf ausgelegt ist, verschiedene Aspekte deines Lebens zu organisieren. Der große Vorteil: Da es eine PWA ist, kannst du sie auf deinem Smartphone oder Desktop installieren und sogar offline nutzen. Alle deine Daten bleiben sicher auf deinem Gerät gespeichert, ohne dass sie in eine Cloud hochgeladen werden müssen.

Die Anwendung ist in mehrere, klar voneinander getrennte Module unterteilt:

1. **Dashboard:** Die Kommandozentrale
   Wenn du die App öffnest, landest du direkt auf dem Dashboard. Hier bekommst du einen schnellen Überblick über die wichtigsten Informationen aus allen Bereichen:

- Anstehende Aufgaben
- Heutige Gewohnheiten
- Eine Zusammenfassung deiner finanziellen Situation

So siehst du auf einen Blick, was gerade wichtig ist.

2. **Budget**: Volle Kontrolle über deine Finanzen
   Das Herzstück für viele Nutzer ist das Budget-Modul. Hier kannst du:

- Transaktionen erfassen: Trage Einnahmen und Ausgaben ein und weise sie verschiedenen Konten (z. B. Girokonto, Bargeld) und Kategorien (z. B. Lebensmittel, Miete) zu.
- Finanzen visualisieren: Verschiedene Diagramme helfen dir, deine Ausgaben zu verstehen. Eine Pareto-Analyse zeigt dir beispielsweise die 20 % der Ausgaben, die 80 % der Kosten verursachen, während Liniendiagramme den Verlauf deines Vermögens darstellen.
- Konten und Kategorien verwalten: Lege eigene Konten und Ausgabenkategorien an, um das Tracking perfekt an deine Bedürfnisse anzupassen.

3. **Habits**: Gute Gewohnheiten aufbauen
   Egal, ob du mehr Sport machen, täglich lesen oder meditieren möchtest – das Gewohnheiten-Modul unterstützt dich dabei. Du kannst tägliche oder wöchentliche Gewohnheiten definieren und jeden Tag abhaken, was du erledigt hast. Das motiviert und hilft dir, am Ball zu bleiben.

4. **Tasks**: Behalte deine Aufgaben im Griff
   Ein einfaches, aber effektives To-Do-Listen-Modul. Erstelle Aufgaben, setze Fälligkeitsdaten und markiere sie als erledigt. So vergisst du nichts Wichtiges mehr.

5. **Notes**: Platz für deine Gedanken
   Ein integrierter Notizblock, der perfekt für schnelle Ideen, Geistesblitze oder Tagebucheinträge ist.

## Ein Blick unter die Haube: Die Technologie

Für die technisch Interessierten unter euch, hier ein kurzer Einblick in den Tech-Stack:

- **Frontend**: Das gesamte Projekt basiert auf Vue.js 3 mit der Composition API und ist in TypeScript geschrieben. Das sorgt für eine moderne, typsichere und gut strukturierte Codebasis.
- **Build-Tool**: Vite ermöglicht eine extrem schnelle Entwicklungsumgebung.
- **State Management**: Pinia dient als zentraler Store, um den Zustand der Anwendung (z. B. geladene Transaktionen oder Aufgaben) zu verwalten.
- **Routing**: Vue Router kümmert sich um die Navigation zwischen den einzelnen Ansichten wie Dashboard, Budget oder Tasks.
- **Client-seitige Datenbank**: Anstelle einer Server-Datenbank kommt Dexie.js zum Einsatz. Dexie ist ein smarter Wrapper für die IndexedDB des Browsers. Das bedeutet:
  - _Datenschutz_: Alle Daten bleiben lokal auf deinem Gerät.
  - _Offline-Fähigkeit_: Die App funktioniert auch ohne Internetverbindung.
- **PWA**: Dank des `vite-plugin-pwa` ist die Anwendung installierbar und bietet ein natives App-Gefühl.

## Fazit und Ausblick

Der Personal Tracker ist ein großartiges Beispiel dafür, wie leistungsfähig moderne Web-Technologien geworden sind. Er vereint mehrere Produktivitäts-Tools in einer einzigen, datenschutzfreundlichen Anwendung.

Die lokale Datenspeicherung ist ein klares Statement für den Schutz der Privatsphäre. Zukünftig könnte man über eine optionale, Ende-zu-Ende-verschlüsselte Synchronisierung nachdenken, um die Daten auf mehreren Geräten nutzen zu können.

Wenn du neugierig geworden bist, schau dir doch den
[Quellcode](https://github.com/KayBeckmann/personal-tracker)
auf GitHub an und probiere die
[App](https://personal-tracker.kay-beckmann.de/)
selbst aus!

# Kaffee

Über einen
[Kaffee](https://www.buymeacoffee.com/snuppedelua)
würde ich mich auf jeden Fall freuen.
