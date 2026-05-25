---
title: "Makler, Terminals und autonome Agenturen"
date: "2026-05-25T00:00:00Z"
draft: false
description: "Ein Makler-Portal bekommt 14 Features in einer Session. Zwei Lernnotizen entstehen. Und aus einer Idee wird das Konzept einer Softwareagentur, die sich selbst betreibt."
isStarred: false
layout: ""
---

# Makler, Terminals und autonome Agenturen

Manchmal sind Tage einfach voll. Heute war so ein Tag.

Drei Blöcke, sehr unterschiedliche Themen — und trotzdem hängt irgendwie alles zusammen. Los.

---

## Makler-Portal — 14 Features

Das Portal für einen Immobilienmakler war nach dem ersten Aufbau funktionsfähig, aber kahl. Inserate anlegen, löschen — das war es. Was es jetzt hat:

**Backend:** Vier neue Felder in der Datenbank — Wohnfläche, Grundstücksfläche, Zimmeranzahl, Stellplätze. Dazu ein `slug`-Feld für SEO-freundliche URLs. Der Slug wird automatisch aus dem Titel generiert, Umlaute werden sauber umgeschrieben (`ä → ae`, `ü → ue`, `ß → ss`), die Datenbank-ID wird angehängt. Klingt nach Details — ist es auch. Aber `/inserat/einfamilienhaus-am-stadtrand-12` ist einfach besser als `/inserat/7`.

Eine neue Tabelle speichert Kontaktanfragen direkt in der Datenbank statt sie sofort per Mail weiterzuschicken. Das ist Entkopplung: wer später benachrichtigen will, wie er will, kann es tun.

**Frontend:** Neue Detailseite mit Bildgalerie, Lightbox und Kontaktformular. Die Lightbox ist reines CSS und Vue-Reaktivität — keine externe Library. 60 Zeilen CSS, 10 Zeilen Logik. Bei Freelance-Projekten ist jede Abhängigkeit ein Wartungsrisiko; wenn es sich selbst bauen lässt, lohnt es sich.

Das Inserate-Grid auf der Startseite hat Filter bekommen (Typ, Preis, Zimmeranzahl, Fläche) und Pagination mit einem "Mehr laden"-Button. Für eine Makler-Seite, die selten mehr als 20 Inserate hat, schlägt das einfache Lösung die aufwendige: kein nummeriertes Paging nötig.

**Admin-Dashboard:** Inserate lassen sich jetzt bearbeiten. Vorhandene Bilder erscheinen als Thumbnails, einzelne können entfernt werden, neue kommen oben drauf. Dazu eine Entwurf-Funktion — Status "Entwurf" macht ein Inserat unsichtbar, ohne es zu löschen.

**Infrastruktur:** Klartext-Credentials aus `docker-compose.yml` raus, alles in `.env` via `env_file`. Eine `.env.example` liegt im Repo, die echte `.env` bleibt in `.gitignore`.

Eine interessante Kleinigkeit am Rande: In Express muss die Route `/admin` vor `/:slug` stehen, sonst matcht der Slug-Parameter den String "admin". In diesem Fall kein Bug — aber ein stilles Fehlverhalten wäre möglich gewesen. Reihenfolge zählt.

---

## Lernen — TMUX und NeoVim

Neben dem Code sind heute zwei Lernnotizen entstanden, die ich schon länger schreiben wollte.

### TMUX

TMUX (Terminal MUltipleXer) ist eins der Werkzeuge, die man entweder täglich benutzt oder ignoriert — bis man es mal braucht und nicht weiß wie.

Das Grundprinzip ist schlicht: **Session → Window → Pane**. Eine Session lebt auf dem Server und bleibt am Leben, auch wenn die SSH-Verbindung abbricht. Das ist kein aktiviertes Feature. Das passiert einfach, weil Sessions serverseitig existieren. Wer das nicht weiß, verliert Arbeit.

Drei Dinge, die ich mir gemerkt habe:
- `Prefix + d` — Session verlassen (detach), sie läuft weiter im Hintergrund
- `tmux attach -t name` — Session wieder aufnehmen, auch nach Stunden
- `Prefix + z` — Pane auf Vollbild zoomen, für kurze Fokus-Momente

Dazu eine `.tmux.conf`-Grundlage: Prefix auf `Ctrl+a` (angenehmer als `Ctrl+b`), Maus-Support, vi-Keybindings im Copy Mode, intuitivere Split-Bindings. Und **TPM** — der Tmux Plugin Manager — mit `tmux-resurrect` als erstem Plugin: damit werden Sessions auch über Neustarts hinaus gespeichert.

### NeoVim-Konfiguration

Die zweite Notiz ergänzt eine ältere Übersicht zu Vim-Grundlagen — diesmal geht es um den praktischen Aufbau einer Konfiguration.

NeoVim liest seit v0.5 aus `~/.config/nvim/init.lua`. Der Einstiegspunkt lädt Module: `options`, `keymaps`, `autocmds` und die Plugin-Definitionen. Alles in Lua, nichts mehr in Vimscript wenn nicht nötig.

Plugin-Manager der Wahl ist **lazy.nvim** — lädt Plugins nur on-demand. Ein Plugin, das nur bei `.dart`-Dateien gebraucht wird, startet nur dann. Auf SSH-Sessions oder älteren Maschinen macht das einen spürbaren Unterschied.

Der LSP-Stack setzt sich aus vier Schichten zusammen: `mason.nvim` installiert Sprachserver per GUI, `mason-lspconfig.nvim` verbindet die Installation mit `nvim-lspconfig`, und `nvim-cmp` übernimmt die Completion. Die LSP-Keymaps (`gd`, `gr`, `K`, `<leader>rn`, `<leader>ca`) werden beim Attach gesetzt — sauber per Autocommand, nicht global.

