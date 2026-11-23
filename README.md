# Widget Grist — Saisie du temps

Widget personnalisé (**Custom Widget**) pour Grist qui permet de saisir le temps
passé sur chaque projet, jour par jour.

## Fonctionnalités

- Saisie rapide : **date**, **projet** (avec suggestions automatiques),
  **heures** et **commentaire** optionnel.
- **Heures en centièmes** : la valeur est enregistrée en centièmes d'heures
  dans Grist (ex. `1,75`), et affichée partout en **heures / minutes**
  (ex. `1 h 45`). La saisie accepte aussi les durées `1h45`, `1:45`,
  `45 min` ou `45` (unit(hour)s entières → `45` = 45 h ; utilisez `0,75` pour 45 min).
- Navigation jour par jour (‹ / ›) avec total d'heures affiché à côté de la date.
- **Répartition du jour** par projet (barres proportionnelles).
- Liste des entrées du jour, suppression en un clic (✕).
- Chaque « Ajouter » crée une nouvelle ligne dans la table liée au widget
  (aucun effort : c'est la table du widget qui est alimentée).

## Installation dans Grist

### Option A — URL directe (le plus simple)

1. Hébergez `grist-saisie-temps.html` sur n'importe quel serveur web public
   (par ex. GitHub Pages) et récupérez son URL.
2. Dans Grist : **Ajouter** → **Nouveau widget sur la page**, puis sélectionnez
   **Custom**.
3. Dans le panneau de création du widget, collez l'URL dans le champ
   **Personnaliser l'URL**.
4. Dans le menu du widget, choisissez **Autoriser l'accès** → **Accès complet**
   (nécessaire pour écrire dans le document).
5. Liez le widget à votre table de suivi du temps (voir ci-dessous) et
   rapprochez les colonnes dans le panneau de création.

### Option B — Catalogue de widgets (auto-hébergé)

Si vous administrez votre instance Grist, hébergez tout le dossier et pointez
`GRIST_WIDGET_LIST_URL` vers `manifest.json` : le widget apparaîtra dans le
catalogue des widgets custom.

```bash
GRIST_WIDGET_LIST_URL="https://mon-serveur.example/manifest.json"
```

## Mise en place du document

1. Créez une table (par ex. **Temps**) avec au minimum :

   | Colonne        | Type     |
   |----------------|----------|
   | Date           | Date     |
   | Projet         | Texte **ou** Référence vers une table Projets |
   | Heures         | Numérique|
   | Commentaire    | Texte    |

2. Ajoutez le widget sur cette table et liez-le à celle-ci
   (*Choisir les données source* → Temps).
3. Dans le panneau de création, rapprochez les colonnes **Date**, **Projet**,
   **Heures**, **Commentaire** (le mapping permet de renommer les colonnes
   librement).
4. **Champ Projet en Référence (optionnel mais recommandé)** : créez une table
   **Projets** (par ex. une colonne `Name` de type Texte), puis dans **Temps**
   définissez `Projet` comme une colonne de type **Référence** ciblant
   `Projets`. Le widget détecte automatiquement qu'il s'agit d'une référence :
   il affiche une liste déroulante avec les lignes de la table cible et
   enregistre l'`rowId` (la vraie référence Grist), pas du simple texte. La
   colonne affichée est choisie automatiquement (priorité aux colonnes
   `Name`, `Nom`, `Titre`, `Projet`, `Label`, ou la première colonne texte).
   Si `Projet` reste une colonne Texte, la saisie libre avec suggestions
   continue de fonctionner comme avant.

### Bonus : table Projets pour les suggestions

Le widget complète ses suggestions avec le contenu d'une table nommée
**Projets** (si elle existe) : ajoutez par exemple une colonne `Name` (Texte).
Sinon, les suggestions proviennent uniquement des valeurs déjà saisies.

> La table `Projets` doit être accessible selon vos règles d'accès ;
> sinon le widget continuera de fonctionner avec les valeurs déjà saisies.

## Notes techniques

- Fichier unique, sans dépendance autre que le script officiel
  `grist-plugin-api.js` (chargé depuis `docs.getgrist.com` ; si vous utilisez
  une instance auto-hébergée, remplacez l'URL du `<script>` par celle de votre
  instance : `https://mon-grist.example/grist-plugin-api.js`).
- Écritures via `grist.getTable().create()` / `.destroy()` (API officielle des
  widgets Custom) — la table cible est automatiquement celle du widget, même
  renommée.
- Mapping des colonnes géré par `grist.mapColumnNames` via l'option `columns`
  de `grist.ready()`.
