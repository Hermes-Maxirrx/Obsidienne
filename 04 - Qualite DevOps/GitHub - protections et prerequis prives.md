# GitHub — protections et prérequis privés

**Définition.** Une protection de branche règle les conditions de push/merge sur le service GitHub (PR, revue, checks requis, éventuelles exemptions) ; elle est distincte de `needs` dans Actions. **Pourquoi :** un workflow qui tourne ne bloque pas automatiquement le merge, et une revue ne remplace pas les tests.

**Exemple réel.** `docs/02-github.md` propose pour `main`/`develop` PR, approbation, invalidation des avis périmés, absence de bypass et sélection des **noms de checks réellement observés** (dont `build`, `performance`, `security-gate`). Ce sont des procédures, **pas des réglages activés**. `.github/workflows/ci-cd.yml` conditionne `dependency-submission`, `dependency-review` (PR seule) et `codeql` à `CODE_SECURITY_ENABLED` et aux droits/conditions de l'événement ; `security-gate` refuse un contrôle obligatoire ignoré. Le dépôt est **privé dans une organisation**, contrairement aux dépôts publics personnels du cours. Vérifier offre, options Code Security/Advanced Security, graphe de dépendances, branche `develop`, permissions, reviewers et secret Actions avec le propriétaire avant opt-in ; ne pas inférer ces droits d'un compte personnel.

**Piège.** Un badge README, un fichier Dependabot ou une action épinglée ne prouvent ni un run vert, ni un check obligatoire, ni une licence privée disponible. N'activez pas des fonctions payantes, ne créez pas de secret ou de règle de branche pour satisfaire une fiche de cours. L'audit complémentaire de `VERIFICATION.md` signale des jobs privés ignorés lors d'une PR et un gate en échec ; le détail distant n'a pas pu être lu.

**Questions**
1. Quelle preuve distante faut-il avant de sélectionner `security-gate` comme check requis ?
2. Pourquoi `CODE_SECURITY_ENABLED=true` ne suffit-il pas, à lui seul, à rendre CodeQL et Dependency Review utilisables ?

À relier : [[Git - branches PR revues]], [[CI - jobs artefacts et gates]], [[CI CD DevSecOps]]. Sources locales : `docs/02-github.md`, `docs/05-securite.md`, `.github/workflows/ci-cd.yml`, `SOURCES.md` (§conditions privées), `VERIFICATION.md`.
