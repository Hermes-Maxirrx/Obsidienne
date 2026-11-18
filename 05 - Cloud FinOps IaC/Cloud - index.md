# Cloud — parcours Azure, FinOps et IaC

**Idée simple.** Décrire une infrastructure, estimer son coût et contrôler son accès sont trois tâches distinctes ; un fichier IaC n'est pas une ressource déployée.

**Parcours.** [[Azure - ressources regions RBAC Policy]] → [[Cloud - architecture SLA RPO RTO]] → [[FinOps - couts FOCUS allocation]] → [[Bicep - declarer compiler what-if]] → [[Terraform - config plan state]] → [[Terraform - modules moved import]] → [[GitHub Azure - OIDC et garde-fous]] → [[Azure CloudShop]]. Pour les prérequis : [[Fondations - index]].

**Exemple concret.** Le [`README.md` du cours, « Déroulé des exercices »](https://github.com/Hermes-Maxirrx/cours-azure-finops-iac/blob/main/README.md) suit CloudShop de l'architecture (TP2) aux coûts (TP3), à Bicep (TP4), Terraform (TP5–6), CI/OIDC (TP7), garde-fous FinOps (TP8), puis au cas statique (TP9). Aucun déploiement Azure n'est attesté.

**Piège.** Un `what-if` ou `plan` prédit des changements sans prouver leur réalisation ; une alerte budgétaire ne plafonne pas les dépenses.

**Questions.**
1. Quelles preuves distinguent un fichier Terraform validé localement d'une ressource Azure créée ?
2. Quel document faut-il consulter avant d'affirmer un montant réel de facture ?

**Vocabulaire :** [`DEFINITIONS.md`](https://github.com/Hermes-Maxirrx/cours-azure-finops-iac/blob/main/DEFINITIONS.md).
