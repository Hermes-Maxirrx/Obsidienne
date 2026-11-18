# Persistance — JPA, Liquibase, PostgreSQL

**Idée.** JPA mappe les objets sur les tables ; une migration versionnée définit le schéma ; une contrainte SQL protège l'unicité même en concurrence.

**Où apprendre.** [[Java - revision Spring Boot]] : `docs/TP5.md`, `tp5/src/main/resources/db/changelog/changes/001-create-books-table.yaml`, `tp5/src/main/java/fr/library/Book.java`, `BookRepository.java`, `BookService.java`, puis `tp5/src/test/java/fr/library/BookApiIT.java`.

**Exemple du code.** `BookService.add` teste `existsByIsbn` pour le cas courant, mais `saveAndFlush` peut encore recevoir une violation UNIQUE SQL `23505`, traduite en doublon. `flush` n'est pas un commit. TP5 s'appuie sur PostgreSQL/Testcontainers ; le DDL appartient à Liquibase, pas à Hibernate (`validate`).

**Piège / exercice.** Le contrôle Java ne remplace pas UNIQUE. (1) Comment deux requêtes peuvent-elles toutes deux voir « absent » ? (2) Qu'apporte un test PostgreSQL réel qu'un mock du repository ne montre pas ?

Avant : [[Spring - HTTP DTO validation erreurs]] · À comparer, Flyway plutôt que Liquibase dans [[Hexa Java]].
