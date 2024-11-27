---
title: "Python Buch"
date: "2024-11-07T12:26:04+02:00"
draft: false
description: "Mein neues Buch"
isStarred: false
tags: ["Programmieren", "Python", "Buch"]
---
Dieser Artikel wird noch weiter wachsen, sobald ich mich mit dem Buch weiter beschäftige.

# 36 Python Projekte für die Praxis

Mein Buch ist letzte Woche Freitag endlich angekommen.
Leider hatte ich es ín die Firma bestellt und am Freitag hatte ich einen Brückentag.
Somit kochhte ich erst am Montag das erstmal dir blättern.

Gestern und Heute habe ich Urlaub, da meine Frau in Berlin ist und ich mich um Kind, Haus, Hof und Tiere kümmern muss.
Dazu noch 8 Stunden arbeiten schaffe ich nicht. Frage mich auch bis heute, wie meine Frau das schafft....

Zurück zum Buch!
Das Buch ist in 9 Kapitel gegliedert, auf die ich im Verlaufe mehr eingehen werde.
Das zehnte Kapitel ist eher ein kleines 'Spiel'. Jedes Projekt ist mit Sternen bewertet.
Ein Stern für einfache Projekte bis drei Sterne für schwere Projekte.
Im zehnten Kapitel kann man dann die Sterne sammeln und wenn man alle Projekte geschafft hat, hat man am Ende 120 Sterne.

## erste Kapitel
Wie in den meisten Sachbürchern, ist das erste Kapitel eher so ein allgemeines Blabla.
'Willkommen', 'Warum Projekte', etc.
Kann man also gerne überspringen.

