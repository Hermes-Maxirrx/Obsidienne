# TDD — révision et double boucle

**Nature.** Corrigé pédagogique reconstruit depuis les supports de Corentin Beuchet, **pas** ses dépôts de TP ni un historique TDD personnel de l'étudiant. Dépôt local : `/home/max/revision-test-driven-development` ; remote `origin` : https://github.com/Hermes-Maxirrx/revision-test-driven-development (potentiellement privé).

**Parcours fichiers (depuis la racine).** `README.md` → `SOURCES.md` → `docs/00-methode-de-revision.md` → `docs/01-kata.md` → `kata/src/test/java/fr/library/fees/LateFeeCalculatorTest.java` → `kata/src/main/java/fr/library/fees/LateFeeCalculator.java`, `kata/src/main/java/fr/library/fees/Loan.java` → `docs/02-double-boucle.md` → `emprunts/src/test/resources/features/emprunter-un-livre.feature` → `emprunts/src/test/java/fr/library/loans/cucumber/LoanSteps.java` → `emprunts/src/test/java/fr/library/loans/LoanServiceTest.java` → `emprunts/src/main/java/fr/library/loans/LoanService.java`, `emprunts/src/main/java/fr/library/loans/LoanController.java`, `emprunts/src/main/java/fr/library/loans/LoanExceptionHandler.java` → `docs/03-tdd-et-ia.md` → `VERIFICATION.md`.

**À retenir.** Le kata montre les cycles RED/GREEN documentés ; la boucle externe Cucumber/MockMvc vérifie HTTP, l'interne la règle. Les logs de `preuves/` distinguent erreurs de compilation, échecs métier et succès locaux ; ni expérience IA non effectuée ni CI distante verte n'est revendiquée.

Concepts : [[Tests - TDD et Cucumber]] · [[Java - exceptions et collections]] · [[Spring - HTTP DTO validation erreurs]] · [[Java - index]].
