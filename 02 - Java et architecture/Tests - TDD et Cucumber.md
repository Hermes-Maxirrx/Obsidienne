# Tests — TDD et Cucumber

**Idée.** RED → GREEN → REFACTOR : observer un échec pertinent, faire le minimum pour passer, simplifier sans changer la règle. En double boucle, le scénario d'acceptation HTTP encadre les tests unitaires métier.

**Où apprendre.** [[TDD - revision double boucle]] : `docs/01-kata.md`, `kata/src/test/java/fr/library/fees/LateFeeCalculatorTest.java`, `LateFeeCalculator.java` côté main ; puis `docs/02-double-boucle.md`, `emprunts/src/test/resources/features/emprunter-un-livre.feature`, `emprunts/src/test/java/fr/library/loans/cucumber/LoanSteps.java`.

**Exemple du code.** La règle des frais passe du `return 0` aux jours facturés et au plafond ; les scénarios d'emprunt font `post("/loans")` via MockMvc tandis que `LoanServiceTest` exerce le service seul. Le hook Cucumber `@Before` remet les données à zéro.

**Piège / exercice.** Une erreur de glue Java n'est pas un RED métier, et MockMvc n'est pas un déploiement. (1) Quel test prouve le statut `409` ? (2) Pourquoi un test service vert ne suffit-il pas à garantir le JSON d'erreur ?

Voir aussi [[Spring - HTTP DTO validation erreurs]] et [[Architecture - hexagonale et ports]].
