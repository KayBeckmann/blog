---
title: "Verschlüsselung"
date: "2025-05-18T00:00:00Z"
draft: false
description: ""
isStarred: false
layout: ""
---

# Hypercube Cipher: Eine Reise in die N-dimensionale Textverschlüsselung mit Gemini

Hallo Krypto-Enthusiasten und Code-Neugierige!

Heute möchte ich euch ein kleines, aber feines Projekt vorstellen, an dem ich in letzter Zeit getüftelt habe: eine browserbasierte Textverschlüsselungsanwendung, die auf einem etwas unkonventionellen Konzept basiert – einem N-dimensionalen Hypercube oder, wie ich es nenne, einem "Gebilde"-Modell. Das Spannende daran? Ein Großteil der Logik und des Codes wurde in Zusammenarbeit mit Gemini, dem KI-Modell von Google, entwickelt und verfeinert.

## Die Kernidee: Verschlüsselung im "Gebilde"

Stellt euch einen Würfel vor. Einfach, oder? Jetzt stellt euch einen vierdimensionalen Würfel (einen Tesserakt) vor. Schon kniffliger. Meine Anwendung hebt dieses Konzept auf `N` Dimensionen.

Die Grundidee ist:

1.  **Das N-dimensionale Gebilde**: Der Nutzer wählt eine Anzahl von Dimensionen (`N`). Jede dieser Dimensionen hat eine "Kantenlänge" von 26, was den 26 Buchstaben des Alphabets ('a'-'z') entspricht, die wir später zur Darstellung der Koordinaten verwenden.
2.  **Füllung mit ASCII**: Dieses $26^N$-zellige Gebilde wird konzeptionell mit druckbaren ASCII-Zeichen (von Leerzeichen bis Tilde `~`) gefüllt. Da es meist mehr Zellen als einzigartige ASCII-Zeichen in unserem Set gibt, wird die ASCII-Zeichenfolge zyklisch wiederholt.
3.  **Von Zeichen zu Koordinaten**: Wenn ein Zeichen aus dem Eingabetext verschlüsselt werden soll, suchen wir dieses Zeichen im Gebilde. Da ein Zeichen an vielen verschiedenen Koordinaten vorkommen kann (durch die zyklische Füllung), haben wir hier einen interessanten Kniff eingebaut.
4.  **Koordinaten zu Chiffre**: Die gefundenen numerischen Koordinaten (jeweils Zahlen von 0-25) werden dann in Buchstaben ('a'-'z') umgewandelt. Die Aneinanderreihung dieser Buchstaben bildet den Chiffretext für das ursprüngliche Zeichen.

## Features, die herausstechen

* **Variable Dimensionalität (`N`)**: Der Nutzer kann `N` (aktuell empfohlen bis 4, aufgrund der Initialisierungszeit) selbst bestimmen und so die Komplexität des Koordinatenraums beeinflussen.
* **Definierter Zeichensatz**: Verwendet die druckbaren ASCII-Zeichen 32-126 – ein Set von 95 Zeichen.
* **Varianz bei wiederholten Zeichen**: Hier kommt die Magie ins Spiel! Zwei gleiche Eingabezeichen führen nicht zwangsläufig zur gleichen Ausgabe. Für jedes Zeichen aus unserem ASCII-Set werden bis zu `K_ALTERNATIVES_PER_CHAR` (aktuell 50) verschiedene Koordinatensätze im Gebilde gesammelt. Bei der Verschlüsselung wird für wiederholte Zeichen zyklisch der nächste verfügbare Koordinatensatz aus dieser Liste gewählt.
* **Interaktive Initialisierung**: Da das Sammeln der Alternativ-Koordinaten für höhere `N` etwas Zeit beanspruchen kann ($26^N$ Zellen müssen durchlaufen werden!), gibt es einen separaten Button zur Initialisierung des Hypercubes. Eine Fortschrittsanzeige visualisiert diesen Prozess.
* **Client-Side Ausführung**: Alles läuft direkt im Browser – kein Server, keine Datenübertragung (außer dem Laden der Seite selbst).
* **Responsives Design**: Dank weiterer Iterationen mit Gemini wurde auch das CSS angepasst, sodass die Anwendung auf mobilen Geräten eine bessere Figur macht.

