# Architecture — hexagonale et ports

**Idée.** Les règles métier restent indépendantes des outils ; les ports entrants disent ce qu'on peut faire, les ports sortants ce dont l'application a besoin. Les adaptateurs HTTP, JPA/JDBC et transactionnels réalisent ces interfaces.

**Où apprendre.** [[Hexa Java]] (projet **indépendant** des TP reconstruits) : `docs/01-lire-le-projet.md`, `docs/02-architecture-et-decisions.md`, puis `adapters/src/main/java/fr/hermes/hexajava/adapters/in/web/product/ProductController.java` → `application/src/main/java/fr/hermes/hexajava/application/port/in/ProductUseCases.java` → `application/src/main/java/fr/hermes/hexajava/application/service/ProductService.java` → `application/src/main/java/fr/hermes/hexajava/application/port/out/ProductRepository.java` → `adapters/src/main/java/fr/hermes/hexajava/adapters/out/persistence/product/JpaProductRepository.java`.

**Exemple du code.** `bootstrap/src/main/java/fr/hermes/hexajava/bootstrap/UseCaseConfiguration.java` construit `ProductService` avec son port repository et `TransactionRunner` ; le cœur n'importe pas Spring.

**Piège / exercice.** Le sens des appels n'est pas celui des dépendances de compilation. (1) Où remplacer JPA par un faux de test ? (2) Pourquoi le contrôleur dépend-il du port entrant plutôt que de `SpringDataProductRepository` ?

Suite : [[Metier - commandes stocks transactions]] · [[Fiabilite - outbox inbox jobs]].