## zweites Kapitel
Im zweiten Kapitel geht es um die Basics in Python:
- Was ist Python
- Installation
- Das erste Programm "Hallo Welt"
- Variablen, Operatoren
- Zahlensysteme (Dezimal, Binär, Hexadezimal)
- Bitoperatoren
- Funktionen
- Input
- Datenstrukturen
- Schleifen und If-Anweisungen
- Dateizugriff
- OOP (Objekt orientierte Programmierung
	- Was sind Klassen
	- Methoden vs Funktionen
	- Vererbung
- Fehler und Ausnahmen

Beim querlesen macht das schon einen guten Eindruck. Man bekommt einen guten Überblick über die Basics.

## drittes Kapitel
Im dritten Kapitel erstellt man die ersten GUI (Graphical User Interface).
Man lernt erstmal die Grundlagen kennen. Welche Elemente gibt es?
- Label
- Button
- Eingabefelder
- Checkboxen / Radiobutton
- etc.

Dazu gibt es dann kleine Übungen, in denen man die Größe des Fensters manipuliert.
Mit dem ersten Button dann ein Label manipuliert (Text, Hintergrundfarbe, Schriftart, etc.)

Zu jedem Element, das erklärt wird, kommt auch eine kleine Übung mit zusätzlichen Aufgaben zum selber ausprobieren und rumspielen.

## viertes Kapitel
Eingeleitet wird das vierte Kapitel mit Grundlagen zu HTML, API's und JSON.

Es wird erklärt wie HTML Daten vom Server zum Client und zurück sendet und welche Statusmeldungen es gibt.

Im Bereich API wird erklärt was eine API ist und wie man sie anspricht. Im Idealfall sollte es zu jeder API eine Dokumentation geben, die man verwenden kann.

Zu JSON wird auch kurz und bündig erklärt, wie der Aufbau ist und dass JSON meist als STRING übertragen werden und anschließend geparst werden müssen.

### Übung 1: ISS lokalisieren
In der ersten Übung wird ein Programm geschrieben, dass den Standort der ISS aufzeigen soll.

```python
import requests
import matplotlib.pyplot as plt
from mpl_toolkits.basemap import Basemap

def iss_location():
    response = requests.get("http://api.open-notify.org/iss-now.json")
    if response.status_code == 200:
        data = response.json()
        iss_position = data["iss_position"]
        return iss_position
    return None

def iss_map():
    iss_position = iss_location()
    if iss_position:
        lon = float(iss_position["longitude"])
        lat = float(iss_position["latitude"])

        geo_map = Basemap(projection="ortho", lat_0=lat, lon_0=lon)
        geo_map.drawcoastlines()

        x, y = geo_map(lon, lat)
        geo_map.plot(x, y, "ro", markersize=10)

        plt.title("Aktuelle Position der ISS")
        plt.show()
    else:
        print("Fehler bei Abruf der ISS-Position.")

if __name__ == "__main__":
    iss_map()
```

Mit diesem kleinen Programm wird in der Funktion "iss_location()" die aktuelle Position des ISS aus dem Internet abgerufen.
Dafür gibt es eine [freie API](http://api.open-notify.org/iss-now.json). Diese stellt die Werte als JSON bereit.

```json
{
	"message": "success",
	"timestamp": 1732300947,
	"iss_position": {
		"longitude": "77.0607",
		"latitude": "12.0804"
	}
}
```

Diese Daten werden innerhalb der Funktion aufbereitet und zuück gegeben.

Die Funktion "iss_map" setzt die Längen- und Breitengrade als Mittelpunkt.
Anschließend werden die Küstenlinien der Kontinente gezeichnet.

In der Mitte wird dann noch ein rotes "X" als Makierung gesetzt.

### Übung 1: Erweiterung (27.11.2024)
Als nächstes soll dann ein zeitlicher Verlauf geplottet werden.
Dafür soll der Benutzer eine Zeit eingeben, über die die aktuelle Position erfasst werden soll.
Im Anschluß wird dann eine Linie auf der Karte gezeichnet, statt eines einzelnen Punktes.

#### Code
Zusätzlich wird nun noch das Modul _time_ importiert.
```python
import requests
import matplotlib.pyplot as plt
from mpl_toolkits.basemap import Basemap
import time
```
Der Abruf der aktuellen Position beibt unverändert.
```python
def iss_location():
    response = requests.get("http://api.open-notify.org/iss-now.json")
    if response.status_code == 200:
        data = response.json()
        iss_position = data["iss_position"]
        return iss_position
    return None
```
Das Zeichnen der Karte wurde dafür umgeschrieben.
Im ersten Schritt wird der Funktion nun die Zeit übergeben, die der 
Benutzer eingegeben hat und ein Array an Positionen der ISS über den
Zeitlichenverlauf.

Die Positionen werden in der Schleife aus dem Array ausgelesen und an das
Modul zum Zeichnen der Karte übergeben. Am Ende der Schleife wird noch
die Überschrift der Karte angepasst und die Karte wird angezeigt.
```python
def iss_map(minutes, positions):
    geo_map = Basemap(projection="ortho",lat_0=0,lon_0=0)
    geo_map.drawcoastlines()

    for position in positions:
        lon = float(position["longitude"])
        lat = float(position["latitude"])

        x, y = geo_map(lon, lat)
        geo_map.plot(x, y, "ro", markersize=5)
    
    plt.title(f"Verlauf der ISS-Position über {minutes} Minuten")
    plt.show()
```
Im Hauptteil wird die aktuelle Systemzeit gespeichert und ein leeres
Array für den Verlauf der Position erstellt.
Anschließend wird der benutzer gefragt, über wie viele Minuten der 
Standort der ISS erfasst werden soll.
Sobald die Eingabe erfolgt ist, startet die Schleife und ruft die Funktion
_iss\_location_ auf. Der Wert, der zurück gegeben wird, wird im Array 
gespeichert. Nach ablauf der Zeit, wird die Eingabe des Benutzers und das
Array an die Funktion _iss\_map_ übergeben.
```python
if __name__ == "__main__":
    starttime = time.time()
    positions = []
    minutes = int(input("Wie viele Minuten soll die ISS getrackt werden? "))

    while time.time() - starttime < minutes * 60:
        position = iss_location()

        if position:
            positions.append(position)
        
        time.sleep(10)

    iss_map(minutes, positions)
```

## fünftes Kapitel

## sechstes Kapitel

## siebtes Kapitel

## achtes Kapitel

## neuntes Kapitel