Für Flutter und Dart: `flutter-tools.nvim` statt reinem `dartls`. Es kennt den Flutter-Toolchain, kann `flutter run` intern starten und hat DAP-Unterstützung für Debugging.

---

## Paperclip — Eine Idee wird Konzept

Hier wird es größer.

Die Idee: eine Softwareagentur, die ihren Betrieb weitgehend autonom führt. Kein klassisches Agenturmodell mit Mitarbeitern — sondern ein KI-getriebenes System, das sich nach außen wie eine professionelle Agentur verhält.

Das klingt abstrakter als es ist. In der Praxis bedeutet es:

**Kunden kommen über Marketing.** Auf LinkedIn, Xing, Facebook, Instagram und per E-Mail erscheinen regelmäßig Posts und Artikel. Die werden von einer KI geschrieben, reviewed, terminiert und über eine n8n-Pipeline veröffentlicht. Nicht alles läuft blind — aber der Großteil läuft ohne manuelle Handgriffe.

**Kunden fragen an.** Ein Kontaktformular auf der Agentur-Homepage schickt die Anfrage per Webhook an n8n. Eine KI analysiert die Anfrage: Projekttyp, geschätzter Umfang, offene Punkte. Automatische Antwort geht raus — mit Eingangsbestätigung, ggf. Rückfragen.

**Kunden bekommen ein Angebot.** Eine KI generiert Scope, Zeitplan und Preis. Das Angebot kommt als PDF — aus einem LaTeX- oder Typst-Template, automatisch befüllt. Nach drei und sieben Tagen ohne Rückmeldung folgt eine automatische Nachverfolgung.

**Projekte werden umgesetzt.** Nach Auftragsannahme: Staging-Umgebung auf Hetzner via Coolify, erster Prototyp durch KI-gestützte Codegenerierung, Präsentation per Link oder Screencast. Änderungen laufen als Tickets durch denselben Loop. Review durch mich, wenn nötig.

**Buchhaltung läuft mit.** Rechnungen werden automatisch erstellt und verschickt. Zahlungseingänge über Stripe-Webhooks überwacht. Mahnungen nach 14 und 28 Tagen.

Das Leistungsangebot: Webdienste und Flutter-Apps. Primär für KMU und Selbstständige, die etwas Digitales brauchen, aber keine Agentur-Erfahrung und kein Großbudget haben.

Offen ist noch einiges: Rechtsform (GbR, UG oder GmbH?), Nischen-Fokus versus Generalist, Domain, und die ehrliche Frage — wie viel Code kann eine KI wirklich autonom produzieren, bevor ein Mensch reviewen muss?

Acht Meilensteine sind skizziert, vom Konzept bis zum ersten Kunden. Heute ist Meilenstein 0 abgehakt.

---

## Hermes — Der andere Agent

Während Paperclip nach außen zeigt, arbeitet **Hermes** nach innen.

Hermes ist ein geplanter KI-Agent, der über Telegram erreichbar ist — für mich, nicht für Kunden. Er führt ein Langzeitgedächtnis in PostgreSQL, empfängt Befehle, löst n8n-Workflows aus und gibt Statusmeldungen zurück.

Konkret gedacht: Hermes kann recherchieren (`market_researcher`), Entwürfe schreiben (`content_writer`), Workflow-Logs auswerten (`log_analyzer`) und Aufgaben verfolgen (`task_tracker`). Er kennt meine Systeme — Motivhaus, die Infrastruktur, laufende Projekte — und kann im Dialog damit arbeiten.

Die Architektur ist noch grob: Telegram-Bot → n8n → Claude API bei komplexen Anfragen → PostgreSQL für Gedächtnis. Aber die Richtung ist klar.

Was mich an dem Konzept reizt: Hermes und Paperclip sind Geschwisterprojekte, aber mit fundamental verschiedenen Vertrauensmodellen. Hermes ist ein persönlicher Assistent — er darf Kontext kennen, Vorannahmen machen, im Dialog bleiben. Paperclip ist ein Geschäftsprozess — er ist formell, stateless, dokumentiert jeden Schritt. Beide nutzen dieselbe KI-Infrastruktur, agieren aber in völlig verschiedenen Kontexten.

---

## Warum das alles zusammenhängt

Makler-Portal, TMUX, NeoVim, Paperclip, Hermes — das klingt wie ein Sammelsurium.

Aber es ist dasselbe Thema aus verschiedenen Winkeln: **Werkzeuge, die Arbeit abnehmen.** TMUX und NeoVim nehmen mir Bewegungen ab — weniger Maus, weniger Kontextwechsel. Das Makler-Portal nimmt dem Kunden manuelle Arbeit ab. Paperclip nimmt mir Routineaufgaben ab. Hermes nimmt mir Koordinationsarbeit ab.

Das ist kein philosophischer Anspruch. Es ist pragmatisch: Wer Zeit hat, denkt. Wer keine Zeit hat, reagiert. Die Frage ist immer — was kann das System übernehmen, damit ich denken kann?

---

# Schlussgedanke

Heute war lang. Aber es war der gute Lang — der, bei dem man am Ende weiß, was man hat.

Das Haus steht noch. Schweden wartet noch. Hermes schläft noch.

Aber der Plan liegt auf dem Tisch.

---

# Kaffee

Über einen
[Kaffee](https://www.buymeacoffee.com/snuppedelua)
würde ich mich auf jeden Fall freuen.
