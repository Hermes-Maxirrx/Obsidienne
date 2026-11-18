# Java — Streams, records, sealed

**Idée.** Un Stream exprime un parcours/filtre/tri ; un record porte des composants et une égalité par valeur ; une interface `sealed` limite ses variantes connues.

**Où apprendre.** [[Java - revision Spring Boot]] : `docs/TP3.md`, `tp3/src/main/java/fr/library/SearchCriteria.java`, `ByAuthor.java`, `Library.java`, puis `tp3/src/test/java/fr/library/LibrarySearchTest.java`.

**Exemple du code.** `SearchCriteria permits ByTitle, ByAuthor, ByYearRange` rend le `switch` de `Library.search` exhaustif ; `booksSortedByAuthor()` trie par auteur **puis** titre avec `thenComparing`. `ByAuthor` est un critère record, pas une entité persistée.

**Piège / exercice.** Un record contenant une collection mutable ne la copie pas automatiquement. (1) Quel test détecterait l'oubli du second critère de tri ? (2) Que faudrait-il modifier en ajoutant un quatrième critère de recherche ?

Avant : [[Java - exceptions et collections]] · Après : [[Spring - HTTP DTO validation erreurs]] · Autre usage de records : [[Hexa Java]].
