# CI/CD DevSecOps — contrôles et preuves

**Sujet.** `/home/max/revision-ci-cd-devsecops` est un corrigé pédagogique reconstruit des exercices de Corentin Beuchet, pas un historique de livraison de l'étudiant. Une petite API Spring Boot `GET /hello` sert de support à la chaîne Git, tests, artefact Java, image Docker, simulation Ansible et sécurité. Le but n'est pas de montrer une production déjà livrée, mais d'apprendre ce que chaque contrôle vérifie réellement.

**Conception.** Le workflow fabrique un seul jar testé avant de le transmettre aux jobs de performance, scan et image ; l'image n'est pas poussée dans un registre. Les branches et les besoins entre jobs expriment des conditions de passage, pas une protection GitHub déjà active. Les scans SAST, SCA, secrets, image et DAST couvrent des surfaces différentes ; un rapport absent, un job ignoré requis ou un avertissement bloquant ne vaut pas validation. Le playbook Ansible est limité à `localhost`, aux assertions et au refus de production en maintenance : ce n'est pas un déploiement sur serveur.

**Ordre de lecture** (depuis la racine du dépôt) :
1. `README.md` — périmètre de reconstruction.
2. `.github/workflows/ci-cd.yml` — graphe des jobs et garde-fous.
3. `Dockerfile` — packaging du jar déjà produit.
4. `ansible/playbook.yml` — simulation et refus.
5. `VERIFICATION.md` — observations et inconnues.

**Transfert et limites.** Croiser [[Git - branches PR CI CD et reproductibilite]], [[Tests - pyramide et TDD]], [[Docker et Ansible - images reseaux deploiement]] et [[DevSecOps - SAST SCA DAST et secrets]]. Des vérifications locales sont documentées, ainsi qu'un échec signalé sur une PR pour `docker`/DAST et `security-gate` sans journal distant accessible. Le résultat **actuel** de la CI demeure inconnu ; les contrôles soumis à des prérequis d'organisation privée et les protections de branche doivent être constatés, non supposés.

**Question de révision.** Pourquoi un job de sécurité ignoré ne peut-il pas être traité comme une preuve de scan réussi ?
