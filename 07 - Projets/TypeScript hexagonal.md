# TypeScript hexagonal — domaine partiel et santé de l'API

**Sujet.** `/home/max/Projet-Hexagonnal-Test` dessine un domaine sportif : utilisateurs, exercices, séances, performances et favoris. Il ne faut pas confondre ses types et ses tables avec des parcours métier complets : l'API TypeScript implémente surtout les routes de santé et la connexion PostgreSQL. La structure hexagonale y est minimale, mais fournit un exemple précis de séparation entre interface HTTP, cas d'usage et dépendance à la base.

**Conception.** La vérification de disponibilité dépend du port `DatabaseHealthPort` ; l'adaptateur PostgreSQL exécute `SELECT 1`, tandis que l'adaptateur HTTP traduit le résultat en `200` ou `503`. La liveness répond sans exiger que la base soit accessible. Cette distinction évite de mettre hors service un processus encore vivant simplement parce qu'une dépendance externe est indisponible. Les migrations sont enregistrées dans un manifeste ordonné et déclenchées explicitement, jamais automatiquement au démarrage du serveur HTTP. Les types du domaine protègent la compilation, mais n'établissent ni validation complète des entrées ni correspondance automatique avec les colonnes SQL.

**Ordre de lecture** (depuis la racine du dépôt) :
1. `README.md` — démarrage et migration explicite.
2. `src/application/ports/database-health.port.ts` — contrat attendu.
3. `src/application/health/readiness-service.ts` — décision applicative.
4. `src/adapters/inbound/http/http-server.ts` — traduction HTTP.
5. `migrations/manifest.json` — évolution ordonnée du schéma.

**Transfert et limites.** Voir [[Architecture hexagonale - Domaine, ports et limites]], [[TypeScript - types et frontieres runtime]], [[SQL - Modéliser, transiger et indexer]] et [[Systèmes distribués - Fiabilité et reprise]]. La présence d'entités sportives ne prouve aucune route CRUD correspondante. Un test de readiness ne prouve pas le fonctionnement des cas d'usage sportifs ; les migrations destructives demandent une base jetable et une décision explicite.

**Question de révision.** Pourquoi `GET /health/live` peut-il réussir quand `GET /health/ready` renvoie `503` ?
