# GitHub et Azure — OIDC et garde-fous

**Définitions.** OIDC permet à un job GitHub de présenter un jeton temporaire à Microsoft Entra ; la confiance fédérée vérifie émetteur, audience et sujet (`sub`). RBAC limite ensuite les actions Azure. CI contrôle le code ; CD organise le déploiement. Une PR relue n'est ni fusion ni déploiement.

**Exemple concret.** [`docs/tp07-oidc-ci-cd.md`, §§1–3](https://github.com/Hermes-Maxirrx/cours-azure-finops-iac/blob/main/docs/tp07-oidc-ci-cd.md) demande des identités et backends dev/prod distincts, des checks qualité puis un plan, une revue indépendante et un déploiement dev → prod protégé. `id-token: write` autorise la demande de jeton, **pas** l'accès Azure à lui seul. Le `sub` exact dépend du contexte (PR, branche, environment) et du format effectif du dépôt : ne pas recopier aveuglément un exemple historique. Aucune CI/CD n'est installée dans le dépôt de cours.

**Garde-fous.** Restreindre rôles et portée, vérifier la protection réellement disponible pour un dépôt privé, épingler les actions, ne pas exécuter une PR non fiable avec des jetons privilégiés et ne pas publier plans/state. [`docs/tp08-finops-as-code.md`, §§1–4](https://github.com/Hermes-Maxirrx/cours-azure-finops-iac/blob/main/docs/tp08-finops-as-code.md) distingue Policy, budget, estimation et dérive : `plan -detailed-exitcode` renvoie 0 pour plan vide, 1 pour erreur, 2 pour changements (pas nécessairement dérive). Un destroy planifié exige un accord distinct et n'est pas activé.

**Piège.** Approbation avant un plan créé dans le même job ≠ approbation de ce plan ; alerte de budget ≠ arrêt des frais. Certaines protections d'environment peuvent être indisponibles selon l'offre et la visibilité du dépôt.

**Questions.**
1. Pourquoi une authentification OIDC réussie peut-elle aboutir à un refus 403 sur le state ?
2. Pourquoi le code retour 2 d'un plan ne démontre-t-il pas une dérive manuelle ?

**Liens :** [[Azure - ressources regions RBAC Policy]] · [[Terraform - config plan state]] · [[FinOps - couts FOCUS allocation]] · [[Azure CloudShop]]. Vocabulaire : [`DEFINITIONS.md`](https://github.com/Hermes-Maxirrx/cours-azure-finops-iac/blob/main/DEFINITIONS.md).
