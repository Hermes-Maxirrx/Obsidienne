# Node — SQLite, API et limites dans Lilian

**Définition.** Ici Node héberge une API HTTP et un moteur de choix de groupe ; SQLite conserve les groupes localement. Une transaction entoure les mutations, tandis que les données inconnues du catalogue restent explicitement non confirmées.

**Dans le dépôt** `/home/max/lilian` : `package.json` demande Node 24 et `npm start` lance `server.mjs`. `server.mjs` utilise `node:http`, expose `/lilian/health`, `/lilian/api/catalog` et les routes de groupes ; les écritures demandent du JSON, les routes membre un jeton Bearer. `model.mjs` ouvre `DatabaseSync` de `node:sqlite`, crée `groups` et son index, puis enveloppe les mutations dans `BEGIN IMMEDIATE` / `COMMIT` ou `ROLLBACK`. Voir `docs/backend.md` et `tests/backend-v2.test.mjs`.

**Exemple.** `model.mjs` projette `me.vetoesRemaining` et marque `budgetUnverified` lorsque le prix n'est pas connu, `requiresAllergyConfirmation` si des allergies existent pour un restaurant réel. L'accord unanime crée un **choix et un brouillon copiable non envoyé**, même si la phase interne s'appelle `reserved` pour compatibilité. `data/lyon-restaurants.json` contient les fiches sourcées ; `docs/sources-lyon.md` date leur vérification du 6 novembre 2026.

**Piège.** `allergens: []` signifie information non certifiée, pas « sans allergènes ». Les prix documentés sont des repères de plat/formule, non un budget garanti ; les disponibilités et coordonnées des restaurants réels sont inconnues. Aucun envoi, réservation, réduction ou partenariat réel n'est établi. Un test sur SQLite et Chromium ne prouve pas la disponibilité du tunnel temporaire aujourd'hui ; les résultats datés sont dans `docs/qa-v2.md`.

**Mini-exercice.** Suivre un quatrième veto dans `model.mjs` et `tests/backend-v2.test.mjs` : repérer l'erreur et expliquer pourquoi la vérification doit précéder la mutation dans la transaction.

Voir [[Lilian]] · [[TypeScript - domaine migrations et ports]].
