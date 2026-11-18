# Qualité DevOps — index

Parcours de révision de l'API `GET /hello` du [dépôt CI/CD DevSecOps](https://github.com/Hermes-Maxirrx/revision-ci-cd-devsecops) : **Git → CI → tests → image → simulation Ansible → sécurité → gouvernance GitHub**. Commencer par [[CI CD DevSecOps]], puis confronter chaque notion au fichier réel et aux preuves, plutôt que de déduire un succès de la présence d'un YAML.

1. [[Git - branches PR revues]] — isoler et relire une modification.
2. [[CI - jobs artefacts et gates]] — vérifier et transmettre le même `app.jar`.
3. [[Tests - unitaire integration charge]] — contrôler contrat Java, HTTP et seuils k6.
4. [[Conteneurs - image Docker et reseau]] — emballer le jar et distinguer ports interne/hôte.
5. [[Ansible - inventaires et simulation]] — paramètres dev/test/prod, refus de maintenance.
6. [[Securite - secret scan SCA SAST DAST]] — détecteurs complémentaires et limites.
7. [[GitHub - protections et prerequis prives]] — protections et fonctionnalités externes à vérifier.

**Fil rouge :** le workflow `.github/workflows/ci-cd.yml` est configuré ; les résultats locaux et les échecs attendus sont consignés dans `VERIFICATION.md`. L'audit complémentaire y mentionne une PR avec échecs `docker`/`security-gate` et étapes privées ignorées, sans journal distant récupéré : **ne pas affirmer que la CI est verte ou attribuer une alerte distante précise**. Les déploiements ne sont que des simulations localhost ; la prod est refusée, le ZAP local a des WARN et les prérequis GitHub privés ne sont pas réputés actifs.

**Piège transversal :** confondre configuration, exécution locale et preuve d'un contrôle distant.

**Questions**
1. Quel fichier produit le jar transmis entre jobs, et lequel décide si la suite peut simuler une livraison ?
2. Pourquoi un job `skipped` ne constitue-t-il pas un contrôle de sécurité réussi ?

Sources de ce parcours : `README.md`, `docs/02-github.md` à `docs/05-securite.md`, `SOURCES.md` et `VERIFICATION.md` dans le dépôt ci-dessus. [[CI CD DevSecOps]] explique l'origine des quatre supports du professeur.
