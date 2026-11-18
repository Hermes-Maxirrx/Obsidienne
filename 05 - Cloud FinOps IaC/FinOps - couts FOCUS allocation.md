# FinOps — coûts, FOCUS et allocation

**Définitions.** FinOps relie consommation cloud, besoins et décisions. Une estimation dépend d'hypothèses ; le coût observé dépend des données réelles. FOCUS normalise les données de coût et d'utilisation. Une allocation affecte les coûts à des environnements ou équipes suivant des règles explicites ; un tag seul ne couvre pas tous les frais.

**Exemple concret.** [`docs/tp03-finops.md`, §§A–B](https://github.com/Hermes-Maxirrx/cours-azure-finops-iac/blob/main/docs/tp03-finops.md) prévoit `finops/focus_cloudshop_2026Q3.csv` dans le ZIP absent : lire `ChargePeriodStart`, `EffectiveCost` et `Tags`, vérifier devise, dates et corrections, puis regrouper par mois et tag `environment`. Séparer tags absents, JSON invalide, valeur manquante et coûts partagés. `EffectiveCost` et `BilledCost` ne s'additionnent pas. Aucune somme de facture n'est calculable sans fichier.

**Méthode.** Documenter région, SKU, durée, volume, devise et date pour l'estimation ; comparer au coût observé sur le même périmètre ; proposer un levier avec gain et risque, sans double compte. [`docs/tp08-finops-as-code.md`, §1](https://github.com/Hermes-Maxirrx/cours-azure-finops-iac/blob/main/docs/tp08-finops-as-code.md) décrit budget et alertes : **alerte ≠ plafond ni arrêt automatique**.

**Piège.** Une métrique CPU basse ne prouve pas qu'une ressource est inutile ; un coût sans tag n'est pas nul. Prix publics et facture contractuelle diffèrent éventuellement.

**Questions.**
1. Comment calculer le taux non alloué sans perdre les lignes aux tags invalides ?
2. Que permet une alerte de budget, et qu'est-ce qu'elle ne fait pas ?

**Liens :** [[Cloud - architecture SLA RPO RTO]] · [[Azure CloudShop]] · [[Cloud - index]]. Glossaire : [`DEFINITIONS.md`](https://github.com/Hermes-Maxirrx/cours-azure-finops-iac/blob/main/DEFINITIONS.md).
