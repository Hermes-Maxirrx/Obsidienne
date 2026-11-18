# Réseau — IP, ports, VNet et accès

**Définitions.** Une IP désigne une destination réseau ; un port précise le service de transport visé (HTTPS généralement TCP 443). CIDR note une plage, par exemple `10.20.1.0/24`. Un VNet Azure est un réseau virtuel ; un subnet en est une subdivision. Un NSG filtre certains flux réseau ; un private endpoint donne à un service compatible une adresse privée dans le VNet.

**Exemple concret.** [`docs/01-reseau-et-cloud-de-zero.md`, §§3–4](https://github.com/Hermes-Maxirrx/cours-azure-finops-iac/blob/main/docs/01-reseau-et-cloud-de-zero.md) dessine un VNet fictif `10.20.0.0/16` avec deux sous-réseaux non chevauchants `10.20.1.0/24` et `10.20.2.0/24`. L'API doit pouvoir joindre la base, sans exposer celle-ci directement au navigateur. `10.20.1.10` illustre une IP privée ; `192.0.2.10` est réservée à la documentation, pas privée RFC 1918.

**Piège.** VNet, NSG ou private endpoint ne remplacent ni authentification ni DNS privé correct ; créer un private endpoint ne coupe pas automatiquement l'accès public. Les règles NSG sur private endpoints exigent une stratégie réseau adaptée au subnet et sa vérification effective.

**Questions.**
1. Pourquoi deux subnets seuls ne rendent-ils pas une base privée ?
2. Que faut-il vérifier en plus du private endpoint avant d'annoncer que l'accès public est fermé ?

**Liens :** [[Reseau - requete DNS TCP TLS HTTP]] · [[Azure - ressources regions RBAC Policy]] · [[Fondations - index]]. Compléments : [`DEFINITIONS.md`](https://github.com/Hermes-Maxirrx/cours-azure-finops-iac/blob/main/DEFINITIONS.md).
