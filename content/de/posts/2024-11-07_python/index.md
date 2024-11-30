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

### Übung 2: NASA - Bild des Tages
In Python werden im ersten Schritt immer erstmal alle Imports vorgenommen.
Nachdem wir mit TKinter eine GUI erstellen, muss TKinter auch importiert werden.
_PIL_ und _io_ werden zur Konvertierung des Bildes benötigt.
Mit _datetime_ wird auf das aktuelle Datum zugegriffen, welches im Namen des Bildes verwendet wird beim Speichern.
_requests_ ruft für uns die Daten der API ab.

```python
import tkinter as tk
from PIL import Image, ImageTk
from io import BytesIO
from datetime import datetime
import requests
```

In der Variablen _current_date_ wird das aktuelle Datum als **string** gespeichert.
_filename_ enthält den Namen des Bildes, ebenfalls als **string**.
mit dem Befehl _img.save()_ wird das Bild im Ordner des Python-Skripts gespeichert.

```python
def save_image(img):
    current_date = datetime.now().strftime("%Y-%m-%d")
    filename = f"{current_date}_nasa.png"
    img.save(filename)
    print(f"Bild gespeichert als: {filename}")
```

Das Hauptprogramm ist etwas lang und könnte noch gut aufgespalten werden.
es wird zweimal die requests-Funktion aufgerufen. Dies könnte man eventuell noch auslagern.
Allgemein ist der Part mit den Datenladen und aufbereiten gut geeignet für eine eigene Funktion.
Das werde ich bei Gelegenheit noch mal in Angriff nehmen.
Heute Abend lebe ich aber erstmal mit der Lösung, die ich aus dem Buch heraus habe.
```python
if __name__ == "__main__":
    root = tk.Tk()
    root.title("NASA Bild des Tages")

    api_url = "https://api.nasa.gov/planetary/apod?api_key=DEMO_KEY"
    response = requests.get(api_url)
    data = response.json()
    image_response = requests.get(data["url"])
    img = Image.open(BytesIO(image_response.content))
    img_scaled = img.resize((600, 400), Image.Resampling.LANCZOS)
    photo_image = ImageTk.PhotoImage(img_scaled)
    label_image = tk.Label(root, image=photo_image)
    label_image.pack()

    img_description = tk.Text(root, 
                              wrap="word", 
                              height=10)
    img_description.insert("1.0", data["explanation"])
    img_description.pack()

    save_button = tk.Button(root,
                            text="Bild speichern",
                            command=save_image(img))
    save_button.pack()

    root.mainloop()
```

Das Bild vom heutigen Tag gefällt mir nicht so gut.
Ich werde mal schauen, was die nächsten Tage für Bilder dabei sind.
Sobald mir eins davon zu sagt, werde ich das in diesem Artikel mit einbinden.


### Übung 2: Erweiterung 
In der Erweiterung soll die Beschreibung des Bildes automatisch ins deutsche übersetzt werden.
Dazu soll eine Google API angesprochen werden.
Diese Übung werde ich zu einem späteren Zeitpunkt nachholen.

### Übung 3: Wo bin ich?
Die dritte Übung soll die eigene IP im Internet aufzeigen.
Weiterhin wird der Browser geöffnet und auf einer Karte wird der Standort angezeigt.

Die IP wird über die folgende API abgerufen:
```
https://api.ipify.org/
```

Die unten stehende API gibt weitere Informationen zu einer IP zurück.
In diesem Fall ist es die eigene IP.
Man kann aber auch jede andere IP abfragen.
```
http://ip-api.com/json/EIGENE_IP
```

Wie gewohnt, werden erst die benötigten Module importiert.
Mit _tkinter_ wird das Modul für die GUI importiert.
_requests_ wird für die Abfrage der APIs benötigt.
_webbrowser_ wird in diesem Projekt nur verwendet, um den 
Browser zu öffnen.
_folium_ erstellt die Karte und setzt die Makierung
```python
import tkinter as tk
import requests, webbrowser, folium
```

Wie der Name der Funktion schon vermuten lässt, wird über den 
Requests die IP-Adresse des Benutzers abgefragt.
Sofern die Abfrage erfolgreich ist, wird die IP als Text zurück
gegeben. Sollte die Abfrage erfolglos sein, wird ein Fehlertext
zurückgegeben.
```python
def get_ip_adresse():
    try:
        response = requests.get("https://api.ipify.org/")
        return response.text
    except requests.exceptions.RequestException:
        return "Deine IP -Adresse konnte nicht ermittelt werden."
```

Mit der Funktion _get\_ip\_location_ werden zusätzliche Informationen
zum Benutzer abgerufen.
Unter anderem der Längengrad und der Breitengrad.
Diese Informationen werden in einer Tuppel zurückgegeben.
Sollte die Abfrage einen Fehler erzeugen, wird **None** zurückgegeben.
```python
def get_ip_location(ip_address):
    try:
        response = requests.get(f"http://ip-api.com/json/{ip_address}")
        data = response.json()
        return (data["lat"], data["lon"])
    except requests.exceptions.RequestException:
        return None
```

Mit dem Längengrad und dem Breitengrad wird die Karte erstellt und im
Browser dargestellt.
```python
def show_location_on_map(location):
    map = folium.Map(location=location,
                     zoom_start=12)
    folium.Marker(location).add_to(map)
    map_path="iplocation.html"
    map.save(map_path)
    webbrowser.open(map_path)
```

Die Hauptfunktion, die vom Button aufgerufen wird.
Als erstes wird die IP angerufen und in einer Variablen gespeichert.
Die IP wird dann an die zweite Funktion übergeben, damit der Standort
ermittelt wird und in der Variablen **location** gespeichert wird.
Sollte ein Fehler auftreten bei der Abfrage, ist der Rückgabewert
**None**, was einem **False**-Wert entspricht. Damit würde die 
IF-Abfrage übersprungen und im _ip\_label_ steht die IP.
Sofern es eine **locatioin** gibt, wird die Karte geöffnet.
```python
def show_ip_and_location():
    ip_address = get_ip_adresse()
    location = get_ip_location(ip_address)
    if location:
        show_location_on_map(location)
    ip_label.config(text=f"IP-Adresse: {ip_address}")
```

Der Hauptabschnitt, in dem alle Elemente der GUI erzeugt werden und
auf der GUI platziert werden.
```python
if __name__ == "__main__":
    root = tk.Tk()
    root.title("Wo bin ich?")
    root.geometry("600x400")

    ip_label_font = ("Arial", 16, "bold")
    button_font = ("Arial", 16)

    ip_label = tk.Label(root,
                        text="...",
                        bg="yellow",
                        font=ip_label_font)
    ip_label.pack(padx=20,
                  pady=20,
                  fill=tk.BOTH,
                  expand=True)
    
    tk.Button(root,
              text="Standort ermitteln",
              command=show_ip_and_location,
              font=button_font,
              height=2,
              width=20).pack(pady=20)

    root.mainloop()
```


### Übung 3: Erweiterung
In der Erweiterung soll man eine Erkennung einbauen.
Diese soll anzeigen, ob man einen Proxy verwendet.


## fünftes Kapitel

## sechstes Kapitel

## siebtes Kapitel

## achtes Kapitel

## neuntes Kapitel
