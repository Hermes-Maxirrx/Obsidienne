# Azure — ressources, régions, RBAC et Policy

**Définitions.** L'abonnement délimite gestion, accès et facturation ; un groupe de ressources rassemble des objets Azure ; ARM traite leurs opérations de gestion. Une région est un emplacement, une zone de disponibilité une séparation pour des services compatibles. Entra ID représente les identités ; RBAC dit *qui peut faire quoi à quelle portée* ; Policy évalue des règles de conformité sur les ressources.

**Exemple concret.** [`docs/tp01-prise-en-main.md`, §§2–4](https://github.com/Hermes-Maxirrx/cours-azure-finops-iac/blob/main/docs/tp01-prise-en-main.md) demande d'observer abonnement et affectations, puis de tester au niveau d'un groupe une Policy `Require a tag on resources` pour `owner`. Un rôle de création n'annule pas un effet `deny`. Le test réel, susceptible de créer une ressource facturable, n'a pas été fait dans ce cours.

**Piège.** Une région citée dans une commande n'est pas forcément autorisée : lire portée, paramètres et exclusions des affectations applicables, quota, SKU, disponibilité et coût dans **son** abonnement. La région d'un groupe ne force pas la région de toutes ses ressources ; une erreur RBAC n'est pas une preuve de refus Policy.

**Questions.**
1. Pourquoi une identité autorisée par RBAC peut-elle recevoir un refus de création ?
2. Quelles observations faut-il réunir avant de choisir une région pour CloudShop ?

**Liens :** [[Reseau - IP ports VNet et acces]] · [[GitHub Azure - OIDC et garde-fous]] · [[Cloud - index]]. Glossaire : [`DEFINITIONS.md`](https://github.com/Hermes-Maxirrx/cours-azure-finops-iac/blob/main/DEFINITIONS.md).
