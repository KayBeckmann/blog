---
title: "ASUS Chromebook Plus CX34"
date: "2024-12-08T02:26:04+02:00"
draft: false
description: "ASUS Chromebook Plus CX34"
isStarred: false
tags: ["Chromebook", "Hardware"]
---

# Chromebook

## Vorgeschichte

Ich habe mir letztes Jahr zu den PrimeDays ein günstiges Chromebook gekauft.
Mit diesem wollte ich mal sehen, wie ich ein Chromebook in meinen
Arbeitsabläufen intigrieren kann. Das Ergebnis war, dass ich mir dieses Jahr
zur BlackWeek ein neues Chromebook gekauft habe.

## Spezifikation

Hier verlgeiche ich einfach mal mein "altes" Chromebook mit dem neuen.
Bei beiden Geräten habe ich darauf geachtet, dass eine X86 CPU verbaut ist.
Da hierfür der Support der Linux Programme einfach besser ist, als bei ARM-CPU.
Ich nutze schon seit 2009 Linux auf allen meinen PC und Laptops, deshalb ist mir
die Unterstützung der Linux Software auch so wichtig.

| **Typ**         | Asus CM1400FX flip | ASUS CX34 |
| --------------- | ------------- | --------- |
| CPU             | AMD Athlon3015Ce | Intel i3|
| Arbeitsspeicher | 4 GB          | 8 GB |
| Festplatte      | 64 GB SSD     | 128 GB UFS |
| ---             | ---           | ---       |
| Display         | Full-HD Touchscreen Display | Full-HD 16:9 IPS Display |
| Diagonale       | 14 Zoll       | 14 Zoll |
| ---             | ---           | ---       |
| Anschlüße       | 2x USB-C, 1xUSB-A, HDMI | 2x USB-C, 2xUSB-A, HDMI |
| Audio       | Klinke | Klinke |
| Speicherkarte   | Micro SD | **OHNE** |

Im ersten Moment sind das alle Daten, die ich vergleichen will.
Sollte mir noch etwas einfallen, werde ich die Liste noch erweitern.

## Einrichtung
Die Einrichtung verlief sehr einfach. Anmelden mit dem Google-Account
und alle Programme, die direkt auf ChromeOS laufen, wurden automatisch
installiert. Die Android Apps musste ich manuell anstoßen.

Linux habe ich erstmal eingerichtet und dann ein Update durchgeführt.
 ```bash
$ sudo apt update && sudo apt upgrade -y
 ```

 Anschließend kann man wie gewohnt über *apt* die gewünschten
 Programme installieren.

 Auf dem neuen Chromebook laufen die virtuellen Umgebungen für
 Android und Linux natürlich deutlich flüssiger.
 Auf dem alten Gerät hat VSCode gerne mal 30 bis 40 Sekunden gebraucht
 für den Start. Auf dem neuen Gerät sind es nur noch wenige Sekunden.
 Da merkt man die stärkere CPU und den doppelten Arbeitsspeicher dann
 sofort.

 ## Alltag
 Die ersen paar Tage im Alltag hat das Gerät nun auch hinter sich 
 und die mehr Leistung macht das Erlebnis doch angenehmer und
 alles läuft besser von der Hand.