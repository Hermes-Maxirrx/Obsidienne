# Frontend, TypeScript et Node — parcours

Ces notes partent de trois dépôts locaux, pas d'une application commune. Lire d'abord les README et les fichiers cités ; les chemins sont relatifs à chaque dépôt.

## React — atelier local

1. [[React - etat rendu reducer contexte]] — rendre et dériver l'état.
2. [[React - effets asynchrone annulation]] — chargement et sauvegarde optimiste.
3. [[React - formulaires accessibilite]] — saisie et retour d'erreur.
4. [[React - performance Suspense erreurs]] — priorités, chargement différé et tests.

Parcours ancré : [[React avance]]. Ce projet utilise des données fictives et `localStorage`, sans API HTTP.

## Domaine, persistance et service

5. [[TypeScript - domaine migrations et ports]] — distinguer modèle, port, adaptateur et migration PostgreSQL ; parcours [[TypeScript hexagonal]].
6. [[Node - SQLite API et limites]] — autre persistance, cette fois dans le serveur Lilian ; parcours [[Lilian]].

Comparer : une interface TypeScript n'effectue aucune validation à l'exécution ; une migration établit un schéma SQL ; un port décrit une dépendance applicative. Dans Lilian, les règles sont vérifiées par le moteur et l'API, mais les inconnues du catalogue restent des inconnues.

**Mini-exercice :** pour chaque dépôt, nommer l'endroit qui détient l'état durable et ce que la lecture de son README ne permet pas de conclure sur une disponibilité réelle.
