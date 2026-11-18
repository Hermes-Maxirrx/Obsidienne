# TypeScript — domaine, ports et migrations

**Définition.** Le domaine nomme les concepts métier ; un port décrit une dépendance requise par l'application, et un adaptateur la réalise. Une migration modifie le schéma persistant ; le type TypeScript seul n'impose pas le schéma SQL ni la validation des entrées à l'exécution.

**Dans le dépôt** `/home/max/Projet-Hexagonnal-Test` : `src/domain/users/entities.ts` déclare `User`, `src/domain/workouts/entities.ts` déclare `Muscle`, `Exercise`, `WorkoutTemplate`, `WorkoutSession`, `Performance`, `ExerciseWorkoutTemplate`, `Favorite`, et `src/domain/shared/types.ts` définit `DatabaseId = string`. `src/application/ports/database-health.port.ts` expose `check(): Promise<void>` ; `src/application/health/readiness-service.ts` transforme succès/échec en `ready`/`unavailable`. `src/adapters/outbound/postgres/postgres-health.adapter.ts` fait `SELECT 1` ; `src/adapters/inbound/http/http-server.ts` expose seulement `/health/live` et `/health/ready`. Lire `README.md`.

**Persistance.** `migrations/manifest.json` énumère les migrations dans l'ordre ; `scripts/migrate.ts` valide le manifeste et délègue à `node-pg-migrate` avec table `pgmigrations`, transaction et verrou. `migrations/20261105211000_create_users_table.ts` crée `users` et fournit `down`. Les migrations sont explicites, non lancées automatiquement par le serveur. Le test d'intégration PostgreSQL exige une base jetable et un opt-in (`RUN_WORKOUT_SCHEMA_TESTS=1`) : voir `tests/integration/workout-schema.test.ts`.

**Piège.** `User.passwordHash` dans l'interface et la colonne `password` dans la migration ne sont pas le même nom : ne pas prétendre qu'un mapping complet est déjà réalisé. Les entités décrivent le domaine visé, pas des routes CRUD opérationnelles. Le script `down --all` est destructif ; ne jamais le lancer sur une base de travail. Ne pas déduire de statut de PR ou de déploiement à partir d'un dépôt.

**Mini-exercice.** Tracer `GET /health/ready` depuis `http-server.ts` jusqu'à `SELECT 1`, puis dire à quel endroit ajouter un faux `DatabaseHealthPort` pour tester le cas indisponible (`tests/unit/readiness-service.test.ts`).

Voir [[TypeScript hexagonal]] · [[Node - SQLite API et limites]].
