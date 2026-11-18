# Métier — commandes, stocks, transactions

**Idée.** Une transition métier conserve ensemble état de commande, réservation de stock et événement ; la transaction protège l'ensemble, et les verrous/version protègent les décisions concurrentes.

**Où apprendre.** [[Hexa Java]] : `docs/03-schema-et-etats.md`, `domain/src/main/java/fr/hermes/hexajava/domain/inventory/StockItem.java`, `domain/src/main/java/fr/hermes/hexajava/domain/order/PurchaseOrder.java`, `application/src/main/java/fr/hermes/hexajava/application/service/OrderService.java`, puis `application/src/test/java/fr/hermes/hexajava/application/service/OrderServiceTest.java`.

**Exemple du code.** `StockItem.reserve(q)` augmente `reserved` sans baisser `onHand`; `consume(q)` baisse les deux. `OrderService.confirm` verrouille la commande, réserve les lignes et enregistre `ORDER_CONFIRMED` sous `transactions.required`. `adapters/src/main/java/fr/hermes/hexajava/adapters/out/persistence/SpringTransactionRunner.java` traduit ce port en `TransactionTemplate`.

**Piège / exercice.** Un record immuable ne verrouille pas SQL. Paiement et expédition sont **simulés**. (1) Pourquoi `available = onHand - reserved` ? (2) Que se passe-t-il si la réservation échoue au milieu d'une confirmation ?

Avant : [[Architecture - hexagonale et ports]] · Après : [[Fiabilite - outbox inbox jobs]].