## Ein kleiner technischer Einblick

Die "Magie" hinter den Kulissen wird durch JavaScript gesteuert:

1.  **`initializeLimitedCoordsMap(N, progressCallback)`**: Diese asynchrone Funktion ist das Herzstück der Vorbereitung. Sie iteriert (in nicht-blockierenden Chunks) durch alle $26^N$ möglichen Koordinaten. Für jede Koordinate wird der entsprechende lineare Index berechnet und das dort "gespeicherte" ASCII-Zeichen (aus `CHARSET_S[linearIndex % CHARSET_S.length]`) ermittelt. Findet sie ein Zeichen, für das noch weniger als `K_ALTERNATIVES_PER_CHAR` Koordinaten gespeichert sind, wird die aktuelle Koordinate der Liste für dieses Zeichen hinzugefügt.
2.  **Verschlüsselung (`processText(true)`)**:
    * Für jedes Eingabezeichen wird die Liste der gesammelten Alternativ-Koordinaten herangezogen.
    * Ein `usageCounters`-Objekt sorgt dafür, dass zyklisch die nächste Koordinate aus der Liste für das jeweilige Zeichen ausgewählt wird.
    * Diese Koordinaten (z.B. `[0, 2, 15]`) werden dann in Buchstaben umgewandelt (z.B. "acp").
3.  **Entschlüsselung (`processText(false)`)**:
    * Der Chiffretext wird in Blöcke der Länge `N` zerlegt.
    * Jeder Block (z.B. "acp") wird in numerische Koordinaten zurückverwandelt (z.B. `[0, 2, 15]`).
    * Aus diesen Koordinaten wird der lineare Index im Gebilde berechnet.
    * Das ursprüngliche Zeichen ergibt sich aus `CHARSET_S[linearIndex % CHARSET_S.length]`.

## Die Entwicklungsreise mit Gemini

Dieses Projekt ist ein großartiges Beispiel dafür, wie generative KI-Modelle wie Gemini den Entwicklungsprozess unterstützen können. Von der ersten Konzeption der mehrdimensionalen Logik über das Debugging von JavaScript-Funktionen, der Strukturierung des HTML-Codes bis hin zur Implementierung der asynchronen Initialisierung mit Fortschrittsanzeige und den responsiven CSS-Anpassungen – Gemini war ein unschätzbar wertvoller Sparringspartner und Code-Assistent. Es war ein iterativer Prozess: Idee -> Implementierungsvorschlag von Gemini -> Testen -> Feedback -> Verfeinerung.

## Probier es aus!

Die Anwendung ist einfach zu bedienen:

1.  Öffne die `index.html` in einem modernen Webbrowser.
2.  Wähle die gewünschte Anzahl der Dimensionen `N` (1 bis 4 empfohlen).
3.  Klicke auf **"Hypercube Initialisieren"**. Beobachte die Fortschrittsanzeige.
4.  Nach erfolgreicher Initialisierung gib deinen Text ein.
5.  Klicke auf **"Verschlüsseln"** oder **"Entschlüsseln"**.

Das Projekt ist [hier](https://projekt.beckmann-md.de/) auch online.

## Quellcode und Feedback

Der gesamte Code ist auf GitHub verfügbar. Schau ihn dir an, probiere ihn aus und gib mir gerne Feedback!

* **[Link zum GitHub-Repository](https://github.com/KayBeckmann/crypt)**
Ich bin gespannt auf eure Gedanken und vielleicht sogar auf Ideen für weitere, verrückte Verschlüsselungsexperimente!

Viel Spaß beim Ausprobieren!
Kay Beckmann

## Kaffee

Über einen
[Kaffee](https://www.buymeacoffee.com/snuppedelua)
würde ich mich auf jeden Fall freuen.
