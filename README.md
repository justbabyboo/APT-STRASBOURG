# Appartement Strasbourg — Dashboard (Maxime & Can)

Dashboard web statique (un seul fichier `index.html`) pour la recherche d'appartement
en location à Strasbourg. Lit en direct un Google Sheet publié en CSV et affiche :
budget, carte des biens, annonces, agences à visiter et checklist papiers (loi ALUR).

## Déploiement

- **Live** : https://appartement-strasbourg-cm.netlify.app
- **Hébergement** : Netlify, lié à ce dépôt GitHub → **auto-déploiement à chaque commit**.
- **Réglages build Netlify** : pas de build command, publish directory = `/` (racine).
  Ces réglages sont aussi déclarés dans [`netlify.toml`](./netlify.toml).

## Fonctionnement

- La source des annonces est un Google Sheet publié en CSV, dont l'URL est codée en dur
  dans `index.html` (`DEFAULT_URL_SUIVI`). Auto-refresh toutes les 10 min.
- L'annuaire des agences (adresses Strasbourg) est codé en dur dans `index.html`
  (tableau `AGENCIES`).

## Modifier le site

Demander à Claude (connecteur GitHub) d'éditer `index.html` → commit → Netlify
redéploie automatiquement. Voir [`HANDOFF-CHAT-GITHUB.md`](./HANDOFF-CHAT-GITHUB.md)
pour le contexte complet (liens, identifiants, webhook d'écriture du Sheet).

## Self-service (sans Claude)

- **Ajouter/éditer une annonce** : éditer directement le Google Sheet → le dashboard
  se met à jour seul (≤ 10 min).
- **Consulter** : ouvrir le lien Netlify sur mobile, « Ajouter à l'écran d'accueil ».
