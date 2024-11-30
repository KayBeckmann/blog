---
title: "Python Buch : Kapitel 4 - Übung 3"
date: "2024-11-30T08:00:04+02:00"
draft: false
#description: ""
isStarred: false
tags: ["Programmieren", "Python"]
---
# 36 Python Projekte für die Praxis
Der [Hauptartikel](https://beckmann-md.de/posts/2024-11-07_python/#viertes-kapitel) wird von mir noch weiter ausgeführt.
In den einzel Artikeln kann man die einzelnen Übungen übersichtlicher Nachlesen.

## Kapitel 4
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
