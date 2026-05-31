---
title: "OpenVault: Was beim Bauen einer eigenen Obsidian-Alternative alles schiefgehen kann"
date: "2026-05-31T00:00:00Z"
draft: false
description: "Ich baue eine selbst gehostete Markdown-Vault-App — und in einer Woche hat mich eine Handvoll Bugs gelehrt, dem eigenen Testkontext grundsätzlich zu misstrauen. AES-Keys mit 33 Bytes, JGit auf Android, R8 der ServiceLoader-Klassen wegoptimiert, und ein SSH-Fehler der gar nichts mit SSH zu tun hat."
isStarred: false
tags: ["Flutter", "Dart", "Android", "Docker", "SSH", "OpenVault", "self-hosted"]
---

# OpenVault: Was beim Bauen einer eigenen Obsidian-Alternative alles schiefgehen kann

Ich baue gerade **OpenVault** — eine selbst gehostete Alternative zu Obsidian Sync. Flutter-Frontend, Dart/Shelf-Backend, Docker. Die Idee: ein Obsidian-kompatibler Markdown-Editor der im Browser läuft und gleichzeitig als native App auf Android und Linux verfügbar ist. Kein proprietäres Cloud-Abo, volle Kontrolle über die Daten, Git als Sync-Mechanismus.

Das Projekt läuft seit ein paar Tagen und ist über das "es compiliert"-Stadium hinaus. Was ich dabei gelernt habe, ist zu gut um es in einem Git-Commit zu begraben — denn alle diese Bugs hatten eine Eigenschaft gemeinsam: **sie traten nur unter bestimmten Bedingungen auf**. Lokal lief alles. Woanders nicht.

→ Repo: https://github.com/KayBeckmann/OpenVault

---

## "error in libcrypto" — und es hat nichts mit meinem Crypto-Code zu tun

Beim ersten echten SSH-Clone-Versuch über die Web-App kam dieser Fehler:

```
Clone failed: error in libcrypto
```

Mein erster Reflex: irgendwas stimmt an meiner AES-Verschlüsselung nicht. Stundenlange Suche. Nichts gefunden.

Die tatsächliche Ursache war vollkommen woanders. `ssh-keygen` erzeugt seit OpenSSH 6.5 standardmäßig das neuere **OPENSSH-Format**:

```
-----BEGIN OPENSSH PRIVATE KEY-----
```

Ich nutze `GIT_SSH_COMMAND="ssh -i /tmp/key"` um Git mit dem gespeicherten Private Key zu starten. Und der SSH-Client im Container — eine ältere libcrypto-Version — kann das OPENSSH-Format nicht laden.

**Fix:** `-m PEM` beim Generieren erzwingt das klassische RSA-Format:

```bash
ssh-keygen -t rsa -b 4096 -m PEM -f /tmp/key -N '' -C "label"
```

Das erzeugt `-----BEGIN RSA PRIVATE KEY-----` — universell kompatibel. Steht nirgendwo in der Flutter/Dart-Doku. Weiß ich jetzt.

Außerdem: der Private Key wird nach der git-Operation sofort gelöscht. Temp-Verzeichnis anlegen, Key reinschreiben (chmod 600), git ausführen, Verzeichnis in einem `finally`-Block löschen.

---

## AES-256 schlägt auf dem Server fehl, lokal läuft alles

SSH-Keys werden bei mir at-rest mit AES-256-GCM verschlüsselt. Der Schlüssel kommt aus der Umgebungsvariable `ENCRYPTION_KEY`. Lokal: alles gut. Auf dem Server: 500.

Mein Code war:

```dart
final keyBytes = Uint8List.fromList(
  utf8.encode(envKey.padRight(32, '!').substring(0, 32))
);
```

`substring(0, 32)` schneidet an **Zeichenpositionen**, nicht Byte-Positionen. Ein `ü` ist ein Zeichen, aber zwei UTF-8-Bytes (`0xC3 0xBC`). `utf8.encode` dieser 32-Zeichen ergibt dann **33 Bytes** — und AES-256 verlangt exakt 32.

Ich hatte auf dem Server beim Ausfüllen der `.env` ein Passwort mit Umlaut benutzt. Lokal hatte mein `ENCRYPTION_KEY` keinen Umlaut. Kein Bug lokal, Bug auf dem Server.

**Fix:** Auf Byte-Ebene arbeiten:

```dart
final rawBytes = utf8.encode(envKey);
final keyBytes = Uint8List(32);
for (var i = 0; i < 32; i++) {
  keyBytes[i] = i < rawBytes.length ? rawBytes[i] : 0x21;
}
```

Nimmt die ersten 32 **Bytes** des UTF-8-encodierten Strings. Zu kurz? Mit `0x21` (`!`) auffüllen.

---

## Android ohne Termux: Git via JGit

Die erste Android-Version zeigte beim Clone-Versuch:

> "git ist auf diesem Gerät nicht verfügbar."

Die naive Lösung wäre `Process.run('git', ...)` — aber das setzt Termux voraus. Eine externe Abhängigkeit für die Kernfunktionalität wollte ich nicht akzeptieren.

**JGit** ist eine pure Java-Implementierung von Git, die auf Android läuft. Die Architektur:

```
Flutter (Dart)              Android (Kotlin)
GitChannel.clone()   →   MethodChannel  →   JGit 6.7
GitChannel.pull()    ←   (Ergebnis)     ←   Apache MINA sshd 2.10
GitChannel.push()
```

Auf Android werden alle Git-Operationen über den MethodChannel an Kotlin delegiert. Auf Linux/Windows bleibt es bei `Process.run('git', ...)`.

