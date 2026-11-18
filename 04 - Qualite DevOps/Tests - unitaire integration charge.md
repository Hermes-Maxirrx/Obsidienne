# Tests — unitaire, intégration, charge

**Définitions.** Un test unitaire vérifie une petite unité sans démarrer Spring ; un test d'intégration HTTP démarre le contexte et interroge le serveur ; un test de charge mesure erreurs et latence sous requêtes répétées. **Pourquoi :** chaque niveau intercepte une classe différente de régression : texte Java, route/protocole, comportement sous charge.

**Exemple réel.** `src/test/java/ort/lyon/demo/HelloControllerTest.java` appelle directement le contrôleur et attend `Hello CI/CD`. `HelloHttpTest.java` utilise `@SpringBootTest(RANDOM_PORT)` et vérifie `GET /hello` (200, corps exact, Content-Type) et `/inconnu` (404). `perf/hello.js` vise uniquement loopback : 20 utilisateurs virtuels pendant 20 s, `http_req_failed < 1 %`, p95 < 200 ms, checks à 100 %. `build.gradle` configure JUnit Platform et Checkstyle ; `build/libs/app.jar` est ensuite testé comme livrable, pas seulement ses classes.

**Preuve et limite.** `VERIFICATION.md` conserve le RED unitaire, le RED de route absente, le build final et le run k6 **locaux**. Ces observations ne démontrent ni le blocage d'un merge GitHub ni la capacité de production. Les tests Python de parsing SARIF ne sont pas une exécution de CodeQL.

**Piège.** `contextLoads` seul peut réussir malgré une route absente ; un `check` k6 sans seuil n'est pas forcément bloquant ; 20 VUs ne signifient pas 20 requêtes.

**Questions**
1. Quel test devient rouge si la méthode Java renvoie le bon texte mais que le mapping `/hello` disparaît ?
2. Pourquoi p95 < 200 ms n'affirme-t-il pas que toutes les requêtes durent moins de 200 ms ?

À relier : [[CI - jobs artefacts et gates]], [[Conteneurs - image Docker et reseau]], [[CI CD DevSecOps]]. Sources locales : `docs/03-tests.md`, `perf/hello.js`, `VERIFICATION.md` ; voir aussi les parcours Java et TDD cités dans [[CI CD DevSecOps]].
