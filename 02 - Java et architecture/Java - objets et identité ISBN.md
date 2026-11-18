# Java — objets et identité ISBN

**Idée.** Un objet protège ses invariants dès sa construction ; son égalité métier n'est pas nécessairement l'identité de l'instance ni l'identifiant SQL.

**Où apprendre.** [[Java - revision Spring Boot]] : `docs/TP1.md`, puis `tp1/src/main/java/fr/library/Book.java` et `tp1/src/test/java/fr/library/BookTest.java` ; prolonger avec `tp5/src/main/java/fr/library/Book.java`.

**Exemple du code.** `Book` refuse un ISBN qui n'a pas treize chiffres ASCII ; `equals` et `hashCode` reposent sur `isbn` même quand TP5 ajoute un `id` JPA. Changer le titre ne crée pas un autre livre selon cette règle locale.

**Piège / exercice.** Un `id` SQL attribué plus tard n'est pas une bonne identité de ce modèle. (1) Deux instances de même ISBN, titres différents : que renvoie `equals` ? (2) Pourquoi garder la même règle après l'ajout de `id` ?

Suite : [[Java - exceptions et collections]] · [[Persistance - JPA Liquibase PostgreSQL]].
