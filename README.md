# LELMA — Safety Dashboard 🏭

Dashboard interactif de sécurité incendie pour LELMA, Zone Franche Tanger.  
Cliquez sur un département du plan d'évacuation pour voir les incidents, les 8D, et les actions correctives.

## Structure du projet

```
lelma-safety/
├── index.html          # Page principale
├── src/
│   ├── style.css       # Styles (thème sombre, responsive)
│   ├── data.js         # Données des départements + config Google Sheets
│   └── app.js          # Logique interactive + intégration Google Sheets
└── README.md
```

## Lancer en local

Aucune installation requise. Ouvrez simplement `index.html` dans votre navigateur.

Ou avec un serveur local (recommandé pour l'API Google Sheets) :
```bash
# Python
python3 -m http.server 3000

# Node (si npx disponible)
npx serve .
```

## Connecter Google Sheets

### 1. Préparez votre Google Sheet

Créez une feuille avec ces colonnes pour chaque département :

| A — Date | B — Type | C — Description | D — Action corrective | E — Statut | F — Réf. 8D | G — Photo (URL) |
|----------|----------|-----------------|----------------------|------------|-------------|-----------------|
| 12/04/2025 | Chute d'objet | Carton tombé... | Fixation renforcée | closed | | |

Statuts possibles : `open` / `progress` / `closed`

### 2. Obtenez vos identifiants

1. Allez sur [Google Cloud Console](https://console.cloud.google.com)
2. Créez un projet → activez **Google Sheets API**
3. Créez une clé API (Credentials → API Key)
4. Copiez l'ID de votre Google Sheet depuis l'URL :  
   `https://docs.google.com/spreadsheets/d/**VOTRE_ID**/edit`

### 3. Configurez `src/data.js`

```js
const CONFIG = {
  SHEET_ID: "votre_sheet_id_ici",
  SHEET_NAME: "Incidents",
  SHEET_URL: "https://docs.google.com/spreadsheets/d/votre_sheet_id_ici/edit"
};
```

### 4. Activez le chargement dans `src/app.js`

Dans `DOMContentLoaded`, remplacez `applyStatusDots()` par :
```js
fetchFromGoogleSheets().then(() => {
  applyStatusDots();
  updateHeaderStats();
});
```
Et décommentez la fonction `fetchFromGoogleSheets()` en bas du fichier.

Ajoutez votre API Key dans cette fonction :
```js
const API_KEY = "votre_api_key_google";
```

### 5. Partagez le Google Sheet

Rendez votre Sheet accessible en lecture publique (ou restreint à votre domaine).

## Déploiement GitHub Pages

1. Créez un repo GitHub : `lelma-safety`
2. Poussez tous les fichiers
3. Allez dans **Settings → Pages → Source: main branch**
4. Votre site sera disponible sur :  
   `https://votre-username.github.io/lelma-safety`

## Ajouter un département

Dans `src/data.js`, ajoutez une entrée dans `DEPARTMENTS` :

```js
monDept: {
  name: "Mon Département",
  zone: "Zone X",
  googleSheetTab: "Mon_Dept",   // nom de l'onglet Google Sheet
  accidents: 0,
  nearMiss: 0,
  open8D: 0,
  incidents: []
}
```

Puis dans `index.html`, ajoutez un élément SVG `<g class="dept" data-id="monDept">`.

## Technologies

- HTML / CSS / JavaScript vanilla — aucune dépendance
- Google Sheets API v4 (optionnel, pour données en temps réel)
- Fonts : [Syne](https://fonts.google.com/specimen/Syne) + [DM Mono](https://fonts.google.com/specimen/DM+Mono) (Google Fonts)

---

**Référence plan** : N° 08-11-12 · REV E04 · LELMA Zone Franche LOT N°1 Tanger
