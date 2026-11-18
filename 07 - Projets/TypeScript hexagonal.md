# TypeScript hexagonal — domaine sport et santé de l'API

**Sujet.** Dépôt `/home/max/Projet-Hexagonnal-Test` : esquisse de domaine utilisateurs, muscles, exercices, séances, performances et favoris ; API TypeScript actuellement centrée sur les routes de santé et sur PostgreSQL. Les types du domaine ne constituent pas à eux seuls des routes CRUD. Source : `README.md`, `src/domain/`, `src/adapters/inbound/http/http-server.ts`.

## Ordre de lecture dans `/home/max/Projet-Hexagonnal-Test`

1. `README.md` → démarrage, limites, migrations explicites ; `package.json` → commandes `build`, `test`, `db:typecheck`.
2. `src/domain/shared/types.ts` → `src/domain/users/entities.ts` → `src/domain/workouts/entities.ts` : interfaces et identifiants. Concept : [[TypeScript - domaine migrations et ports]].
3. `src/application/ports/database-health.port.ts` → `src/application/health/readiness-service.ts` → `src/adapters/outbound/postgres/postgres-health.adapter.ts` → `src/adapters/inbound/http/http-server.ts` → `src/adapters/inbound/http/server.ts` : port, service, adaptateurs et composition.
4. `migrations/manifest.json` → `migrations/20261105211000_create_users_table.ts` → autres fichiers `migrations/` dans l'ordre du manifeste → `scripts/migrate.ts` : schéma et évolution. Le test `tests/integration/workout-schema.test.ts` demande une base jetable pour l'intégration PostgreSQL.
5. `tests/unit/readiness-service.test.ts` → `tests/integration/http-health.test.ts` : distinguer l'essai du service et celui de l'adaptateur HTTP.

**Exemple à tracer.** `GET /health/ready` invoque le service `getReadiness` par le serveur HTTP ; `PostgresHealthAdapter.check()` effectue `SELECT 1`. Le résultat est `ready` ou `unavailable`, et le serveur renvoie 200 ou 503 (`src/application/health/readiness-service.ts`, `src/adapters/inbound/http/http-server.ts`).

**Piège.** Le nom du champ `passwordHash` dans `src/domain/users/entities.ts` diffère de la colonne `password` créée dans la migration users : ne pas inventer une couche de mapping absente. `db:migrate:down -- --all` détruit le schéma dans une base jetable ; pas de migrations au démarrage HTTP. Aucune affirmation de statut PR ou de déploiement n'est déduite ici.

**Mini-exercice.** Dessiner les dépendances du chemin readiness puis écrire sur papier un faux adaptateur `DatabaseHealthPort` dont `check` échoue : quel statut HTTP serait attendu ?
