# Concours de la meilleure affiche – Fête de l’Unité

Site du club **Digital Craft** (École Normale Supérieure de Marrakech) pour le concours de la meilleure affiche, du 2 au 4 novembre.

Les participants saisissent leur nom, décrivent leur affiche et la téléversent. Les affiches s’affichent aussitôt dans une galerie partagée. Le site est bilingue (français et arabe), avec l’hymne national et « Sawt Al-Hassan » en fond sonore, et un bouton pour couper le son.

Temps nécessaire : environ 15 à 20 minutes. Aucun serveur à louer, tout est gratuit (GitHub Pages et Firebase, plan Spark).

---

## 1. Contenu du dépôt

```
index.html                      le site complet (page, style, code)
firestore.rules                 règles de sécurité de la base de données
README.md                       ce guide
assets/digital-craft-logo.png   logo du club
assets/ens-logo.png             logo de l’ENS
audio/hymne-national.mp3        à ajouter (voir étape 6)
audio/sawt-al-hassan.mp3        à ajouter (voir étape 6)
```

Les noms de fichiers doivent être **exactement** ceux-ci, en minuscules : GitHub distingue majuscules et minuscules.

## 2. Comment ça marche

GitHub Pages ne sait qu’afficher des pages fixes, il ne peut pas enregistrer les affiches envoyées. On utilise donc **Firebase Firestore**, une base de données en ligne gratuite :

- le navigateur du participant réduit l’image à 1000 px (environ 300 Ko) puis l’envoie dans Firestore ;
- la galerie lit Firestore en direct : une nouvelle affiche apparaît chez tout le monde sans recharger la page ;
- on n’utilise pas Firebase Storage, qui demande désormais un abonnement avec carte bancaire.

Chaque affiche est un document de la collection `affiches` :

| Champ | Type | Contenu |
|---|---|---|
| `nom` | texte | nom du participant (80 caractères max) |
| `description` | texte | description de l’affiche (600 caractères max) |
| `image` | texte | l’affiche en JPEG encodé en base64 |
| `createdAt` | horodatage | date d’envoi, ajoutée par le serveur |

## 3. Étape 1 – Mettre le site sur GitHub

1. Connectez-vous à github.com, puis cliquez sur **New** (nouveau dépôt).
2. Nom du dépôt : par exemple `affiches-unite`. Choisissez **Public**. Cliquez sur **Create repository**.
3. Cliquez sur **uploading an existing file** (ou *Add file → Upload files*).
4. Glissez `index.html`, `firestore.rules` et `README.md`.
5. Pour les dossiers : glissez le dossier `assets` entier. Il est conservé avec son arborescence. Faites de même avec `audio` quand vous aurez les morceaux.
6. Cliquez sur **Commit changes**.
7. Allez dans **Settings → Pages**. Sous *Build and deployment*, choisissez **Deploy from a branch**, branche **main**, dossier **/ (root)**, puis **Save**.
8. Attendez une à deux minutes. L’adresse s’affiche en haut de la page, de la forme :
   `https://VOTRE-COMPTE.github.io/affiches-unite/`

À ce stade le site s’ouvre déjà, en **mode test** : un bandeau jaune indique que les affiches restent sur l’appareil. C’est normal tant que Firebase n’est pas relié.

## 4. Étape 2 – Créer le projet Firebase

1. Allez sur **console.firebase.google.com** et connectez-vous avec un compte Google.
2. Cliquez sur **Créer un projet** (ou *Ajouter un projet*).
3. Nom : `digital-craft-affiches` (ou autre).
4. Désactivez **Google Analytics** (inutile ici), puis **Créer le projet**.

## 5. Étape 3 – Créer la base de données Firestore

1. Dans le menu de gauche : **Build → Firestore Database** (parfois *Créer → Firestore*).
2. Cliquez sur **Créer une base de données**.
3. Édition : *Standard*. Emplacement : un site européen (`eur3` ou `europe-west`). L’emplacement ne peut pas être changé ensuite.
4. Mode de démarrage : **Mode production**. Cliquez sur **Activer**.

Il n’y a rien d’autre à créer : la collection `affiches` apparaîtra toute seule au premier envoi.

## 6. Étape 4 – Publier les règles de sécurité

Sans règles, personne ne peut lire ni écrire. Les règles ci-dessous autorisent la lecture pour tous, l’ajout d’une affiche valide, et interdisent toute modification ou suppression depuis le site.

1. Dans Firestore, ouvrez l’onglet **Règles**.
2. Supprimez tout le contenu et collez celui du fichier `firestore.rules`.
3. Cliquez sur **Publier**.

Si vous réutilisez un projet Firebase existant, **n’effacez pas** vos règles actuelles : ajoutez seulement le bloc `match /affiches/{id} { ... }` à l’intérieur de `match /databases/{database}/documents { ... }`.

## 7. Étape 5 – Relier le site à Firebase

1. Dans Firebase, cliquez sur la roue dentée, puis **Paramètres du projet**.
2. Descendez à **Vos applications** et cliquez sur l’icône **`</>`** (Web).
3. Donnez un nom (par exemple `site-affiches`). **Ne cochez pas** Firebase Hosting. Cliquez sur **Enregistrer l’application**.
4. Firebase affiche un bloc `firebaseConfig`. Copiez ces quatre valeurs : `apiKey`, `authDomain`, `projectId`, `appId`.
5. Sur GitHub, ouvrez `index.html`, cliquez sur le **crayon** (modifier) et trouvez, vers le début du script, ce bloc :

```js
const FIREBASE_CONFIG = {
  apiKey: "COLLER_ICI",
  authDomain: "VOTRE-PROJET.firebaseapp.com",
  projectId: "VOTRE-PROJET",
  appId: "COLLER_ICI"
};
```

