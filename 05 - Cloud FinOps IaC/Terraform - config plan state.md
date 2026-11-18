# Terraform — configuration, plan et state

**Définitions.** HCL (`.tf`) décrit l'état souhaité ; un provider parle à une API ; le state associe des adresses du code aux objets gérés ; un backend stocke cet état. `plan` compare configuration, state et généralement réalité distante ; `apply` peut créer, modifier ou supprimer.

**Exemple concret.** [`exemples/terraform/main.tf`](https://github.com/Hermes-Maxirrx/cours-azure-finops-iac/blob/main/exemples/terraform/main.tf) valide `environment`, choisit un sizing dans `locals`, puis transmet tags et sizing à `terraform_data.choix`. Le type intégré ne crée **aucune ressource Azure**. Les scénarios de [`exemples/terraform/tests/choix.tftest.hcl`](https://github.com/Hermes-Maxirrx/cours-azure-finops-iac/blob/main/exemples/terraform/tests/choix.tftest.hcl) vérifient dev, prod et le refus d'un environnement invalide ; [`VERIFICATION.md`](https://github.com/Hermes-Maxirrx/cours-azure-finops-iac/blob/main/VERIFICATION.md) rapporte trois tests locaux réussis, non un plan Azure.

**Séquence.** `fmt -check` vérifie le style ; `init -backend=false` prépare localement sans backend distant mais peut télécharger ; `validate` contrôle syntaxe/schémas ; un plan connecté peut lire Azure, verrouiller le state et révéler des secrets. La migration du state local vers backend distant demande sauvegarde, cible vérifiée et autorisation ; `-reconfigure` ne migre pas. Détails : [`docs/tp05-terraform-state.md`](https://github.com/Hermes-Maxirrx/cours-azure-finops-iac/blob/main/docs/tp05-terraform-state.md).

**Piège.** State et plan sauvegardé peuvent contenir des secrets ; ne pas les committer. Un state partagé et verrouillé ne bloque pas des changements manuels dans le portail. Un `apply` sans plan enregistré en recalcule un ; avec plan enregistré il l'applique sans nouvelle confirmation interactive.

**Questions.**
1. Pourquoi deux membres ne doivent-ils pas gérer les mêmes objets avec deux states locaux ?
2. Que prouve `terraform validate`, et que ne prouve-t-il pas ?

**Liens :** [[Terraform - modules moved import]] · [[Bicep - declarer compiler what-if]] · [[Cloud - index]]. Glossaire : [`DEFINITIONS.md`](https://github.com/Hermes-Maxirrx/cours-azure-finops-iac/blob/main/DEFINITIONS.md).
