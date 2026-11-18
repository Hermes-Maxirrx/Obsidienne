# Hexa Java — projet indépendant

**Nature.** Monolithe pédagogique autonome d'architecture hexagonale, **pas** un TP supplémentaire du support de Corentin Beuchet ni une reconstruction du projet Spring. Dépôt local : `/home/max/hexa-java` ; remote `origin` : https://github.com/Hermes-Maxirrx/hexa-java (potentiellement privé).

**Parcours fichiers (depuis la racine).** `README.md` → `docs/01-lire-le-projet.md` → `docs/02-architecture-et-decisions.md` → `docs/03-schema-et-etats.md` → `domain/src/main/java/fr/hermes/hexajava/domain/inventory/StockItem.java` → `domain/src/main/java/fr/hermes/hexajava/domain/order/PurchaseOrder.java` → `application/src/main/java/fr/hermes/hexajava/application/port/in/OrderUseCases.java` → `application/src/main/java/fr/hermes/hexajava/application/service/OrderService.java` → `application/src/main/java/fr/hermes/hexajava/application/port/out/TransactionRunner.java` → `adapters/src/main/java/fr/hermes/hexajava/adapters/out/persistence/SpringTransactionRunner.java` → `application/src/main/java/fr/hermes/hexajava/application/service/OutboxService.java` → `adapters/src/main/java/fr/hermes/hexajava/adapters/out/event/JdbcEventJournal.java` → `bootstrap/src/test/java/fr/hermes/hexajava/OutboxDeliveryIT.java`.

**À retenir.** Domaine/application indépendants de Spring ; adaptateurs HTTP/JPA/JDBC/jobs et assemblage `bootstrap`. PostgreSQL/Flyway et outbox/inbox ; paiements et expéditions **simulés**, pas de banque/transporteur. Lire les tests, pas présumer une CI distante verte.

Concepts : [[Architecture - hexagonale et ports]] · [[Metier - commandes stocks transactions]] · [[Fiabilite - outbox inbox jobs]] · [[Java - index]].
