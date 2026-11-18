# CI — jobs, artefacts et gates

**Définition.** Un workflow déclenché par `push`/`pull_request` contient des jobs isolés ; `needs` exprime leurs dépendances ; un artefact transfère un résultat entre jobs ; un *gate* tranche selon les verdicts. **Pourquoi :** faire échouer une livraison si une vérification nécessaire n'a pas eu lieu, plutôt qu'afficher un succès décoratif.

**Exemple réel.** Dans `.github/workflows/ci-cd.yml`, `build` exécute `./gradlew ... build`, publie `build/libs/app.jar` comme artefact `app` seulement si le build réussit, et conserve les rapports avec `if: always()`. `performance`, `sca` et `docker` téléchargent `app` ; `docker` dépend aussi de `performance`. `security-gate` lit les résultats de `docker`, `sca`, `secret-scan`, `dependency-submission`, `codeql` et `ansible-check`, et sur PR aussi `dependency-review`. Il refuse `skipped` et `failure` ; les jobs `deploy-*` en dépendent et ne lancent qu'Ansible simulé.

**Piège.** Un cache Gradle n'est pas un artefact de livraison ; `needs` n'est pas une règle de merge GitHub ; `if: always()` préserve les diagnostics, pas la réussite. Le fichier YAML et actionlint prouvent une configuration analysée localement, pas un run vert. `VERIFICATION.md` signale ensuite un échec de PR à `docker` et `security-gate` sans log distant accessible : ne pas en inventer la cause.

**Questions**
1. Pourquoi `docker` télécharge-t-il `app.jar` au lieu d'exécuter à nouveau `bootJar` ?
2. Sur un push, pourquoi `dependency-review` peut-il être ignoré sans être exigé par le gate, mais pas sur une PR ?

À relier : [[Tests - unitaire integration charge]], [[Securite - secret scan SCA SAST DAST]], [[GitHub - protections et prerequis prives]], [[CI CD DevSecOps]]. Sources locales : `.github/workflows/ci-cd.yml`, `docs/03-tests.md` (§3.10–3.15), `docs/05-securite.md` (§5.3), `VERIFICATION.md`.
