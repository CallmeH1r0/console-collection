# 🎮 Konsolen-Sammlung

Eine persönliche Website zum Verwalten deiner Spielekonsolen-Sammlung – mit Live-Marktpreisen von eBay, Reparatur-Markierungen und automatischer Gesamtwert-Berechnung.


---

## Features

- ✅ Alle gängigen Konsolen verwalten (Nintendo, PlayStation, Xbox)
- 🟢 Konsolen als „Im Besitz" markieren
- 🔧 Konsolen als „Zur Reparatur" markieren
- 💜 Live-Marktpreise von eBay (täglich automatisch aktualisiert)
- 💰 Automatischer Gesamtwert deiner Sammlung
- 🔍 Suche & Filter
- 🔒 Admin-Login zum Bearbeiten

---

## Voraussetzungen

Alles komplett **kostenlos**:

- [GitHub](https://github.com) Konto
- [Firebase](https://firebase.google.com) Konto
- [eBay Developer](https://developer.ebay.com) Konto
- [Google](https://google.com) Konto (für Apps Script)

---

## Schritt 1 – Repository einrichten

1. Dieses Repository **forken** (oben rechts auf „Fork" klicken)
2. Im geforkten Repo auf **Settings → Pages** gehen
3. Source: **Deploy from a branch** → Branch: `main` → Ordner: `/ (root)`
4. Speichern – deine Seite ist jetzt unter `https://DEINNAME.github.io/REPONAME` erreichbar

---

## Schritt 2 – Firebase einrichten

### 2a. Projekt erstellen
1. Geh auf [console.firebase.google.com](https://console.firebase.google.com)
2. **„Projekt hinzufügen"** → Name vergeben → Erstellen
3. Im Projekt: **Realtime Database → Erstellen** → Region: `europe-west1` → **Im Testmodus starten**

### 2b. Datenbankregeln setzen
Unter **Realtime Database → Regeln** alles ersetzen mit:

```json
{
  "rules": {
    "counts":       { ".read": true, ".write": "auth != null" },
    "repair":       { ".read": true, ".write": "auth != null" },
    "marketPrices": { ".read": true, ".write": true }
  }
}
```

Auf **„Veröffentlichen"** klicken.

### 2c. Admin-Benutzer anlegen
1. Im Firebase-Projekt: **Authentication → Jetzt loslegen**
2. **E-Mail/Passwort** aktivieren
3. Unter **Benutzer → Benutzer hinzufügen** → deine E-Mail + Passwort eingeben

### 2d. Firebase-Config kopieren
1. Im Projekt: **Projekteinstellungen** (⚙️) → **Allgemein → Deine Apps → Web-App hinzufügen**
2. App registrieren → du bekommst einen `firebaseConfig`-Block

---

## Schritt 3 – index.html anpassen

Öffne `index.html` auf GitHub (✏️ Bleistift-Symbol) und ersetze den `firebaseConfig`-Block mit deinen eigenen Werten:

```javascript
const firebaseConfig = {
  apiKey: "DEIN_API_KEY",
  authDomain: "DEIN_PROJEKT.firebaseapp.com",
  databaseURL: "https://DEIN_PROJEKT-default-rtdb.europe-west1.firebasedatabase.app",
  projectId: "DEIN_PROJEKT",
  storageBucket: "DEIN_PROJEKT.firebasestorage.app",
  messagingSenderId: "DEINE_SENDER_ID",
  appId: "DEINE_APP_ID"
};
```

**„Commit changes"** klicken.

---

## Schritt 4 – eBay API einrichten

### 4a. Developer Account erstellen
1. Geh auf [developer.ebay.com](https://developer.ebay.com) → kostenlos registrieren
2. **„Create Application"** → App-Name vergeben
3. Unter **My Account → Application Access** auf **„Apply for exemption"** klicken
   - Begründung: *„Personal hobby project, no eBay user data is stored or processed"*
   - Wird meist sofort genehmigt
4. **Production Keys** kopieren: **App ID (Client ID)** und **Cert ID (Client Secret)**

> ⚠️ Nur die **Production Keys** verwenden, nicht Sandbox!

---

## Schritt 5 – Google Apps Script einrichten

### 5a. Script erstellen
1. Geh auf [script.google.com](https://script.google.com) → **„Neues Projekt"**
2. Öffne die Datei `apps-script/updatePrices.gs` aus diesem Repository
3. Kopiere den gesamten Inhalt in den Apps Script Editor (altes löschen)

### 5b. Eigene Zugangsdaten eintragen
Ersetze ganz oben im Script die drei Platzhalter:

```javascript
const FIREBASE_URL       = "https://DEIN-PROJEKT-default-rtdb.europe-west1.firebasedatabase.app";
const EBAY_CLIENT_ID     = "DEINE_PRODUCTION_CLIENT_ID";
const EBAY_CLIENT_SECRET = "DEIN_PRODUCTION_CLIENT_SECRET";
```

### 5c. Einmal manuell testen
- Oben `updatePrices` auswählen → **▶ Ausführen** klicken
- Im Log sollten Preise erscheinen, z.B. `gbasp: 75`
- In der Firebase Console unter `marketPrices/` sollten Werte erscheinen

### 5d. Automatischen Zeitplan einrichten
1. In der linken Seitenleiste auf **⏰ Trigger** klicken
2. **„+ Trigger hinzufügen"**
3. Funktion: `updatePrices` → Zeitbasiert → **Täglich** → zwischen 03:00–04:00 Uhr
4. Speichern

Die Preise werden jetzt jeden Tag automatisch aktualisiert.

---

## Konsolen anpassen

Möchtest du andere Konsolen hinzufügen oder entfernen? Bearbeite in `index.html` die `<li data-id="...">` Einträge und füge die entsprechenden IDs auch in der `CONSOLES`-Liste im Apps Script hinzu.

---

## Admin-Modus

Um Änderungen an deiner Sammlung vorzunehmen:

1. Auf der Website ganz nach unten scrollen
2. E-Mail und Passwort eingeben (die du in Firebase angelegt hast)
3. **Login** klicken
4. Jetzt erscheinen **+/−** Knöpfe zum Zählen und ein **🔧** Knopf zum Markieren von Reparaturen

---

## Technischer Aufbau

```
index.html              → Die komplette Website (HTML, CSS, JS in einer Datei)
apps-script/
  updatePrices.gs       → Google Apps Script für tägliche eBay-Preisabfragen
```

| Dienst | Verwendung | Kosten |
|---|---|---|
| GitHub Pages | Hosting der Website | kostenlos |
| Firebase Realtime Database | Speicherung von Sammlung & Preisen | kostenlos |
| Firebase Authentication | Admin-Login | kostenlos |
| eBay Browse API | Marktpreise abrufen | kostenlos |
| Google Apps Script | Täglicher Preis-Abruf | kostenlos |

**Gesamt: 0 €/Monat** ✅

---

## Lizenz

MIT – mach damit was du willst! Ein ⭐ auf dem Repo freut mich aber 😄
