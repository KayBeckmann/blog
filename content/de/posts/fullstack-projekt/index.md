---
title: 'Fullstack Projekt'
date: '2024-10-26T14:12:37+02:00'
draft: false
description: "Notizen zu meiner Fullstack-Übung"
isStarred: true
tags: ["Django", "Vue", "Fullstack"]
---
Gestartet habe ich den Post am 17.10.2024. Das Datum werde ich bei den Bearbeitungen wahrscheinlich weiter schreiben.
Heute (26.10.24) mache ich etwas weiter in dem Kurs. Meine Zeit ist rar und kostbar.


# Fullstack Projekt

Hier schreibe ich auch die Schnelle mit, wie ich mein Tutorial-Projekt anlege, welche Einstellungen ich mache und was ich sonst so benötige an Infos.

## Installation

In diesem Tutorial werden das Django-Projekt und das Vue-Projekt in einem Repository in Git verwaltet.

Ich bin mir noch nicht sicher, ob ich dieses Vorgehen unterstützen soll.
Zu Übungszwecken halte ich mich aber an die Vorgaben des Dozenten. In späteren Projekten, werde ich wahrscheinlich ein Repository für das Backend und ein weiteres für das Frontend anlegen.

Sicher stellen, dass eine virtuelle Umgebung eingerichtet werden kann:

```bash
sudo apt install python3.11-venv
```
Hier ist der Link zu meinem [Github-Repository](https://github.com/KayBeckmann/django-vue3)

### Django

Erst wird die virtuelle Umgebung erstellt und dann wird diese aufgerufen:

````bash
python3 -m venv venv
source venv/bin/activate
````

Anschließend kann Django zusammen mit dem Rest-Framework installiert werden.

````bash
pip install django
````

Sobald Django in der *VENV* installiert wurde, kann man das Projekt selber anlegen.
Der Punkt am Ende des Befehls ist wichtig, damit das Projekt im Aktuellen Verzeichnis angelegt wird.

````bash
django-admin startproject /PROJECT-NAME/ .
````

Damit ist das Projekt angelegt und sollte startfähig sein.

````bash
python manage.py runserver
````

### VueJS
Zuerst wird ein Vue-Projekt angelegt. Dafür gibt es folgenden Befehl:

```bash
npm init vue@latest
```

Mit diesem Befehl wird ein Vue-Projekt angelegt, dass die neueste Vue-Version benutzt.
Die Fragen beantwortet man dann "einfach" und schon steht das Grundgerüst für das Projekt.

Nachdem die Installation abgeschlossen ist, habe ich den "src" Ordner im neuen Projekt umbenannt und in das Hauptverzeichnis verschoben.
Anschließend mussten noch 2 Dateien bearbeitet werden, damit Vue den neuen Verzeichnisnamen auch kennt.

Nun konnten alle Abhängigkeiten installiert werden und das Projekt zum ersten mal gestartet werden.

```bash
npm install
npm run dev
```

Über den angegebenen Link, kann man die Testseite aufrufen.

## Konfiguration

