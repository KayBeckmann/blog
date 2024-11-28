---
title: "Python Buch : Kapitel 4 - Übung 1"
date: "2024-11-22T08:00:04+02:00"
draft: false
#description: ""
isStarred: false
tags: ["Programmieren", "Python"]
---
# 36 Python Projekte für die Praxis
Der [Hauptartikel](https://beckmann-md.de/posts/2024-11-07_python/#viertes-kapitel) wird von mir noch weiter ausgeführt.
In den einzel Artikeln kann man die einzelnen Übungen übersichtlicher Nachlesen.

## Kapitel 4
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