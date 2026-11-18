# Terraform — modules, moved et import

**Définitions.** Un module Terraform est un répertoire de fichiers `.tf` avec entrées et sorties ; il structure le code, sans isoler automatiquement dev et prod. `moved` indique qu'une adresse d'objet **déjà suivie dans le state** change ; `import` rattache un objet **déjà existant hors de ce state** à une adresse. Ni l'un ni l'autre ne migre les données métier.

**Exemple concret.** [`docs/tp06-modules-sql-import.md`, §1](https://github.com/Hermes-Maxirrx/cours-azure-finops-iac/blob/main/docs/tp06-modules-sql-import.md) montre `moved { from = azurerm_storage_account.web; to = module.frontend.azurerm_storage_account.this }` pour éviter une destruction/recréation due au changement d'adresse, sous réserve de vérifier le plan sur un state réel. Le §4 décrit l'import d'une workspace Log Analytics par son ID ARM complet. [`exemples/README.md`](https://github.com/Hermes-Maxirrx/cours-azure-finops-iac/blob/main/exemples/README.md) précise que l'exemple local n'inclut **ni moved, ni import, ni SQL, ni backend distant**.

**Isolation.** `env/dev.tfvars` fournit des variables ; `env/dev.backend.hcl` désigne le backend. Une autre `.tfvars` n'isole pas le state : contrôler compte, conteneur, clé, souscription et identité.

**Piège.** Un `moved` ne garantit pas un plan « zéro destruction » si les propriétés changent ; un import peut proposer une modification, et le plan seul n'écrit pas l'import dans le state. Retirer une ressource du code puis appliquer peut planifier sa destruction. Une bascule de `count` de 1 à 0 peut détruire SQL.

**Questions.**
1. Quand choisir `moved` plutôt qu'`import` ?
2. Que faudrait-il observer pour prouver un refactoring sans destruction ?

**Liens :** [[Terraform - config plan state]] · [[Donnees - SQL et migrations]] · [[Azure CloudShop]]. Glossaire : [`DEFINITIONS.md`](https://github.com/Hermes-Maxirrx/cours-azure-finops-iac/blob/main/DEFINITIONS.md).
