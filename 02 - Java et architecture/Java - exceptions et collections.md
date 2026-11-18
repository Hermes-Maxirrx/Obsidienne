# Java — exceptions et collections

**Idée.** Une `Map` associe une clé à un objet ; une exception métier rend explicite un refus ou une absence au lieu d'exposer un `null` ambigu.

**Où apprendre.** [[Java - revision Spring Boot]] : `docs/TP2.md`, puis `tp2/src/main/java/fr/library/Library.java`, `DuplicateBookException.java`, `BookNotFoundException.java` et `tp2/src/test/java/fr/library/LibraryTest.java`.

**Exemple du code.** `Library.addBook` fait `containsKey(book.getIsbn())` avant `put`, sinon `LinkedHashMap.put` écraserait un livre existant. `findByIsbn` convertit l'absence en `BookNotFoundException` ; `findByAuthor` renvoie plutôt une liste vide.

**Piège / exercice.** `containsKey` suivi de `put` n'est pas atomique entre threads. (1) Quel état serait perdu sans le contrôle de doublon ? (2) Pourquoi chercher un auteur absent ne lance-t-il pas nécessairement une exception ?

Avant : [[Java - objets et identité ISBN]] · Après : [[Java - Streams records sealed]] et [[Spring - HTTP DTO validation erreurs]].
