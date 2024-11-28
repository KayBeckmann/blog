---
title: "Python Buch : Kapitel 4 - Übung 2"
date: "2024-11-27T08:00:04+02:00"
draft: false
#description: ""
isStarred: false
tags: ["Programmieren", "Python"]
---
# 36 Python Projekte für die Praxis
Der [Hauptartikel](https://beckmann-md.de/posts/2024-11-07_python/#viertes-kapitel) wird von mir noch weiter ausgeführt.
In den einzel Artikeln kann man die einzelnen Übungen übersichtlicher Nachlesen.

## Kapitel 4
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