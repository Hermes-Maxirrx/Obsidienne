# TDD — révision et double boucle

**Sujet.** Ce corrigé reconstruit depuis les supports de Corentin Beuchet explore le développement piloté par les tests, du calcul de pénalités à l'emprunt de livres. Il n'est ni un dépôt de TP de l'enseignant ni la trace d'un ancien historique personnel. Dans `/home/max/revision-test-driven-development`, le kata travaille les règles de grâce, tarif et plafond ; les scénarios d'emprunt décrivent les effets attendus à la frontière de l'application.

**Conception.** La boucle externe formule le résultat métier avec Cucumber et exerce aussi la réponse HTTP ; la boucle interne pousse des cas unitaires courts jusqu'aux bornes. Écrire un test rouge, produire le minimum pour le rendre vert, puis refactorer évite d'attribuer au test des garanties qu'il n'a pas. Dans le service d'emprunt, les refus sont vérifiés avant toute mutation : un livre inconnu ou déjà emprunté ne doit pas modifier le catalogue. Une `Map` en mémoire rend la règle visible, mais ne fournit ni persistance ni sérialisation des demandes concurrentes.

**Ordre de lecture** (depuis la racine du dépôt) :
1. `README.md` — périmètre et statut des preuves.
2. `kata/src/test/java/fr/library/fees/LateFeeCalculatorTest.java` — exemples et valeurs frontières.
3. `kata/src/main/java/fr/library/fees/LateFeeCalculator.java` — règle extraite des tests.
4. `emprunts/src/test/resources/features/emprunter-un-livre.feature` — intention externe.
5. `emprunts/src/main/java/fr/library/loans/LoanService.java` — invariant et limites du stockage.

**Transfert et limites.** Comparer [[Tests - pyramide et TDD]], [[Contrats API - HTTP, idempotence et pagination]] et [[Concurrence - Consistance et isolation]] : un scénario fonctionnel et un test unitaire n'observent pas les mêmes frontières. Les preuves RED/GREEN consignées concernent des cycles précis, non chaque test. Aucune expérience IA non faite, CI distante réussie ou robustesse multi-requêtes n'est déduite de ce dépôt.

**Question de révision.** Pourquoi un test unitaire vert du service n'établit-il pas le statut HTTP d'un emprunt refusé ?
