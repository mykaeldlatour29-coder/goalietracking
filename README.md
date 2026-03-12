# Goalie Tracker 🏒

Application web pour entraîneurs de gardiens de hockey — analyse des tirs en temps réel.

## Fonctionnalités

- Connexion / création de compte (Firebase Auth)
- Gestion de plusieurs gardiens
- Création de séances d'entraînement
- Enregistrement des tirs : cliquez sur la patinoire (origine) + le filet (destination) + résultat
- Carte thermique des zones du filet
- Statistiques SV%, buts, arrêts par zone
- Sauvegarde automatique dans Firebase Firestore
- Design sportif rouge/noir

---

## Setup Firebase

### 1. Créer un projet Firebase

1. Allez sur https://console.firebase.google.com
2. Cliquez **Ajouter un projet**
3. Donnez un nom (ex: `goalie-tracker`)
4. Désactivez Google Analytics (optionnel)

### 2. Activer Authentication

1. Dans Firebase Console → **Authentication** → **Commencer**
2. Onglet **Sign-in method** → Activez **Email/Password**

### 3. Activer Firestore

1. Dans Firebase Console → **Firestore Database** → **Créer une base de données**
2. Choisissez **Mode production**
3. Sélectionnez une région (ex: `us-central1` ou `northamerica-northeast1`)

### 4. Configurer les règles Firestore

Dans **Firestore → Règles**, collez le contenu du fichier `firestore.rules` :

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /sessions/{sessionId} {
      allow read, write: if request.auth != null && request.auth.uid == resource.data.uid;
      allow create: if request.auth != null && request.auth.uid == request.resource.data.uid;
    }
    match /goalies/{goalieId} {
      allow read, write: if request.auth != null && request.auth.uid == resource.data.uid;
      allow create: if request.auth != null && request.auth.uid == request.resource.data.uid;
    }
  }
}
```

### 5. Récupérer la config Firebase

1. Firebase Console → ⚙️ **Paramètres du projet** → **Vos applications**
2. Cliquez **</>** (Web) → Donnez un nom → **Enregistrer l'application**
3. Copiez l'objet `firebaseConfig`

### 6. Mettre à jour la config dans le code

Ouvrez `src/firebase.js` et remplacez les valeurs :

```js
const firebaseConfig = {
  apiKey: "VOTRE_API_KEY",
  authDomain: "VOTRE_PROJECT.firebaseapp.com",
  projectId: "VOTRE_PROJECT_ID",
  storageBucket: "VOTRE_PROJECT.appspot.com",
  messagingSenderId: "VOTRE_SENDER_ID",
  appId: "VOTRE_APP_ID"
};
```

---

## Déploiement sur Netlify

### Option A — Via GitHub (recommandé)

1. Poussez ce projet sur GitHub
2. Allez sur https://app.netlify.com → **Add new site** → **Import from Git**
3. Connectez votre repo GitHub
4. Build settings :
   - **Build command**: `npm run build`
   - **Publish directory**: `build`
5. Cliquez **Deploy site**

### Option B — Drag & Drop

1. Localement : `npm run build`
2. Allez sur https://app.netlify.com → **Add new site** → **Deploy manually**
3. Glissez le dossier `build/` dans Netlify

### Autoriser le domaine Netlify dans Firebase

Après déploiement :
1. Firebase Console → **Authentication** → **Settings** → **Authorized domains**
2. Cliquez **Add domain**
3. Ajoutez votre URL Netlify (ex: `goalie-tracker.netlify.app`)

---

## Développement local

```bash
npm install
npm start
```

L'app tourne sur http://localhost:3000

---

## Structure du projet

```
src/
  App.js              # Routing principal
  firebase.js         # Config Firebase
  index.js            # Point d'entrée React
  index.css           # Styles globaux
  components/
    Login.js          # Page de connexion / inscription
    Dashboard.js      # Liste des séances et gardiens
    Session.js        # Interface d'enregistrement des tirs
public/
  index.html
netlify.toml          # Config Netlify (redirection SPA)
firestore.rules       # Règles de sécurité Firestore
```
