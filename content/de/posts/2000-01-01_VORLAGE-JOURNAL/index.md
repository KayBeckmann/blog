---
title: 'Monatsübersicht'
date: '2025-01-01T00:00:00Z'
draft: true
description: "Vorlage für Journals."
isStarred: false
layout: "monthly_overview"
---

# Monatsübersicht: Januar 2025

Willkommen zur Monatsübersicht! Hier kommen die Aufgaben und Journaleinträge für den aktuellen Monat.

## Aufgabenübersicht

| Datum       | Aufgaben                          | Status       |
|-------------|-----------------------------------|--------------|
| 01.01.2025  | Beispielaufgabe 1                 | Offen        |
| 02.01.2025  | Beispielaufgabe 2                 | Erledigt     |
| 03.01.2025  | Beispielaufgabe 3                 | In Bearbeitung |

## Journaleinträge

{{ range (where .Site.Pages "Section" "journal") }}
- [{{ .Title }}]( {{ .RelPermalink }} ) ({{ .Date.Format "02.01.2006" }})
{{ end }}