---
title: "Join Backend"
date: "2025-01-23T00:00:00Z"
draft: false
description: "Join"
isStarred: false
---

# Entwicklung eines Task-Management-Backends mit ExpressJS
Dieser Beitrag wurde von ChatGPT erstellt.

## Einleitung
In diesem Beitrag erläutern wir die Entwicklung eines Task-Management-Backends mit ExpressJS und einer SQL-Datenbank. Während der Entwicklung nutzen wir SQLite, später wird die Umstellung auf MySQL erfolgen. Das Backend stellt eine Registrierungs- und Login-API bereit, verwendet JWT zur Authentifizierung und bietet die Möglichkeit zur Verwaltung von Tasks und Kontakten.

## Projektstruktur
Die Projektstruktur ist wie folgt organisiert:

```
backend/
│-- node_modules/
│-- src/
│   │-- config/              # Konfigurationsdateien
│   │-- controllers/         # API-Logik
│   │-- middleware/          # Auth-Middleware
│   │-- models/              # Datenbankmodelle
│   │-- routes/              # API-Routen
│   │-- services/            # Geschäftslogik
│   │-- utils/               # Hilfsfunktionen
│   │-- app.js               # Express-App
│   │-- server.js            # Server-Startpunkt
│-- .env                     # Umgebungsvariablen
│-- package.json             # Paketverwaltung
│-- README.md                # Dokumentation
```

## Technologien
- **ExpressJS**: Framework für das Backend
- **SQLite / MySQL**: Datenbank für Entwicklung und Produktion
- **Sequelize**: ORM für SQL-Datenbanken
- **bcrypt**: Hashing von Passwörtern
- **jsonwebtoken (JWT)**: Authentifizierung
- **dotenv**: Verwaltung von Umgebungsvariablen
- **axios**: HTTP-Anfragen im Frontend

## Implementierte Module
### 1. Authentifizierung
Die Authentifizierung erfolgt über JWT-Tokens. Folgende Endpunkte stehen zur Verfügung:
- POST */auth/register* – Registrierung neuer Benutzer
- POST */auth/login* – Benutzeranmeldung
- GET */auth/me* – Abruf eigener Profildaten

#### Middleware zur Authentifizierung:
```javascript
const jwt = require('jsonwebtoken');

module.exports = (req, res, next) => {
  const token = req.header('Authorization');
  if (!token) {
    return res.status(401).json({ message: 'Kein Token, Zugriff verweigert' });
  }

  try {
    const decoded = jwt.verify(token.split(' ')[1], process.env.JWT_SECRET);
    req.user = decoded;
    next();
  } catch (err) {
    res.status(401).json({ message: 'Token ungültig' });
  }
};
```

### 2. Task-Verwaltung
Jeder Task verfügt über folgende Eigenschaften:
- Titel
- Beschreibung
- Fälligkeitsdatum
- Kategorie
- Sachbearbeiter
- Priorität
- Subtasks
- Status (geplant, bearbeitung, prüfen, fertig)

#### Routen:
- POST */tasks* – Task anlegen
- GET */tasks* – Aufgaben abrufen
- PUT */tasks/:id* – Task bearbeiten
- DELETE */tasks/:id* – Task löschen

### 3. Dashboard
Das Dashboard liefert statistische Daten über Tasks:
- Anzahl aller Tasks
- Anzahl Tasks mit hoher Priorität
- Nächstes Fälligkeitsdatum

#### Route:
```
router.get('/', authMiddleware, DashboardController.getDashboardStats);
```
## Fehler und deren Behebung
### Fehler 1: "Cannot find module './routes/userRoutes'"
**Ursache:**
- Die Datei userRoutes.js war nicht im erwarteten Pfad vorhanden.

**Lösung:**
- Überprüfung der Projektstruktur und Sicherstellung, dass die Datei korrekt benannt und importiert wurde.

### Fehler 2: "401 Unauthorized" bei API-Anfragen
**Ursache:**
- Der JWT-Token wurde nicht korrekt übermittelt.

**Lösung:**
- Sicherstellen, dass der Token mit dem Präfix Bearer in den Header eingefügt wird:
````javascript
axios.post('/api/tasks', data, {
  headers: { Authorization: `Bearer ${authStore.token}` }
});
````

### Fehler 3: "Cannot read properties of null (reading 'parentNode')"
***Ursache:**
- Zugriff auf ein nicht vorhandenes DOM-Element.

**Lösung:**
- Überprüfung, ob das Element existiert, bevor darauf zugegriffen wird:
```javascript
if (element?.parentNode) {
  element.parentNode.insertBefore(newElement, element);
}
```
### Fehler 4: "404 Not Found" für das Dashboard
**Ursache:**
- API-Routen für das Dashboard waren nicht registriert.

**Lösung:**
- Einbindung der Dashboard-Routen in server.js:

```javascript
const dashboardRoutes = require('./routes/dashboardRoutes');
app.use('/api/dashboard', dashboardRoutes);
```
## Fazit
Durch den modularen Aufbau des Backends mit ExpressJS und einer klaren Trennung der Funktionalitäten konnte eine robuste Lösung geschaffen werden. Fehler wurden systematisch behoben, indem Debugging-Techniken und strukturiertes Testen zum Einsatz kamen.

Mit diesen Ansätzen ist das Task-Management-Backend gut skalierbar und bereit für den Einsatz in einer Produktionsumgebung.