6. Remplacez les valeurs par les vôtres, par exemple :

```js
const FIREBASE_CONFIG = {
  apiKey: "AIzaSy…",
  authDomain: "digital-craft-affiches.firebaseapp.com",
  projectId: "digital-craft-affiches",
  appId: "1:123456789:web:abc123"
};
```

7. Cliquez sur **Commit changes**.

La clé `apiKey` n’est pas un mot de passe : elle identifie seulement le projet. Ce sont les règles de l’étape 4 qui protègent la base. Les autres champs du bloc Firebase (`storageBucket`, `messagingSenderId`) ne sont pas nécessaires.

## 8. Étape 6 – Ajouter la musique

Le site lit dans l’ordre `audio/hymne-national.mp3` puis `audio/sawt-al-hassan.mp3`, en boucle, à volume modéré. Je n’ai pas pu les inclure : procurez-vous des fichiers dont vous avez le droit de vous servir.

- Format : MP3. Poids conseillé : moins de 10 Mo chacun (128 kbps suffisent).
- Ajoutez-les dans un dossier `audio` du dépôt (*Add file → Upload files*).
- Pour changer les noms ou l’ordre, modifiez la liste `TRACKS` dans `index.html`.
- Si un fichier manque, le site passe au suivant ; si les deux manquent, il reste silencieux sans erreur visible.

Le son ne démarre qu’après un clic sur « Entrer avec le son » : les navigateurs interdisent la lecture automatique. Le bouton rond en haut à droite permet de couper ou remettre le son à tout moment.

## 9. Étape 7 – Tester

1. Ouvrez l’adresse du site, puis forcez le rechargement (**Ctrl + F5**, ou fermez et rouvrez l’onglet sur téléphone).
2. Le bandeau jaune « Mode test » ne doit plus apparaître.
3. Choisissez « Entrer avec le son » et vérifiez la musique.
4. Envoyez une affiche d’essai (JPG, PNG ou WebP).
5. Elle doit apparaître dans la galerie, et un clic doit l’ouvrir en grand avec le bouton Télécharger.
6. Dans Firebase, ouvrez **Firestore → Données** : la collection `affiches` contient un document avec `nom`, `description`, `image`, `createdAt`.
7. Testez aussi sur un téléphone, et avec le bouton **العربية** pour la version arabe.
8. Supprimez ensuite l’affiche d’essai (voir plus bas).

## 10. Pendant et après le concours

**Retirer une affiche** : Firebase → Firestore → Données → `affiches` → ouvrez le document → icône de suppression. Elle disparaît de la galerie en quelques secondes.

**Fermer les dépôts** : dans l’onglet Règles, remplacez la ligne `allow create: if …;` (toute la condition) par :

```
allow create: if false;
```

puis cliquez sur **Publier**. La galerie reste visible mais plus personne ne peut envoyer.

**Quotas gratuits** (plan Spark) : 1 Go de stockage, 50 000 lectures et 20 000 écritures par jour. Chaque visite lit toutes les affiches affichées (120 au maximum), donc 50 000 lectures par jour représentent environ 400 visites avec 120 affiches, ou bien plus avec moins d’affiches. Si le quota est dépassé, la galerie ne se charge plus jusqu’au lendemain.

## 11. Personnaliser

Tout se trouve dans `index.html` :

- **Textes et dates** : objet `T` dans le script (`fr` et `ar`). Les dates sont les clés `dates`.
- **Couleurs** : variables en haut de la feuille de style (`--red`, `--green`, `--gold`, `--ink`).
- **Nombre d’affiches affichées** : `fs.limit(120)`.
- **Taille des images** : dans la fonction `compress`, `1000` (pixels) et `450000` (poids maximal en caractères).
- **Volume de la musique** : `au.volume = .45` (entre 0 et 1).

Après chaque modification sur GitHub, attendez une à deux minutes puis rechargez avec Ctrl + F5.

## 12. Si quelque chose ne marche pas

| Symptôme | Cause probable et solution |
|---|---|
| Le bandeau jaune « Mode test » reste affiché | `apiKey` vaut encore `COLLER_ICI`, ou la page est en cache. Corrigez puis faites Ctrl + F5. |
| « L’envoi a échoué » | Ouvrez la console du navigateur (touche F12). Si l’erreur mentionne *permission*, les règles ne sont pas publiées ou ne sont pas celles de `firestore.rules`. |
| L’envoi réussit mais la galerie reste vide | Mauvais `projectId`, ou règles de lecture manquantes. Vérifiez qu’un document existe dans Firestore. |
| Page 404 sur GitHub | Pages n’est pas activé (étape 1, point 7), ou `index.html` n’est pas à la racine du dépôt. Attendez deux minutes. |
| Pas de musique | Noms de fichiers différents (majuscules comprises), ou clic sur « Entrer en silence ». Vérifiez aussi le bouton son en haut à droite. |
| Logos absents | Le dossier `assets` n’a pas été téléversé avec ses deux images. |
| Image refusée | Seuls JPG, PNG et WebP sont acceptés. Les photos d’iPhone au format HEIC doivent être converties ou exportées en JPG. |

## 13. Données personnelles

Le nom et l’affiche de chaque participant sont **publics** dès l’envoi. Prévenez les participants avant le dépôt, et demandez-leur de ne mettre ni numéro de téléphone ni autre information personnelle dans l’affiche ou la description. Pour les participants mineurs, obtenez l’accord nécessaire auprès de l’établissement.

---

Organisé par le club Digital Craft, École Normale Supérieure de Marrakech.
