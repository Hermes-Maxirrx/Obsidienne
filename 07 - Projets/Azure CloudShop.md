# Azure CloudShop — fiche de projet

**Statut : parcours de révision, non réalisation du groupe.** Le cahier Word « 4OLEN · Architecture Cloud, FinOps & IaC sur Azure » est la source des consignes ; [`README.md`](https://github.com/Hermes-Maxirrx/cours-azure-finops-iac/blob/main/README.md) et [`VERIFICATION.md`](https://github.com/Hermes-Maxirrx/cours-azure-finops-iac/blob/main/VERIFICATION.md) distinguent explications et preuves. **Aucune ressource Azure n'a été déployée** pour ce cours ; pas de connexion Azure, pipeline ni facture réelle attestés. Le ZIP `cloudshop-etudiants.zip` et les slides PowerPoint sont absents ; frontend, bootstrap, CSV FOCUS et cas facture ne sont donc pas inspectables. Les exemples Bicep/HCL du dépôt sont **locaux, pédagogiques et partiels** (stockage Bicep ; `terraform_data` sans provider Azure), pas le livrable CloudShop complet.

**Besoin.** Catalogue web → API → base relationnelle ; deux environnements dev/prod. Le cahier fixe des objectifs de disponibilité, RPO/RTO et coûts à **évaluer**, pas une disponibilité acquise ni un tarif garanti. Choisir la région après observation des Policy, quotas, droits, services et localisation des données ; aucune région autorisée n'est connue ici. Voir [[Reseau - requete DNS TCP TLS HTTP]], [[Donnees - SQL et migrations]] et [[Cloud - architecture SLA RPO RTO]].

## Parcours des neuf TP

| Étape | Travail et preuve à produire (pas encore attestée) | Source |
|---|---|---|
| TP1 | Inventorier abonnement, RBAC, Policy, régions et quotas ; tester et nettoyer un groupe isolé si autorisé. | [`docs/tp01-prise-en-main.md`](https://github.com/Hermes-Maxirrx/cours-azure-finops-iac/blob/main/docs/tp01-prise-en-main.md) |
| TP2 | Dessiner dev/prod, flux, WAF, ADR région/API et hypothèses SLA/RPO/RTO, sans déployer. | [`docs/tp02-architecture.md`](https://github.com/Hermes-Maxirrx/cours-azure-finops-iac/blob/main/docs/tp02-architecture.md) |
| TP3 | Analyser les CSV FOCUS/métriques après réception ; estimer dev/prod et noter hypothèses, couverture, anomalies. | [`docs/tp03-finops.md`](https://github.com/Hermes-Maxirrx/cours-azure-finops-iac/blob/main/docs/tp03-finops.md) |
| TP4 | Construire modules Bicep stockage/API, compiler localement puis what-if et déploiement **seulement si autorisés**. | [`docs/tp04-bicep.md`](https://github.com/Hermes-Maxirrx/cours-azure-finops-iac/blob/main/docs/tp04-bicep.md) |
| TP5 | Décrire Terraform, comprendre state local puis migration prudente vers backend distant et verrouillage. | [`docs/tp05-terraform-state.md`](https://github.com/Hermes-Maxirrx/cours-azure-finops-iac/blob/main/docs/tp05-terraform-state.md) |
| TP6 | Modulariser avec `moved`, séparer states dev/prod, étudier SQL, importer une ressource existante et lire le plan. | [`docs/tp06-modules-sql-import.md`](https://github.com/Hermes-Maxirrx/cours-azure-finops-iac/blob/main/docs/tp06-modules-sql-import.md) |
| TP7 | Concevoir OIDC, CI/plan et CD dev → prod avec revue et protection vérifiées. | [`docs/tp07-oidc-ci-cd.md`](https://github.com/Hermes-Maxirrx/cours-azure-finops-iac/blob/main/docs/tp07-oidc-ci-cd.md) |
| TP8 | Concevoir policies, budget/alertes, estimation de PR et dérive ; destruction programmée **non activée** sans opt-in distinct. | [`docs/tp08-finops-as-code.md`](https://github.com/Hermes-Maxirrx/cours-azure-finops-iac/blob/main/docs/tp08-finops-as-code.md) |
| TP9 | Auditer **statiquement** le cas de facture quand son dossier sera reçu ; aucun déploiement demandé. | [`docs/tp09-facture.md`](https://github.com/Hermes-Maxirrx/cours-azure-finops-iac/blob/main/docs/tp09-facture.md) |

**Livrable final.** [`docs/projet-final.md`](https://github.com/Hermes-Maxirrx/cours-azure-finops-iac/blob/main/docs/projet-final.md) demande dépôt du groupe, architecture et ADR, IaC modulaire, CI/CD, FinOps, preuves et soutenance. À marquer « non disponible/non exécuté » tant que les fichiers, autorisations et résultats manquent. Les seules vérifications citées dans [`VERIFICATION.md`](https://github.com/Hermes-Maxirrx/cours-azure-finops-iac/blob/main/VERIFICATION.md) sont compilation Bicep et tests Terraform **locaux** ; elles ne valident ni SQL, ni import, ni Azure.

**Pièges.** `what-if`/`plan` ≠ création ; une alerte budget **n'arrête pas la facturation** ; ne pas inventer chiffres de facture, région permise ou SLA effectif. Ne jamais publier secret, state ou plan sensible ; toute suppression demande inventaire, sauvegarde, portée et accord.

**Questions.**
1. Quelles preuves manquent pour annoncer un coût réel et un déploiement CloudShop ?
2. Pourquoi un plan Terraform local réussi ne suffit-il pas à valider le TP6 ?

**Navigation :** [[Fondations - index]] · [[Cloud - index]] · [[FinOps - couts FOCUS allocation]] · [[Bicep - declarer compiler what-if]] · [[Terraform - modules moved import]] · [[GitHub Azure - OIDC et garde-fous]]. Glossaire central : [`DEFINITIONS.md`](https://github.com/Hermes-Maxirrx/cours-azure-finops-iac/blob/main/DEFINITIONS.md).
