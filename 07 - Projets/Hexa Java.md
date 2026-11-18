# Hexa Java — commandes et événements fiables

**Sujet.** `/home/max/hexa-java` est un monolithe pédagogique autonome de gestion de commandes et de stocks, pas un TP supplémentaire du corrigé Spring. Il expose des ressources métier via HTTP tout en gardant les règles de domaine et les cas d'usage indépendants de Spring, de JPA et du transport. Les paiements et expéditions sont simulés : aucune banque ni aucun transporteur n'est intégré.

**Conception.** Le domaine porte les états et invariants ; l'application coordonne une commande, ses réservations de stock et l'enregistrement d'un événement dans la même transaction. Les ports décrivent ce dont les cas d'usage ont besoin ; les adaptateurs fournissent PostgreSQL, HTTP et les tâches planifiées. L'outbox évite de publier un événement réseau avant le commit métier. Une livraison peut être répétée si l'effet a eu lieu mais que son accusé se perd : journal de déduplication, inbox et reprises rendent le consommateur idempotent, sans promettre un « exactement une fois » global. Les tests d'intégration confrontent ces hypothèses à PostgreSQL jetable.

**Ordre de lecture** (depuis la racine du dépôt) :
1. `README.md` — périmètre et architecture.
2. `docs/02-architecture-et-decisions.md` — dépendances et compromis.
3. `application/src/main/java/fr/hermes/hexajava/application/service/OrderService.java` — orchestration transactionnelle.
4. `application/src/main/java/fr/hermes/hexajava/application/service/OutboxService.java` — reprise et répétition.
5. `bootstrap/src/test/java/fr/hermes/hexajava/OutboxDeliveryIT.java` — preuve à la frontière.

**Transfert et limites.** Revoir [[Architecture hexagonale - Domaine, ports et limites]], [[Événements - Outbox, inbox et reprises]], [[SQL - Modéliser, transiger et indexer]] et [[Patterns - index]]. Des tests et une API locale ne prouvent ni intégration de paiement réel, ni authentification, ni CI distante actuellement réussie. Ne pas exposer l'API pédagogique sans contrôle d'accès.

**Question de révision.** Que se passe-t-il si l'effet d'un événement réussit, mais que l'accusé de livraison échoue ?
