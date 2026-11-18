# CI CD DevSecOps

**Projet de révision, non historique étudiant reconstitué.** Dépôt exact : [Hermes-Maxirrx/revision-ci-cd-devsecops](https://github.com/Hermes-Maxirrx/revision-ci-cd-devsecops) (local : `/home/max/revision-ci-cd-devsecops`). Corrigé reconstruit des exercices 2–5 de Corentin Beuchet autour d'une API Spring Boot `GET /hello → Hello CI/CD`, Java 25 / Gradle 9.8.0. Ne pas confondre dépôt de travail et quatre supports du professeur.

## Ordre de lecture

1. `README.md` et `docs/00-methode-de-revision.md` — périmètre, méthode, parcours.
2. `docs/02-github.md` puis [[Git - branches PR revues]] et [[GitHub - protections et prerequis prives]].
3. `docs/03-tests.md`, `src/test/java/ort/lyon/demo/HelloControllerTest.java`, `HelloHttpTest.java`, `perf/hello.js` — [[Tests - unitaire integration charge]].
4. `build.gradle`, `.github/workflows/ci-cd.yml` — [[CI - jobs artefacts et gates]].
5. `docs/04-docker-ansible.md`, `Dockerfile`, `.dockerignore`, `ansible/inventory/`, `ansible/group_vars/`, `ansible/playbook.yml` — [[Conteneurs - image Docker et reseau]] et [[Ansible - inventaires et simulation]].
6. `docs/05-securite.md`, `scripts/verify-trivy-report.py`, `scripts/check_sarif.py` — [[Securite - secret scan SCA SAST DAST]].
7. `docs/MATRICE.md`, `SOURCES.md` puis `VERIFICATION.md` et `docs/preuves/` — distinguer couverture, provenance et exécution. Revenir à [[DevOps - index]].

## Sources du professeur et adaptation

`SOURCES.md` du dépôt attribue les quatre snapshots de **Corentin Beuchet** : [ex. 2 secure-your-repository](https://github.com/corentinbeuchet/secure-your-repository/tree/5798c3dc8892fff4f2c194eb08b1b63009ddd41b), [ex. 3 automated-tests](https://github.com/corentinbeuchet/automated-tests/tree/7a63146c56b1ec6b5c1647807417fe25fe357e97), [ex. 4 deploy-with-ansible](https://github.com/corentinbeuchet/deploy-with-ansible/tree/1b23ec60af9d0006076909f9aeb832661e85695e), [ex. 5 manage-security](https://github.com/corentinbeuchet/manage-security/tree/4d7244f2e6331a4c66a17d1ce438a8061ba5d052). Ce sont des supports attribués, non des preuves d'une réalisation ancienne ou d'une licence libre globale des énoncés. Contextes complémentaires distincts : `/home/max/revision-java-spring-boot/README.md` et `/home/max/revision-test-driven-development/README.md`.

## Ce qui est établi et ce qui ne l'est pas

- Le même `build/libs/app.jar` alimente les essais locaux et l'image ; des résultats locaux précis et les refus attendus figurent dans `VERIFICATION.md`. Cette note ne relance ni scan, ni build, ni conteneur.
- Ansible est une **simulation localhost** (`fail`, `assert`, `debug`) : pas de serveur déployé ; `prod.yml` garde `maintenance_mode: true` et le **refus production**. Le port 80 n'est qu'une donnée théorique.
- Le ZAP local a retourné **WARN (code 2, trois types)** ; le gate strict ne le convertit pas en succès. Les autres scans ont leurs périmètres et dates propres.
- Les protections de branches, la revue, les secrets, le graphe de dépendances, CodeQL/Dependency Review et l'offre de l'**organisation privée** demandent une vérification/une décision humaine. `CODE_SECURITY_ENABLED` n'est pas une licence et les contrôles ignorés ne valent pas réussite.
- `README.md` et la section initiale de `VERIFICATION.md` disent ne connaître aucun run GitHub au moment de leur rédaction ; le **complément ultérieur** de `VERIFICATION.md` mentionne une PR dont `docker` (DAST) et `security-gate` ont échoué, avec jobs privés ignorés, mais sans accès au journal distant (HTTP 401). L'état actuel de la CI est **inconnu** : ne pas dire « CI verte », ni extrapoler la cause exacte de l'échec distant.

**Piège.** Prendre une simulation ou une vérification statique du YAML pour une preuve de livraison réelle.

**Questions**
1. Où trouver la preuve que la prod a été refusée et non déployée ?
2. Quel élément manque pour transformer une règle GitHub proposée en protection démontrée ?
