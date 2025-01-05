---
title: "Ansible"
date: "2025-01-05T00:00:00Z"
draft: false
description: "Ansible."
isStarred: false
---

# Ansible

Ansible ist ein Programm, mit dem bestimmte Aufgaben automatisiert und immer wieder auf die gleiche Weise ausgeführt
werden können – eine Art _Stapelverarbeitung_.

Das Ganze funktioniert so, dass man **Playbooks** erstellt. In diesen **Playbooks** definiert man einfache _Tasks_
(Aufgaben), die abgearbeitet werden sollen. Zum Beispiel könnte ein Task darin bestehen, Programme zu installieren.
Diesem Task kann man ein einzelnes Programm oder eine Liste von Programmen übergeben, die anschließend installiert
werden.

Für einen einzelnen Rechner mag das wenig sinnvoll erscheinen. Der wahre Nutzen zeigt sich jedoch, wenn man die
IT-Infrastruktur eines Unternehmens betrachtet.

Ein Administrator kann für verschiedene Abteilungen unterschiedliche Tasks oder Listen anlegen. Zum Beispiel benötigt
die Konstruktionsabteilung andere Software als die Buchhaltung, das Lager oder die Monteure. Dadurch ist gewährleistet,
dass ein neuer Mitarbeiter in einer Abteilung dieselbe Software und Konfiguration erhält wie seine Kollegen.

## Kleines Beispiel

Ich experimentiere gerne mit meinem System und teste regelmäßig neue Software. Wenn ich dabei mein Testsystem wieder
einmal zerschossen habe, installiere ich das Basissystem neu und starte anschließend mein **Playbook**
_Grundeinrichtung_. Dieses führt verschiedene Tasks aus, wie z. B.:

- SSH-Key erstellen
- SSH-Key im Netzwerk veröffentlichen
- Eine Liste von Programmen installieren
- Grundeinstellungen für den Desktop kopieren
- E-Mail-Konten in Thunderbird einrichten

Zudem habe ich ein zweites **Playbook**, mit dem ich alle meine virtuellen Maschinen (VMs) aktualisieren kann. Dadurch
muss ich nicht jede VM manuell aktualisieren, sondern kann mit einem einzigen **Playbook** alle Server gleichzeitig
updaten.

## Blog

Für meinen Blog möchte ich mit einem einzigen Befehl Folgendes erreichen:
Die neueste Version soll in _Git_ committed und anschließend zu GitHub gepusht werden. Der Server soll dann die
aktuellste Version pullen und den _Build_-Prozess ausführen.

Dafür habe ich die Aufgaben in verschiedene Dateien unterteilt:

- `blog-playbook.yml`
- `local.yml`
- `remote.yml`
- `inventory`

### Blog-Playbook

Dieses **Playbook** lädt lediglich die beiden anderen Dateien (`local.yml` und `remote.yml`).

### Local

In diesem **Playbook** werden die Änderungen zu GitHub übertragen.

```yml
---
- name: Local tasks - Git commit and push
  hosts: localhost
  vars:
    local_repo_path: "/PATH/TO/REPO"
  tasks:
    - name: Commit changes to local repository
      shell: |
        ./upload.sh
      args:
        chdir: "{{ local_repo_path }}"
```

| Parameter | Beschreibung                                                                                               |
| --------- | ---------------------------------------------------------------------------------------------------------- |
| **name**  | Der Name der Task-Zusammenstellung                                                                         |
| **hosts** | Der Host, auf dem die Tasks ausgeführt werden sollen. Kann auch aus der _inventory_-Datei übergeben werden |
| **vars**  | Variablen, die in den Tasks verwendet werden können                                                        |
| **tasks** | Die Aufgaben, die abgearbeitet werden sollen. Es können mehrere Tasks untereinander definiert werden.      |

Bei **YML**-Dateien ist die korrekte Einrückung entscheidend. Mische niemals Tabs und Leerzeichen! Außerdem sollte die
Einrückung in der gesamten Datei einheitlich sein.

### Remote

Dieses **Playbook** lädt die Daten auf den Server herunter, erstellt das Docker-Image neu und startet anschließend den
Container mit dem aktualisierten Image.

### Inventory

In dieser Datei sind alle Hosts aufgeführt, auf die zugegriffen werden soll.

```bash
[localhost]
127.0.0.1 ansible_connection=local

[remote_server]
192.168.178.250 ansible_user=user ansible_ssh_private_key_file=/PFAD/ZU/SSH/KEY
```

Die Hosts werden mit ihrer IP-Adresse angegeben. Anschließend definiert man den Benutzer, mit dem auf das jeweilige
System zugegriffen werden soll. Am Ende wird der Pfad zum SSH-Key angegeben, der für die Authentifizierung verwendet
wird.

In eckigen Klammern können Systeme zu Gruppen zusammengefasst werden. Zum Beispiel könnten folgende Gruppen definiert
werden:

- Konstruktion
- Buchhaltung
- Lager
- Webserver

Die Gruppen lassen sich nach den eigenen Anforderungen benennen und organisieren.

## Fazit

Dieser Beitrag ist die Feuertaufe für mein **Playbook**. Ich schreibe den Artikel und möchte ihn direkt mit dem neuen
**Playbook** hochladen. Wenn ihr diesen Beitrag lesen könnt, hat alles wie geplant funktioniert.
