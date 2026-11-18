# Lilian — choix collectif et brouillon V2

**Sujet.** `/home/max/lilian` est une application Node 24, SQLite et interface JavaScript. Le catalogue V2 documente des restaurants lyonnais réels et conserve des hôtels parisiens fictifs clairement distincts. Le groupe vote, dispose de trois veto par personne et par tour, et aboutit à un **choix unanime et un brouillon copiable non envoyé**, jamais une réservation effective. Sources : `README.md`, `docs/backend.md`, `docs/v2-contract.md`.

## Ordre de lecture dans `/home/max/lilian`

1. `README.md` → périmètre et limites ; `docs/v2-contract.md` → contraintes explicites, dont absence d'envoi et de données médicales dans le brouillon.
2. `docs/sources-lyon.md` → `data/lyon-restaurants.json` → `catalog.mjs` : provenance, tarifs contextualisés, inconnues et distinction réel/démo. La vérification documentaire est datée du 6 novembre 2026, non une promesse de prix actuels.
3. `docs/backend.md` → `package.json` → `server.mjs` → `model.mjs` : API de groupes, jetons privés, moteur, SQLite et transactions. Concept : [[Node - SQLite API et limites]].
4. `public/index.html` → `public/app.js` → `public/styles.css` : exploration, votes, chrono, brouillon et liens officiels ; `tests/backend-v2.test.mjs` → `tests/backend-v2-data.test.mjs` → `tests/browser_qa_v2.py` → `docs/qa-v2.md` : comportements testés et limites datées.

**Exemple à tracer.** Dans `model.mjs`, `projectVenue` signale `budgetUnverified` si un tarif est inconnu et `requiresAllergyConfirmation` si un membre a déclaré des allergies pour un restaurant réel. Le texte final ne reprend pas automatiquement ces préférences privées. `server.mjs` publie l'état et les routes ; la phase interne `reserved` signifie ici brouillon (`status: "draft"`, `simulated: true`), non confirmation réelle.

**Piège.** Une liste `allergens: []` n'atteste aucune innocuité ; demander confirmation au restaurant. Un tarif est un repère de plat/formule sourcé, pas un prix total garanti ; pas de réduction, partenariat ou disponibilité inventés. Aucun e-mail, SMS, WhatsApp, formulaire externe ou paiement n'est envoyé. La QA datée ne prouve pas que le tunnel temporaire est actuellement accessible.

**Mini-exercice.** Suivre de `public/app.js` à `server.mjs` et `model.mjs` la réponse à un quatrième veto ; identifier la règle serveur et l'indication UI, puis expliquer pourquoi un nouveau tour remet le quota à trois sans effacer les exclusions précédentes.
