# Données — SQL et migrations

**Définitions.** Une base relationnelle organise les données en tables liées par des clés ; SQL sert notamment à interroger ces tables. Une migration de schéma fait évoluer la structure ou les données avec une procédure testée et réversible ou sauvegardée. Ne pas confondre cette migration applicative avec la migration du **state Terraform** vers un backend distant ou avec un **import Terraform** d'une ressource existante.

**Exemple concret.** CloudShop prévoit catalogue → API → base relationnelle dans [`docs/00-notions-et-methode.md`, §0](https://github.com/Hermes-Maxirrx/cours-azure-finops-iac/blob/main/docs/00-notions-et-methode.md). [`docs/tp06-modules-sql-import.md`, §§3–4](https://github.com/Hermes-Maxirrx/cours-azure-finops-iac/blob/main/docs/tp06-modules-sql-import.md) décrit un serveur Azure SQL avec admin Entra et une base potentiellement serverless, puis l'import distinct d'une workspace Log Analytics. Le dépôt ne fournit **ni schéma SQL, ni scripts de migration de données, ni instance SQL créée** : préparer un plan de changement, sauvegarde, test et retour arrière avant tout vrai schéma.

**Piège.** Auto-pause du calcul SQL ne rend pas le stockage gratuit ; la règle pare-feu `0.0.0.0` du cahier autorise largement des services Azure, pas seulement l'API. Un import Terraform ne migre aucune ligne SQL.

**Questions.**
1. Quelles preuves demander avant de modifier la structure d'une table en production ?
2. Pourquoi le bloc `import` du TP6 ne déplace-t-il pas les données clients ?

**Liens :** [[Terraform - modules moved import]] · [[Cloud - architecture SLA RPO RTO]] · [[Fondations - index]]. Glossaire : [`DEFINITIONS.md`](https://github.com/Hermes-Maxirrx/cours-azure-finops-iac/blob/main/DEFINITIONS.md).
