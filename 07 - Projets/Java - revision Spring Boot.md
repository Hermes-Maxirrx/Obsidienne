# Java — révision Spring Boot

**Sujet.** Ce corrigé pédagogique, reconstruit à partir des cours de Corentin Beuchet, fait évoluer une bibliothèque de livres : identité ISBN et collections Java, recherche, interface HTTP, puis persistance PostgreSQL. Ce n'est ni le dépôt du professeur ni un ancien travail personnel de l'étudiant. Les modules du dépôt `/home/max/revision-java-spring-boot` sont indépendants : ils comparent des solutions successives plutôt que les couches d'une seule application à déployer.

**Conception.** La route HTTP traduit les paramètres et délègue au service. Dans la version persistante, celui-ci choisit un tri SQL explicite et encadre les écritures par une transaction ; une migration Liquibase crée la table et impose l'unicité de l'ISBN. Vérifier avant l'insertion améliore le message d'erreur, mais ne ferme pas la course entre deux requêtes : la contrainte de base reste décisive. Un test intégrant HTTP, JPA et une vraie base jetable apporte une autre preuve qu'un test de contrôleur avec dépendances simulées.

**Ordre de lecture** (chemins relatifs au dépôt) :
1. `README.md` — portée des modules et prérequis.
2. `tp4/src/main/java/fr/library/BookController.java` — frontière HTTP.
3. `tp5/src/main/java/fr/library/BookService.java` — transactions et tri.
4. `tp5/src/main/resources/db/changelog/changes/001-create-books-table.yaml` — schéma et unicité.
5. `tp5/src/test/java/fr/library/BookApiIT.java` — preuve d'intégration.

**Transfert et limites.** Relier [[Contrats API - HTTP, idempotence et pagination]], [[SQL - Modéliser, transiger et indexer]], [[Concurrence - Consistance et isolation]] et [[Tests - pyramide et TDD]] : aucun test isolé ne remplace une vérification à la frontière de la base. L'intégration requiert Docker ; les preuves locales consignées par le dépôt ne démontrent ni CI distante verte ni déploiement.

**Question de révision.** Pourquoi `existsByIsbn` ne suffit-il pas à garantir l'unicité lors de deux créations simultanées ?
