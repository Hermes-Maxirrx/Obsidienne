# Cloud — architecture, SLA, RPO et RTO

**Définitions.** Une architecture nomme composants, flux et dépendances. Un SLA est un engagement contractuel conditionnel ; le RPO est la perte de données maximale acceptable exprimée en temps, le RTO le délai cible de rétablissement. Aucun ne prouve seul la disponibilité de bout en bout.

**Exemple concret.** [`docs/tp02-architecture.md`, §§1–4](https://github.com/Hermes-Maxirrx/cours-azure-finops-iac/blob/main/docs/tp02-architecture.md) demande de dessiner frontend → API → base pour dev/prod, de discuter sauvegardes, identité et redondance, puis de calculer un SLA composite **pédagogique** en multipliant des fractions pour des composants en série *si* les pannes sont indépendantes. Les cibles du cahier sont disponibilité mensuelle 99,5 %, RPO 1 h et RTO 4 h ; ce ne sont pas des performances démontrées. Vérifier les contrats applicables aux services et configurations choisis et tester une restauration.

**Piège.** Les pannes peuvent être corrélées : multiplier des SLA ne donne pas la disponibilité réelle. Une sauvegarde non restaurée ne prouve pas le RTO. Deux zones ne couvrent pas automatiquement une panne régionale ni la localisation de toutes les données.

**Questions.**
1. Quelle mesure et quel test établiraient que l'API et la base reprennent dans le délai visé ?
2. Pourquoi ne faut-il pas annoncer le produit des SLA comme engagement du site complet ?

**Liens :** [[Donnees - SQL et migrations]] · [[FinOps - couts FOCUS allocation]] · [[Azure CloudShop]]. Définitions détaillées : [`DEFINITIONS.md`](https://github.com/Hermes-Maxirrx/cours-azure-finops-iac/blob/main/DEFINITIONS.md).
