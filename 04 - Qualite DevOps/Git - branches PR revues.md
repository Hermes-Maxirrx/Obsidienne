# Git — branches, PR, revues

**Définition.** Une branche isole des commits ; une pull request (PR) propose leur intégration et offre un lieu de discussion. Une revue humaine examine exigences, logique et risques que les tests n'expriment pas. **Pourquoi :** éviter qu'un changement sur `main` échappe à la discussion, sans imaginer qu'une branche protège à elle seule le serveur.

**Exemple réel.** `hello.txt` et la branche locale `feat/corrige-pedagogique` illustrent l'isolation ; `docs/02-github.md`, section « Fiche de revue du panier », formule des remarques sur `double` monétaire, `List<String[]>`, tests de non-nullité et notification réseau. Ce sont des **commentaires pédagogiques locaux**, pas des avis postés sur GitHub. Le workflow `.github/workflows/ci-cd.yml` écoute les PR ciblant `main` ou `develop` ; la branche cible est le filtre de `pull_request.branches`.

**Piège.** Une PR ouverte n'implique ni approbation, ni protection de branche ; une CI rouge locale ne prouve pas que GitHub bloque le merge. Après un nouveau commit, il faut faire relire l'état réellement proposé. Ne pousser ni secret pédagogique ni faute volontaire sur une branche partagée.

**Questions**
1. Quelle différence entre isoler une modification sur une branche et interdire un push direct sur `main` ?
2. Pourquoi le test `assertNotNull` du panier ne remplace-t-il pas une revue de son calcul ?

À relier : [[GitHub - protections et prerequis prives]], [[CI - jobs artefacts et gates]], [[CI CD DevSecOps]]. Source locale : `revision-ci-cd-devsecops/docs/02-github.md` (sections 2.2, 2.5–2.8 et fiche de revue) ; attribution des supports dans `SOURCES.md`.