Drei nicht-offensichtliche Probleme beim JGit-Setup:

**Problem 1 — Package-Verwechslung.** `TransportConfigCallback` liegt in `org.eclipse.jgit.api`, nicht in `org.eclipse.jgit.transport`. Der Compiler sagt "Unresolved reference" ohne Hinweis aufs richtige Package. Die Dokumentation für ältere JGit-Versionen macht es noch schlimmer.

**Problem 2 — API-Signatur.** `SshdSessionFactoryBuilder.build()` nimmt einen `KeyCache`-Parameter. `build(null)` ist der korrekte Aufruf für kein Caching — aber das findet man nicht auf Anhieb.

**Problem 3 — R8 strippt ServiceLoader-Klassen.** Apache MINA SSHD findet SSH-Key-Parser über `ServiceLoader` zur Laufzeit. R8 (Androids Code-Minifier) sieht diese Referenzen nicht statisch und entfernt die Klassen. Das führt zur Laufzeit zu einem `NoClassDefFoundError`.

Und weil ich `catch (e: Exception)` statt `catch (t: Throwable)` hatte, wurde dieser Error still geschluckt. Der MethodChannel-`result` wurde nie aufgerufen. Der Dart-`await` wartete ewig. Die App zeigte einen Spinner der nie aufhörte.

**Fixes:**
- `catch (t: Throwable)` — fängt alle JVM-Errors
- `-keep class org.eclipse.jgit.** { *; }` und `-keep class org.apache.sshd.** { *; }` in `proguard-rules.pro`
- 30-Sekunden-Timeout für alle Git-Operationen

---

## Android-Pfade und `MANAGE_EXTERNAL_STORAGE`

Mein erster Versuch für den Standard-Vault-Pfad auf Android:

```dart
final idx = path.indexOf('/Android/data/');
return '${path.substring(0, idx)}/OpenVault';
// → /storage/emulated/0/OpenVault
```

Klingt sinnvoll. Ist es nicht. `/storage/emulated/0/OpenVault` liegt im **gemeinsamen externen Speicher**. Seit Android 10 braucht man dafür `MANAGE_EXTERNAL_STORAGE` — eine Sonderberechtigung, die manuell in den Systemeinstellungen aktiviert werden muss und in der Play Store Policy als "special access" gilt.

**Fix:** App-spezifisches External Storage nutzen, das keine Berechtigung braucht:

```dart
final ext = await getExternalStorageDirectory();
// → /storage/emulated/0/Android/data/de.kaybeckmann.app/files/vaults
```

Die App hat vollständigen Zugriff auf dieses Verzeichnis — ohne jede Permission-Anfrage.

---

## Flutter-SDK-Konflikt auf Arch Linux

Noch ein Nebeneffekt beim APK-Build:

```
Unexpected Kernel Format Version 127 (expected 130)
platform_strong.dill: kernel format mismatch
```

Das Arch-AUR-Paket `flutter` hatte eine `platform_strong.dill` die mit einem älteren Dart für Kernelformat 127 kompiliert wurde — mein System-Dart war aber 3.12.0 (Format 130). Die Artefakte lagen in root-owned Verzeichnissen, `sudo flutter precache` half nicht weiter.

**Fix:** Flutter direkt aus dem offiziellen Repository installieren statt per AUR:

```bash
git clone https://github.com/flutter/flutter.git -b stable --depth 1 ~/flutter-sdk
echo 'export PATH="$HOME/flutter-sdk/bin:$PATH"' >> ~/.bashrc
flutter precache --android
```

Self-consistent, kein Package-Manager-Konflikt, keine root-owned Caches.

---

## Wikilinks auflösen wenn Namen mehrdeutig sind

Ein Obsidian-Vault hat viele `README.md`-Dateien — eine pro Ordner. Ein `[[README]]`-Link soll auf die "nächste" README zeigen, nicht irgendeine.

Mein erster Algorithmus: Tiefensuche im Baum, erster Treffer gewinnt. Das war deterministisch — aber falsch, weil die Reihenfolge von der Alphabetik des Baums abhing.

**Neuer Ansatz: Proximity-Scoring**

Wenn mehrere Dateien denselben Namen haben, wird die Nähe zur aktuell geöffneten Datei als Score berechnet:

```dart
int proximity(String candidateDir, String currentDir) {
  if (candidateDir == currentDir)          return 1000; // gleicher Ordner
  if (currentDir.startsWith(candidateDir)) return 500;  // Vorfahre
  if (candidateDir.startsWith(currentDir)) return 400;  // Nachfahre
  return sharedSegments(candidateDir, currentDir); // gemeinsamer Präfix
}
```

Der Wikilink-Dialog speichert jetzt außerdem den **vollen Pfad** für mehrdeutige Namen. Wenn ich `README` im Dialog auswähle und es drei davon gibt, wird `[[10_Projects/OpenVault/README]]` eingefügt statt `[[README]]`. Eindeutige Namen bleiben `[[Dateiname]]`.

---

## Was nach einer Woche bleibt

Jeder dieser Bugs hatte dieselbe Wurzel: **den eigenen Testkontext misstrauen**. Lokal ohne Umlaut, auf dem Server mit Umlaut. Android mit Termux, Android ohne Termux. Eindeutiger Dateiname, mehrdeutiger Dateiname.

"Es läuft" ist keine hinreichende Testbedingung für alles was schiefgehen könnte.

OpenVault ist jetzt auf Android, Linux und im Browser lauffähig — mit Git-Sync, Markdown-Editor, SSH-Keys und Docker-Deployment. Wer reinschauen will: https://github.com/KayBeckmann/OpenVault
