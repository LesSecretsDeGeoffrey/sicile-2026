# App Sicile 2026 — règles pour Claude Code (édition depuis le téléphone)

Voyage de Geoffrey en Sicile du 23 au 30 août 2026. App web perso, servie par GitHub Pages depuis la branche `main` :
https://lessecretsdegeoffrey.github.io/sicile-2026/ — tout ce qui est poussé sur `main` est en ligne en 1 à 2 minutes.

## Fichiers
- `index.html` : TOUTE l'app (HTML + CSS + JS vanilla + Leaflet, ~4 100 lignes). Données et code dans le même fichier.
- `sw.js` : service worker (cache hors ligne). **Après TOUTE modification de `index.html`, incrémenter `CACHE_NAME`** (`sicile-2026-v10` → `v11`, etc.), sinon le téléphone garde l'ancienne version.
- `manifest.json`, `icon.svg` : ne pas toucher.

## Où sont les données (dans `index.html`, bloc `// ========= DATA =========`)
- `const days = [...]` : 8 jours. Chaque jour = `{ number, shortDate: "DIM 23", fullDate, title, budget: {low, typical, high}, planB: {intro, alternatives: [{name, desc}]}, spots: [...] }`.
- Chaque étape (`spot`) = `{ name, category, time: "14h15", coords: [lat, lon], description, meta, options?: [{name, rating, hours, price, flag, gmaps}], sunset?: true }`.
  - `category` ∈ `hotel · resto · plage · culture · insta · vin · nature · bar · glace · transport` (rien d'autre, sinon l'icône casse).
  - `time` toujours au format `"09h30"` (2 chiffres + h + 2 chiffres). Garder chaque tableau `spots` dans l'ordre chronologique.
  - `coords` = vraies coordonnées GPS vérifiées (Sicile : lat 36,4–38,5 · lon 12–15,8). Jamais de coordonnées inventées.
- `SPOT_METADATA` (par `name` exact) : `rating, hours, price, booking: 'required'|'recommended'|'none', bookingUrl, lead, booked`. Si tu ajoutes un resto/activité, ajoute-lui une entrée si tu connais les infos.
- `SUNSET_TIMES` par numéro de jour, `BONUS_SPOTS` (pépites hors programme), `PACKING_LIST` (valise), `SURVIVAL_KIT`.

## ⚠️ Modifs faites sur le téléphone
L'app permet à Geoffrey de modifier le programme depuis son iPhone (crayon ✎, ajout d'étapes). Ces modifs vivent dans le `localStorage` du téléphone (`sicile-2026-edits`) et sont accrochées à **jour + nom exact de l'étape**.
- **Ne renomme pas une étape existante sans nécessité** et ne la change pas de jour dans les données si tu peux l'éviter : ses modifs locales seraient perdues. Préfère modifier `time`, `description`, `meta`, `options`, ou ajouter/supprimer des étapes.
- Ne touche pas au moteur d'édition (`applyEdits`, `assignBaseIds`, `EDITS_KEY`) ni à la recherche (`runSearch`) sauf demande explicite.

## Contraintes du voyage (à respecter dans toute proposition)
- Base : Eurostars Monte Tauro, Taormina. Voiture de location. **Pas plus de 1h30 de route par jour** (aller).
- Vol retour dimanche 30/08 à 09h25 depuis Catane. Etna réservé le mardi 25 (tour GetYourGuide, sommet 3 300 m).
- Restos : viser des tables ≥ 4,5/5 vérifiées, vérifier les jours de fermeture. Ne jamais inventer un établissement.

## Style
- Français, tutoiement, ton direct et concret (« tu », pas « vous »). Pas de point en fin de titre.
- Diff minimal : modifie ce qui est demandé, ne réécris pas le reste.

## Avant de commiter
1. Vérifier la syntaxe du script inline : `node -e "const h=require('fs').readFileSync('index.html','utf8');const s=[...h.matchAll(/<script>([\s\S]*?)<\/script>/g)].map(m=>m[1]);s.forEach(x=>new Function(x));console.log('OK')"`
2. Bump de `CACHE_NAME` dans `sw.js`.
3. Commit court en français, directement sur `main` si possible (sinon PR, Geoffrey merge depuis son téléphone).
