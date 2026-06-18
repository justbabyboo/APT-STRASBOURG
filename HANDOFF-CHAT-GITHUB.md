# Handoff — Dashboard Appartement Strasbourg → migration GitHub + auto-déploiement

> À coller (et joindre `index.html`) dans un **chat Claude classique** où le **connecteur GitHub** est activé, pour continuer le projet.

## Contexte
Recherche d'appartement en location à Strasbourg, à deux (**Maxime + Can**). Un **dashboard web** (Netlify) lit en direct un Google Sheet et affiche : budget, carte, annonces, **agences à visiter**, checklist papiers ALUR. Tout est déjà en place et fonctionnel.

## Objectif de la suite
Mettre le code du dashboard (`index.html`) sur **GitHub**, et brancher **Netlify** sur ce dépôt pour un **auto-déploiement** à chaque modif. Ensuite : « Claude, change X » → commit → le site se met à jour seul.

## Liens & identifiants
- **Dashboard (live)** : https://appartement-strasbourg-cm.netlify.app
- **Netlify** : projet `appartement-strasbourg-cm` (créé en *Netlify Drop* / glisser-déposer, auto-publish ON)
- **Sheet « Suivi annonces »** : https://docs.google.com/spreadsheets/d/1un6O8nXrDRYe0ZfSS2ZMP99XVUtANxAXDQBLsOmh8wQ
- **CSV publié du Suivi** (source live du dashboard) :
  `https://docs.google.com/spreadsheets/d/e/2PACX-1vT9hEb8kad0_6QzY5yqvm0n4gZKKUdLCtG0TNUrhUiGm_nRlFUMHMw2R3aqrdrxl-TFXXrzplfOjgXD/pub?output=csv`
- **Sheet « Budget »** : https://docs.google.com/spreadsheets/d/1s0pwh6ker4HjiLdWbr1mDZvyyDomvmN8EhBrLttB2Us (pas encore publié en CSV)
- **Dossier Drive parent** : https://drive.google.com/drive/folders/1PW7SZfnFzqf2SEwMbZDwMKKXV4NN97o1
- **Webhook d'écriture** (Apps Script, ajoute des annonces dans le Sheet) :
  `https://script.google.com/macros/s/AKfycbyQoMa2FeCUFq8JLWrJjri_9JHLY_7r6_dLTdlaLxlrXh960AH7pOKUW2cXGuTFoVXapQ/exec`
  - POST JSON `{ "token": "REMPLACE_MOI_par_un_secret_aleatoire", "action": "upsert", "data": { ... colonnes ... } }`
  - Appeler avec `curl -sSL` (PAS `-X POST` — casse sur la redirection 302). N'insère pas de nouvelle ligne s'il n'y a plus de ligne vide avant la section COMPARATIF.

## État actuel
- **13 annonces** dans le Sheet (4 d'origine + 9 ajoutées le 2026-06-18).
- Dashboard live affiche tout, **auto-refresh 10 min**. L'URL CSV est **codée en dur** dans `index.html` (`const DEFAULT_URL_SUIVI`) → marche sans réglage sur tout appareil.
- Section **« Agences à visiter »** en place : regroupe les annonces par agence + adresse + lien Google Maps + liens des offres. **Adresses codées en dur** dans `index.html` (tableau `AGENCIES`).

## Annuaire agences (codé en dur dans index.html — à éditer dans le code)
| Clé | Agence | Adresse Strasbourg |
|---|---|---|
| foncia | Foncia — Location & Gestion | 27 avenue du Rhin, 67100 Strasbourg |
| immoval | Immoval | 22 avenue de la Paix, 67000 Strasbourg — ☎ 03 88 22 88 22 |
| lamy | Lamy Strasbourg Kléber | 2 quai Kléber (Tour ESCA), 67000 Strasbourg |
| asi | ASI — Agence Strasbourg Immobilière | 200 avenue de Colmar (Meinau), 67100 ; succursale 77 rue Boecklin (Robertsau) |
| 123 loger | 123 Loger | en ligne (entre particuliers) — bonjour@123loger.com |
| locservice | LocService | en ligne (de particulier à particulier) — locservice.fr |
| leboncoin | LeBonCoin | annonces de particuliers (en ligne) |

## Étapes pour la migration GitHub (dans le chat classique)
1. **Joindre `index.html`** à la conversation (le fichier déployé actuel) pour servir de base au dépôt.
2. Claude (via connecteur GitHub) : **crée un dépôt** (ex. `appartement-strasbourg-dashboard`) et y **commit `index.html`**.
3. **Toi, dans Netlify** (clics ponctuels) : projet `appartement-strasbourg-cm` → *Site configuration → Build & deploy → Link repository* → autoriser GitHub (OAuth) → choisir le dépôt. Réglages build : **pas de build command**, **publish directory = `/`** (racine).
4. Test : Claude fait un petit commit → Netlify auto-déploie. ✅
5. Ensuite, pour toute modif (adresse d'agence, design, etc.) : demander à Claude → il édite `index.html` dans le dépôt → commit → déploiement auto.

## Ce qu'un chat classique NE peut PAS faire (≠ Cowork)
- Pas d'accès aux fichiers locaux du Mac, pas de contrôle du navigateur, pas d'exécution de code.
- Donc la **source de vérité du code passe sur GitHub** (plus le fichier local). La liaison Netlify↔GitHub = **tes clics** une fois (OAuth).

## Workflows qui restent en self-service (sans Claude)
- **Ajouter/éditer une annonce** : éditer directement le Google Sheet (appli mobile OK) → dashboard se met à jour seul (≤10 min).
- **Voir le dashboard** : ouvrir le lien Netlify sur tél, « Ajouter à l'écran d'accueil ».

## Limites connues
- Connecteur Google Drive de Claude = **lecture seule** sur les Sheets (ne peut pas écrire) → c'est pour ça que l'écriture passe par le webhook.
- SeLoger & Immoval = récupérables via fetch web (agence, DPE, charges). LeBonCoin bloqué (Datadome).
