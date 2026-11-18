# Lilian — décision collective sans réservation

**Sujet.** `/home/max/lilian` est une application Node, SQLite et JavaScript pour choisir un lieu en groupe. Le catalogue distingue de vrais restaurants lyonnais sourcés et des hôtels parisiens fictifs. Les participants votent et disposent de trois veto par personne et par tour ; l'accord unanime produit un choix et un **brouillon copiable, non envoyé**. Il n'existe ni réservation effective, ni paiement, ni message transmis automatiquement à un établissement.

**Conception.** Le moteur conserve les états de groupe et impose côté serveur le quota de veto, le chrono et les transitions ; l'interface présente les choix sans devenir l'autorité des règles. Une transaction SQLite évite que deux actions simultanées consomment incohéremment le même quota. Les invitations et jetons personnels sont des capacités privées ; les préférences individuelles, notamment allergies et position, ne sont pas publiées au groupe ni insérées automatiquement dans le brouillon. Une disponibilité ou un tarif inconnu reste inconnu : `allergens: []` ne signifie jamais « sans allergènes ». La date de consultation des sources n'est pas une garantie de prix ou de disponibilité aujourd'hui.

**Ordre de lecture** (depuis la racine du dépôt) :
1. `README.md` — usage et limites.
2. `docs/v2-contract.md` — engagements de confidentialité et absence d'envoi.
3. `data/lyon-restaurants.json` — provenance et inconnues du catalogue.
4. `model.mjs` — règles et transactions.
5. `server.mjs` — contrats HTTP et frontière publique.

**Transfert et limites.** Comparer [[Concurrence - Consistance et isolation]], [[Contrats API - HTTP, idempotence et pagination]], [[SQL - Modéliser, transiger et indexer]] et [[UI - accessibilite et performance]]. Une QA datée et un tunnel temporaire n'établissent pas que le service est actuellement joignable. Confirmer directement prix, disponibilité et allergies avec le restaurant ; la copie du brouillon n'est pas une soumission.

**Question de révision.** Pourquoi faut-il contrôler le veto dans une transaction serveur plutôt que seulement désactiver un bouton après trois clics ?
