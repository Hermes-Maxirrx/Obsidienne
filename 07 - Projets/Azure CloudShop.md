# Azure CloudShop — concevoir sans confondre plan et réalisation

**Sujet.** `/home/max/cours-azure-finops-iac` explique le cahier de cours Cloud, FinOps et IaC sur Azure autour de CloudShop : catalogue web, API, base relationnelle et environnements dev/prod. C'est un accompagnement pédagogique, pas le dépôt du groupe ni une preuve de réalisation. Les guides des exercices restent dans le dépôt source ; cette fiche privilégie les décisions transférables plutôt que l'énumération des travaux pratiques.

**Conception.** Avant de choisir région et services, vérifier identité, droits, politiques, quotas, localisation des données et budget. Relier objectifs de disponibilité et de reprise à une architecture argumentée, puis séparer estimation de coût et dépense mesurée. Bicep décrit un modèle de stockage partiel ; Terraform utilise localement `terraform_data` sans provider Azure ni backend distant. Compiler ou obtenir un plan vérifie une description, pas les droits réels, les prix, la conformité ou l'existence d'une infrastructure. Un état Terraform et ses plans peuvent exposer des données sensibles ; une alerte budget ne suspend pas automatiquement les dépenses.

**Ordre de lecture** (depuis la racine du dépôt) :
1. `README.md` — statut du parcours.
2. `docs/01-reseau-et-cloud-de-zero.md` — fondations des flux.
3. `docs/projet-final.md` — décisions et preuves attendues.
4. `exemples/README.md` — périmètre des deux exemples locaux.
5. `VERIFICATION.md` — résultats observés et manques.

**Transfert et limites.** Voir [[Cloud - responsabilite IAM reseau et fiabilite]], [[IaC - Bicep Terraform plan et state]], [[FinOps - couts budgets et FOCUS]], [[Réseau - Du nom DNS à la réponse HTTP]] et [[Git - branches PR CI CD et reproductibilite]]. Aucun déploiement ni connexion Azure n'est attesté. Le ZIP `cloudshop-etudiants.zip` et les slides sont absents : frontend, données FOCUS et cas de facture ne sont pas vérifiables. Les seuls contrôles réalisés concernent la compilation Bicep et des tests Terraform locaux ; ils ne valident ni SQL, ni import, ni coût réel.

**Question de révision.** Quelles preuves supplémentaires faudrait-il pour annoncer que CloudShop est déployé et que son coût réel est connu ?
