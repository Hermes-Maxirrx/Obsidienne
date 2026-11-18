# Git — branches, PR, CI/CD et reproductibilité

## De la proposition à la livraison

Git enregistre des instantanés reliés par un historique ; une branche est une référence mobile vers un commit. Une branche courte isole une proposition, tandis qu'une demande de fusion (PR) offre un lieu de discussion, de revue et de vérification. La revue examine le comportement, les risques et la lisibilité ; elle n'est pas une garantie mathématique. Après fusion, le commit cible et les artefacts associés doivent rester identifiables. Les protections et contrôles automatiques varient selon la plateforme et la configuration : leur présence ne doit pas être supposée ([Git : Branches](https://git-scm.com/book/en/v2/Git-Branching-Branches-in-a-Nutshell)).

```mermaid
flowchart LR
  A[Branche de travail] --> B[PR et revue]
  B --> C[CI : construire et tester]
  C -->|contrôles validés| D[Fusion]
  D --> E[Artefact identifié]
  E --> F[Déploiement autorisé]
```

## Ce que la CI vérifie réellement

L'intégration continue exécute des contrôles sur une révision et un environnement donnés : format, analyse statique, tests, construction ou scans. Un résultat vert signifie que ces contrôles ont réussi sur **ce commit**, pas qu'une autre révision ou un autre environnement fonctionne. Il faut savoir quelle révision a été testée et si le commit fusionné diffère du commit de la PR. Les étapes concurrentes doivent déclarer leurs dépendances ; une étape de publication ne doit pas se produire avant les validations nécessaires. Une stratégie de « gate » décide ce qui bloque et ce qui alerte, selon le risque plutôt que le seul nombre d'outils.

La livraison continue prépare un candidat toujours publiable ; le déploiement continu automatise aussi sa mise en production après les contrôles définis. Ces concepts ne disent pas qu'un dépôt particulier est effectivement déployé. L'approbation, la séparation des environnements et la stratégie de retour arrière dépendent du service. Un rollback de code ne répare pas forcément une migration de données déjà appliquée. L'identité de l'artefact importe : reconstruire plus tard à partir d'une branche mouvante peut produire autre chose que ce qui a été testé.

## Reproductibilité et provenance

Pour rapprocher deux exécutions, on fixe les dépendances via un fichier de verrouillage, on explicite la configuration et on trace l'outil et la révision utilisés. Un environnement épinglé réduit les surprises, mais implique un entretien régulier : figer indéfiniment des dépendances laisse vieillir les correctifs de sécurité. Les secrets ne sont pas des paramètres à enregistrer dans Git ; ils doivent être fournis par des mécanismes adaptés avec droits minimaux. Une construction reproductible au sens strict exige aussi de contrôler les entrées cachées, comme l'heure, le réseau et les générateurs non déterministes ; un lockfile seul n'en est pas la preuve.

**Erreur courante :** considérer « CI verte » comme synonyme de production saine. La CI ne couvre que ses assertions ; observabilité et validation après publication répondent à d'autres questions. **Trade-off :** multiplier les validations peut ralentir la boucle ; on privilégie des contrôles fiables et rapides au début, puis des vérifications plus coûteuses là où le risque le justifie. Voir la documentation de la plateforme pour la portée effective de ses événements et permissions ([GitHub Actions : Workflow syntax](https://docs.github.com/en/actions/reference/workflows-and-actions/workflow-syntax)).

## Questions de révision

- Quelle révision et quel artefact une CI verte attestent-ils ?
- Pourquoi reconstruire après validation peut-il changer le résultat livré ?
- Quelle différence entre livraison continue et déploiement continu ?

Liens : [[Tests - pyramide et TDD]], [[DevSecOps - SAST SCA DAST et secrets]]. Retour à [[Qualite DevOps - index]].