# Réseau — requête DNS, TCP, TLS, HTTP

**Définitions.** DNS associe un nom à une adresse IP ; TCP établit un transport fiable et ordonné ; TLS chiffre l'échange et vérifie le certificat du serveur ; HTTP porte la requête et la réponse applicatives. HTTPS est HTTP protégé par TLS (dans le scénario TCP décrit ici).

**Parcours.** Pour `https://boutique.example/catalogue` : résolution DNS (éventuellement en cache), routage vers l'IP et le port, connexion TCP, négociation TLS, puis `GET /catalogue` et réponse HTTP. Le navigateur peut ensuite appeler l'API pour récupérer des données JSON. Exemple détaillé dans [`docs/01-reseau-et-cloud-de-zero.md`, §2](https://github.com/Hermes-Maxirrx/cours-azure-finops-iac/blob/main/docs/01-reseau-et-cloud-de-zero.md). Aucun de ces services fictifs n'est déployé.

**Diagnostic.** Nom introuvable → DNS ; délai/connexion refusée → route, port ou service ; erreur de certificat → TLS ; code 404 → réponse HTTP, pas nécessairement panne réseau. Une connexion peut être réutilisée ; HTTP/3 passe par QUIC/UDP plutôt que TCP.

**Piège.** DNS ne chiffre pas la connexion ; certificat valide ne garantit pas l'honnêteté de l'application. Un `200` sur la page n'assure pas la lecture des produits en base.

**Questions.**
1. Pourquoi une résolution DNS réussie ne suffit-elle pas à garantir une connexion sûre ?
2. Si la page s'affiche mais pas les produits, quelle autre requête faut-il examiner ?

**Liens :** [[Reseau - IP ports VNet et acces]] · [[Donnees - SQL et migrations]] · [[Fondations - index]]. Vocabulaire : [`DEFINITIONS.md`](https://github.com/Hermes-Maxirrx/cours-azure-finops-iac/blob/main/DEFINITIONS.md).
