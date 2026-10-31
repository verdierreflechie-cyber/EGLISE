# ECIK — Plateforme communautaire web

Application web responsive construite à partir du document « Plan de travail et spécifications complètes — Projet ECIK ».

## Fonctionnalités présentes
- Connexion membre par nom complet et téléphone avec vérification Firebase Phone Authentication.
- Attribution par défaut du rôle `USER` à la création du profil.
- Espaces `PASTOR` et `ADMIN` protégés par rôle Firestore (les rôles privilégiés doivent être attribués par un administrateur de confiance dans Firebase).
- Médiathèque des cultes, étapes chronologiques, brouillons et publication.
- Publications pastorales et commentaires avec modération.
- Interface trilingue : français, lingala et anglais.
- Mise en cache locale des cultes et publications pour consultation ultérieure.
- Firebase Firestore, Authentication, Storage et point d'intégration FCM.

Aucun module de paiement, de dons ou de contributions n'est inclus.

## Démarrage

```bash
npm install
cp .env.example .env.local
# Renseigner les clés Firebase dans .env.local
npm run dev
```

## Configuration Firebase
1. Créer un projet Firebase et une application Web.
2. Activer Authentication > Phone.
3. Créer la base Cloud Firestore et appliquer `firestore.rules`.
4. Activer Cloud Storage et appliquer `storage.rules`.
5. Copier les paramètres de l'application Web dans `.env.local`, y compris la clé VAPID Web Push.
6. Pour les notifications push, configurer Firebase Cloud Messaging et la clé VAPID Web Push (`VITE_FIREBASE_VAPID_KEY`). L’application demande la permission lors de la première session membre et enregistre le token FCM dans le profil. Le service worker FCM et le backend d’envoi doivent être déployés avant production. Le navigateur doit être en HTTPS (ou localhost).
7. Attribuer `ADMIN` et `PASTOR` uniquement depuis un environnement backend de confiance (Firebase Admin SDK / Custom Claims). Ne jamais permettre à l'utilisateur de choisir ces rôles dans l'interface publique.

## Modèle Firestore
- `users/{uid}` : `fullName`, `phone`, `role` (`USER`, `ADMIN`, `PASTOR`), `fcmToken`, `createdAt`.
- `cultes/{id}` : `title`, `date`, `day`, `steps[]`, `status` (`DRAFT`, `PUBLISHED`), médias éventuels.
- `pastor_posts/{id}` : `title`, `body`, `author`, `createdAt`, `likes`, `comments[]`.

## Production

```bash
npm run build
npm run preview
```

Le dossier `dist/` contient les fichiers statiques à déployer sur Firebase Hosting ou un hébergeur compatible.

## Limites à compléter avant mise en production
- Déployer les règles Firestore/Storage et tester les scénarios d'accès avec Firebase Emulator Suite.
- Déplacer la modération et l'écriture des commentaires vers des Cloud Functions avec validation stricte, et éviter les tableaux de commentaires embarqués si le volume augmente.
- Mettre en place l'envoi FCM depuis un backend sécurisé et le service worker correspondant.
- Ajouter l'interface de téléversement des médias dans Storage et le téléchargement hors ligne explicite des fichiers audio/vidéo.
- Vérifier la conformité locale, la politique de confidentialité et les obligations relatives aux numéros de téléphone.

Le contenu de démonstration affiché lorsque Firebase n'est pas configuré est fictif et sert uniquement à prévisualiser l'interface.
