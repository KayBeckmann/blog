---
title: "Mai"
date: "2025-05-01T00:00:00Z"
draft: false
description: "Vorlage für Journals."
isStarred: false
layout: "monthly_overview"
---

# Monatsübersicht: Mai

Aktuell habe ich privat einiges um die Ohren. Leider fehlt mir im Moment die Zeit, um mich den IT-Themen zu widmen, denen ich mich eigentlich gerne widmen würde. Familie, Haus, Hof und Arbeit fordern momentan viel meiner Zeit.

Trotzdem geht es weiter.

## Aufgabenübersicht

| Priorität | Aufgaben                | Status         | Deadline | Verzögerungsstatus |
| --------- | ----------------------- | -------------- | -------- | ------------------ |
| Mittel    | [Portfolio](#portfolio) | In Bearbeitung | _offen_     | zurückgestellt          |
| Niedrig   | [Join](#join)           | In Bearbeitung | _offen_    | zurückgestellt          |
| Niedrig   | [ERP](#erp)             | wartend        | offen    | wartend            |
| Niedrig   | [PBS](#pbs)             | wartend        | offen    | wartend            |
| Niedrig   | [Journal](#journal)     | wartend        | offen    | wartend            |
| Niedrig   | [MyPet](#mypet)         | wartend        | offen    | wartend            |

## Statusübersicht

Aufgrund der aktuellen Situation habe ich alle Projekte vorerst zurückgestellt. Sobald ich wieder mehr Zeit zur Verfügung habe, werde ich mich diesen Projekten widmen.

Komplett auf Eis liegt die Sache aber nicht. Auch wenn ich derzeit nicht aktiv am Code arbeite und somit keinen digitalen Fortschritt aufzeigen kann, sammle ich offline in meinem Notizbuch weiterhin Ideen zu Strukturen, Layouts und der Architektur der Projekte. Was mir in den Sinn kommt, wird zunächst zu Papier gebracht. Eine spätere Bewertung ist natürlich noch möglich.

## MyPet

_MyPet_ soll eine einfache Anwendung werden, in der man möglichst viele Informationen zu seinen
Tieren hinterlegen kann, z. B.:

- Futtermenge und -häufigkeit
- Medizinische Geschichte
- Medikation
- usw.

Diese Informationen sollen mit Tierärzten oder anderen Personen teilbar sein, beispielsweise wenn eine Urlaubsvertretung ansteht.

### Aufbau

Auch wenn hier bisher wenig am Code passiert ist, habe ich mir bereits Gedanken zur Struktur gemacht. Das Projekt wird aktuell als Monorepo auf [GitHub](https://github.com/KayBeckmann/mypet) geführt. Der Plan sieht vor, das Projekt in drei Teilen aufzubauen:
1.  Backend
2.  Frontend für Tierbesitzer
3.  Frontend für Tierärzte

Das Backend soll mit ExpressJS entwickelt werden, während die Frontends mit Vue3 geplant sind. Ich erhoffe mir, dass ich gemeinsame Klassen innerhalb des Monorepos nutzen kann. Dadurch sollen die Daten konsistent zwischen Frontend und Backend ausgetauscht werden können.

Das gemeinsame Backend soll sicherstellen, dass sowohl Tierbesitzer als auch Tierärzte immer mit den aktuellsten Daten arbeiten.

Sollten mir noch Änderungen oder Erweiterungen einfallen, schreibe ich diese erst einmal in mein 
[Notizbuch](https://blog.kay-beckmann.de/posts/2025-03-23_notizbuch/).

## PBS

PBS steht für _Personal Balance Software_ – zu Deutsch: ein Haushaltsbuch.

Folgende Punkte möchte ich umsetzen:

1. Benutzerverwaltung
2. Geschützte Links im Backend durch Authentifizierung
3. Backend-Logik, die bestimmte Vorgänge überwacht und abfängt
4. Wiederkehrende Aufgaben im Backend automatisiert ausführen
5. Datenbankverwaltung in einer SQL-Datenbank
6. Grafische Aufbereitung der Daten in Diagrammen

Weitere Punkte werden sicher folgen, sobald ich mit dem Projekt beginne.

## Journal

Das Journal aus dem  
[Blogpost](https://blog.kay-beckmann.de/posts/2025-03-23_notizbuch/)  
über mein neues Notizbuch enthält die Einlagen, die ich aktuell verwende.  
Diese sind noch nicht final und werden weiterhin überarbeitet.  
Die neuen Versionen werde ich erneut zum Download anbieten, sobald ich zufrieden bin.

Ich überlege auch, eine gedruckte Version über KDP (Kindle Direct Publishing) anzubieten.

## Portfolio

Das Portfolio ist online, allerdings müssen noch einige Anpassungen vorgenommen werden.
Diese werden jedoch erst später umgesetzt, sobald die ersten Projekte online sind.

## Join

Meine kleine Hassliebe. Auf der einen Seite ist es ein schönes kleines Projekt,
mit dem man zeigen kann, was man erstellen kann.

Aber mir fehlt bei dem Projekt etwas der innere Antrieb, weil ich den Mehrwert des Projekts
für mich noch nicht sehe. Deshalb fällt es mir schwer, die Motivation dafür aufzubringen.

Ich habe mir nun von ChatGPT eine 30 Tage Challange erstellen lassen.
Diese soll mich motivieren das Projekt umzusetzten.
Für jeden Tag hat mir ChatGPT nun eine Aufgabe erstellt, die ich umsetzten muss, damit das Projekt
fertig gestellt wird.

## ERP

Ich habe schon lange den Wunsch ein ERP zu erstellen.
Das ist ein Projekt in dem man sehr umfangreich seinem "Spieltrieb" nachgehen kann.
Es gibt so viele potentielle Funktionen, die man dort einbauen kann.

Bis jetzt habe ich folgende Idee zur Aufteilung:

- Frontend
- Backend
- SQL Datenbank

**Frontend**:

Das Frontend beinhaltet erstmal nur den Login.

**Backend**:

Im Backend kann der Admin sich anmelden und neue Benutzer anlegen.

**Datenbank**:

Die Datenbank wird schrittweise mit dem ERP erweitert. Die Tabellen werde
von den Klassen im Backend erstellt und verwaltet.

# Kaffee

Über einen
[Kaffee](https://www.buymeacoffee.com/snuppedelua)
würde ich mich auf jeden Fall freuen.