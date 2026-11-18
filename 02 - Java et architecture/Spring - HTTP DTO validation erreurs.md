# Spring — HTTP, DTO, validation et erreurs

**Idée.** Le contrôleur traduit HTTP en appel métier ; un DTO décrit le corps attendu ; l'advice transforme les exceptions en statut et corps d'erreur. La validation est à localiser, pas à présumer.

**Où apprendre.** [[Java - revision Spring Boot]] : `docs/TP4.md`, `tp4/src/main/java/fr/library/BookRequest.java`, `BookController.java`, `LibraryExceptionHandler.java`, puis `tp4/src/test/java/fr/library/BookControllerTest.java`.

**Exemple du code.** `POST /books` reçoit `BookRequest`, appelle `toBook()` : c'est le constructeur `Book` qui valide l'ISBN et l'advice renvoie `400` pour `IllegalArgumentException`. Doublon → `409`, absence → `404` via `ProblemDetail`. **Pas** de `@Valid` à inventer sur ce DTO du TP4 ; le projet indépendant [[Hexa Java]] utilise `@Valid` sur `OrderController`.

**Piège / exercice.** Un test MVC avec mock ne démontre pas la persistance. (1) Qui rejette réellement l'ISBN invalide ici ? (2) Quel statut devrait produire un ISBN déjà présent ?

Avant : [[Java - Streams records sealed]] · Après : [[Persistance - JPA Liquibase PostgreSQL]].
