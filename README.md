# CRM Entreprise AR

Application web de visites techniques pour l'Entreprise AR, hébergée sur GitHub Pages avec Firebase (gratuit).

## Ce que fait l'application

- **Techniciens** sur téléphone : parcours en 5 étapes (Client → Existant → Travaux → Photos → Récap), avec photos cadrées (motif + modèle) et compte rendu automatique pour le devis.
- **Bureau** : tableau de bord, liste des visites, fiches client, menu d'administration des techniciens, export du compte rendu en un clic pour le CRM ou WhatsApp.
- **Admin** : création et gestion des comptes techniciens directement depuis l'application (pas besoin de passer par Firebase).

## Installation en 20 minutes

### 1. Créer un projet Firebase (5 min)

1. Allez sur https://console.firebase.google.com
2. Cliquez sur **« Ajouter un projet »**, nommez-le `entreprise-ar-crm`
3. Désactivez Google Analytics (pas utile)
4. Dans le menu de gauche, cliquez sur **Authentification** → **Commencer** → activez **E-mail / Mot de passe**
5. Dans le menu, cliquez sur **Firestore Database** → **Créer une base de données** → mode **production** → région `eur3 (europe-west)`
6. Dans le menu, cliquez sur **Storage** → **Commencer** → mode **production** → même région

### 2. Récupérer la configuration (2 min)

1. En haut à gauche, cliquez sur l'engrenage ⚙️ → **Paramètres du projet**
2. En bas de l'onglet **Général**, section **Vos applications**, cliquez sur l'icône `</>` (web)
3. Nommez l'appli `AR CRM`, ne cochez pas l'hébergement, cliquez **Enregistrer l'application**
4. Vous obtenez un bloc de code `const firebaseConfig = { apiKey: "...", ... }` → copiez-le

### 3. Coller la configuration dans `index.html` (1 min)

Ouvrez `index.html`, cherchez `REMPLACEZ_PAR_VOTRE_API_KEY` et remplacez par vos valeurs :

```js
const firebaseConfig = {
  apiKey: "AIza…",            // votre valeur
  authDomain: "entreprise-ar-crm.firebaseapp.com",
  projectId: "entreprise-ar-crm",
  storageBucket: "entreprise-ar-crm.appspot.com",
  messagingSenderId: "1234…",
  appId: "1:1234…:web:…"
};
```

### 4. Appliquer les règles de sécurité (3 min)

Dans la console Firebase, menu **Firestore Database** → onglet **Règles** → remplacez tout par :

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {

    // Utilisateurs : chacun lit son propre profil, les admins lisent et écrivent tout
    match /users/{userId} {
      allow read: if request.auth != null && (request.auth.uid == userId || isAdmin());
      allow create: if request.auth != null && request.auth.uid == userId;
      allow update, delete: if isAdmin();
    }

    // Visites : les techniciens accèdent aux leurs, les admins à toutes
    match /visites/{visiteId} {
      allow read: if request.auth != null && (resource.data.createdBy == request.auth.uid || isAdmin());
      allow create: if request.auth != null;
      allow update, delete: if request.auth != null && (resource.data.createdBy == request.auth.uid || isAdmin());
    }

    function isAdmin() {
      return request.auth != null && get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role == "admin";
    }
  }
}
```

Cliquez sur **Publier**.

Puis menu **Storage** → onglet **Règles** → remplacez par :

```
rules_version = '2';
service firebase.storage {
  match /b/{bucket}/o {
    match /photos/{visiteId}/{allPaths=**} {
      allow read, write: if request.auth != null;
    }
  }
}
```

Cliquez sur **Publier**.

### 5. Pousser sur GitHub (5 min)

1. Créez un dépôt sur https://github.com, par exemple `entreprise-ar-crm`
2. Déposez les trois fichiers (`index.html`, `logo.jpg`, `README.md`) à la racine
3. Dans l'onglet **Settings** du dépôt → **Pages** → **Branch** → `main` → **/root** → **Save**
4. Au bout de 2 minutes, le site est en ligne à `https://<votre-login>.github.io/entreprise-ar-crm/`

### 6. Créer votre compte admin (1 min)

1. Ouvrez l'URL du site en ajoutant `#setup` à la fin : `https://<votre-login>.github.io/entreprise-ar-crm/#setup`
2. Remplissez le formulaire (votre nom, votre e-mail pro, un mot de passe)
3. Vous êtes automatiquement **Administrateur** (parce que vous êtes le premier)
4. À partir de maintenant, vous ajoutez les techniciens depuis le menu **Techniciens** de l'application. L'adresse `#setup` ne doit plus être partagée.

## Utilisation quotidienne

- Les techniciens ouvrent le lien sur leur téléphone, se connectent, et remplissent les visites.
- Vous (bureau) voyez tout dans **Tableau de bord** et **Visites**, filtrez par statut, copiez le compte rendu, marquez « devis fait ».
- Dans **Techniciens**, vous pouvez ajouter, désactiver, réactiver un compte, ou envoyer un lien pour réinitialiser le mot de passe.

## Coûts

- **GitHub Pages** : gratuit, illimité.
- **Firebase** (plan Spark gratuit) : largement suffisant pour une équipe de 20 personnes :
  - 50 000 lectures / jour
  - 20 000 écritures / jour
  - 1 Go de stockage de photos
  - 10 Go de téléchargement / mois

Au-delà, le plan Blaze facture au vrai usage et resterait sous 5 € / mois pour votre taille.

## Mettre à jour l'application

Modifiez `index.html` sur GitHub (bouton crayon de l'interface web), validez les modifications, le site est mis à jour en 2 minutes.

## En cas de souci

- **« Configuration requise » à l'ouverture** → vous avez oublié de coller les valeurs Firebase dans `index.html`.
- **« Problème d'accès » après connexion** → les règles Firestore ne sont pas en place (étape 4).
- **Un technicien ne reçoit pas son e-mail** → vérifiez le dossier spam ou renvoyez le lien depuis la page **Techniciens**.
- **Un fichier ne s'envoie pas** → vérifiez que Storage est activé (étape 1.6) et que ses règles sont bien publiées.

---

🤖 Généré avec [Claude Code](https://claude.com/claude-code)

https://claude.ai/code/session_0199hfXqwymit9JPpaR2K3Wm
