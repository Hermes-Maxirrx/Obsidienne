# Fondations — parcours de révision

**Idée simple.** Une requête web traverse plusieurs couches (nom, adresse, transport, sécurité, application) avant de lire des données ; comprendre chaque couche aide à localiser une panne.

1. [[Reseau - requete DNS TCP TLS HTTP]] — suivre une demande du navigateur à l'API.
2. [[Reseau - IP ports VNet et acces]] — adresses, sous-réseaux et filtres.
3. [[Donnees - SQL et migrations]] — données relationnelles et évolution du schéma.
4. [[Cloud - index]] puis [[Azure CloudShop]] — relier ces bases au projet.

**Exemple concret.** Le scénario `https://boutique.example/catalogue` du fichier [`docs/01-reseau-et-cloud-de-zero.md`](https://github.com/Hermes-Maxirrx/cours-azure-finops-iac/blob/main/docs/01-reseau-et-cloud-de-zero.md) décrit DNS → TCP → TLS → HTTP, puis l'appel du catalogue à une API et à une base ; ce nom est fictif.

**Piège.** Une page affichée ne prouve ni que l'API fonctionne ni que la base est accessible.

**Questions.**
1. Quelle couche vérifier si le nom du site ne se résout pas ?
2. Comment vérifier que le catalogue fonctionne au-delà de l'affichage de la page ?

**Référence de vocabulaire :** [`DEFINITIONS.md`](https://github.com/Hermes-Maxirrx/cours-azure-finops-iac/blob/main/DEFINITIONS.md) ; ne pas recopier tout le glossaire ici.
