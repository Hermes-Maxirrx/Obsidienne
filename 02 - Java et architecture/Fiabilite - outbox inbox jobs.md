# Fiabilité — outbox, inbox et jobs

**Idée.** L'outbox écrit un fait avec la transaction métier ; un job le livre plus tard. L'inbox (journal `processed_events`) déduplique la réception répétée : « au moins une fois » n'est pas « exactement une fois » sur le réseau.

**Où apprendre.** [[Hexa Java]] : `docs/02-architecture-et-decisions.md`, `application/src/main/java/fr/hermes/hexajava/application/service/OutboxService.java`, `IdempotentEventConsumer.java`, `adapters/src/main/java/fr/hermes/hexajava/adapters/out/event/JdbcEventJournal.java`, `adapters/src/main/java/fr/hermes/hexajava/adapters/in/job/CommerceJobs.java`, puis `bootstrap/src/test/java/fr/hermes/hexajava/OutboxDeliveryIT.java`.

**Exemple du code.** `JdbcEventJournal.lockBatch` emploie `FOR UPDATE SKIP LOCKED`; le consommateur réclame `(eventId, "shipping-v1")` dans une transaction `requiresNew`, puis prépare une expédition simulée pour `PAYMENT_CAPTURED`. `CommerceJobs` déclenche livraison et expiration, sans contacter banque, broker ou transporteur.

**Piège / exercice.** Un crash après l'effet mais avant l'acquittement relivre le même événement. (1) Pourquoi conserver son identifiant ? (2) Quelle transaction annule la réclamation si préparer l'expédition échoue ?

Avant : [[Metier - commandes stocks transactions]] · [[Architecture - hexagonale et ports]].
