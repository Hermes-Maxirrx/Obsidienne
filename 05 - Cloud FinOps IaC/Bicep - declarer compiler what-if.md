# Bicep — déclarer, compiler, prévisualiser

**Définitions.** Bicep décrit des ressources Azure dans des fichiers `.bicep` ; sa compilation produit un modèle JSON ARM. Un module reçoit des paramètres et expose des sorties. `what-if` interroge Azure pour prévisualiser un déploiement ; il ne l'applique pas.

**Exemple concret.** Dans [`exemples/bicep/main.bicep`](https://github.com/Hermes-Maxirrx/cours-azure-finops-iac/blob/main/exemples/bicep/main.bicep), `targetScope = 'resourceGroup'` vise un groupe existant, `environment` sélectionne LRS en dev ou ZRS en prod, et `module storage 'modules/storage.bicep'` appelle [`exemples/bicep/modules/storage.bicep`](https://github.com/Hermes-Maxirrx/cours-azure-finops-iac/blob/main/exemples/bicep/modules/storage.bicep). Le module déclare un compte Storage et une politique `logs/` susceptible de supprimer des blobs si déployée. [`VERIFICATION.md`](https://github.com/Hermes-Maxirrx/cours-azure-finops-iac/blob/main/VERIFICATION.md) atteste uniquement la compilation locale du modèle pédagogique, pas un déploiement CloudShop complet.

**Étapes distinctes.** Lire le fichier → compiler localement (`bicep build`, hors dépôt pour le JSON généré) → si autorisé, examiner un `what-if` connecté et incomplet possible → seulement après accord, déployer et vérifier. Voir [`docs/tp04-bicep.md`, §§3–7](https://github.com/Hermes-Maxirrx/cours-azure-finops-iac/blob/main/docs/tp04-bicep.md).

**Piège.** Une compilation ne vérifie ni droits, ni quotas, ni prix. `what-if` exige des permissions et peut contenir des faux positifs ; même créer le groupe avant l'aperçu modifie Azure. Ne pas déployer l'exemple de politique destructrice.

**Questions.**
1. Quelle différence entre `build`, `what-if` et un déploiement ?
2. Où se trouve le risque de suppression de données dans l'exemple local ?

**Liens :** [[Terraform - config plan state]] · [[Azure - ressources regions RBAC Policy]] · [[Azure CloudShop]]. Glossaire : [`DEFINITIONS.md`](https://github.com/Hermes-Maxirrx/cours-azure-finops-iac/blob/main/DEFINITIONS.md).
