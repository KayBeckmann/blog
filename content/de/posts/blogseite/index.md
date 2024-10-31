---
title: 'Blog-Seite'
date: '2024-10-10T22:00:04+02:00'
draft: false
description: "Wie ich diesen Blog eingerichtet habe."
isStarred: true
tags: ["hugo", "installation", "docker", "linux"]
---
# Blog erstellen

In diesem Artikel werde ich mal zusammen schreiben, mit welchen Programmen ich diesen Blog erstellt habe.

Grundlegend benutze ich seit 2008 auf allen meinen Rechnern Linux, somit beziehen sich auch alle Befehle hier auf diese Systeme.

Ich bin mir noch nicht sicher, wie ich diesen Artikel aufbauen werde. Das gesamte Thema 'bloggen' ist noch neu für mich.

Als erstes werde ich die verwendeten Programme kurz vorstellen und dann meine jeweilige Konfiguration.

## Verwendete Programme

Verwendet werden die Programme Hugo, Git und Docker. Zur Syncronisation zwischen den einzelnen Systemen benutze ich Github als Plattform. Ich hatte auch mal eine eigene Gitlab-Instanz laufen, diese ist aber nicht mehr aktiv. Eventuell kommt das zu einem späteren Zeitpunkt noch mal.

### Hugo (ChatGPT erklärung)

Hugo ist ein statischer Website-Generator, der es mir erlaubt, die Inhalte dieses Blogs einfach zu verwalten. Er ist in Go geschrieben und performant. Ich habe mich für Hugo entschieden, weil er schnell, einfach zu benutzen und erweiterbar ist.

### git (ChatGPT erklärung)

Git ist ein Versionskontrollsystem, das mir erlaubt, die Änderungen an den Inhalten dieses Blogs zu verfolgen. Ich kann Änderungen speichern, zurücksetzen und mit anderen Entwicklern zusammenarbeiten.

### docker (ChatGPT erklärung)

Docker ist ein Containerisierungstool, das es mir erlaubt, Programme in isolierten Umgebungen auszuführen. Ich benutze Docker, um Hugo und andere benötigte Programme auszuführen. Das hat den Vorteil, dass die Installation auf allen Systemen identisch ist und ich somit einfach zwischen verschiedenen Computern wechseln kann.

## Konfiguration

In der Konfiguration möchte ich noch einiges automatisieren. Dafür benötige ich aber noch etwas Zeit und die habe ich aktuell nicht.
Sobald ich dafür Zeit habe, werde ich das noch in angriff nehmen und den Artikell hier dann noch anpassen.

## Aktuell
Ich schreibe einen Artikell am Laptop oder am Chromebook. Per Skript lege ich ein neuen Commit in meinem Git-Repository an und pushe das ganze zu Github.
Am Server läuft einmal am Tag ein Crone-Job, der das Repo pulled und dann den entsprechenden Docker Container rebootet.
Das ist schon quasi automatisiert.
Bevor ein Artikel aber auf der Seite erscheint, muss das Programm "Hugo" die Seite aber erst noch Compelieren und das passiert noch auf meinem Laptop manuell.

## Geplant
In Zukunft möchte ich die Artikell nur noch schreiben und dann per Git in das Github-Repository hochladen.
Auf dem Server soll dann ein Container mit Hugo laufen und die Seite auf dem Server compelieren und gleich veröffentlichen.
So viel muss ich also gar nicht mehr nacharbeiten. Trotzdem fehlt mir neben Familie, Hof, Tiere und Arbeit aber die Zeit dafür, mich dort einzulesen.

## Nachtrag 25.10.2022
Vor ein paar Tagen habe ich Googles Gemini mal nach einer Lösung gefragt.
Diese teste ich nun mit diesem Nachtrag.

Das Ganze sieht nun so aus, dass ich einen Beitrag an einem meiner Rechner oder online bei Github schreiben kann. Sofern ich den Beitrag auf den PC's schreibe, kann ich diesen mit meinem Upload-Script zu Github pushen.
Die Beiträge werden dann vom Server von Github runter geladen.
Dann wird ein Container mit Archlinux gestartet, dieser "compeliert" das Projekt und ein zweiter Docker-Container veröffentlicht dann die statischen Inhalte..
Die entsprechenden Dateien werde ich noch nachreichen, sofern der Testlauf erfolgreich ist.

Es hat natürlich nicht auf anhieb funktioniert. Nach ein bisschen hin und her, habe ich festgestellt, dass auf dem Server das Repository mit dem Theme nicht geladen wurde.
Dies habe ich dann einmal manuell nachgeholt und sobald die Daten des Themes vorhanden waren, lief die Compelierung auch durch und nginx konnte den aktuellen Content anzeigen.

Mein DOCKERFILE:
````bash
# Verwenden Sie eine offizielle Arch Linux Basis-Image
FROM archlinux:latest

# Aktualisiere das System und installiere benötigte Pakete
RUN pacman -Syu --noconfirm && \
    pacman -S --noconfirm \
        git \
        hugo

# Setze das Arbeitsverzeichnis im Container
WORKDIR /app

# Kopiere nur die Hugo-Konfiguration und andere notwendige Dateien (nicht den gesamten Quellcode)
# COPY config.toml themes .

# Expose den Port, falls du einen Webserver im Container starten möchtest (optional)
# EXPOSE 1313

# Befehl zum Starten des Containers
CMD ["hugo"]
````

Mein Docker-Compose.yml
````yml
version: "3"

services:
  hugo:
    build: .
    volumes: 
      - ./blog/:/app
    command:
      - hugo

  webseite:
    image: nginx:latest
    restart: always
    volumes:
      - ./blog/public/:/usr/share/nginx/html
    ports:
      - 8080:80
    depends_on:
      - hugo
````

Mein Update-Script:
````bash
#!/bin/bash

cd /home/kay/blog/blog
git pull
cd ..
docker-compose pull
docker-compose down
docker-compose up -d
````
