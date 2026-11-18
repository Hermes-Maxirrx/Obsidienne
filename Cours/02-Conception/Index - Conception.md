# Conception — Parcours de lecture

Ces notes abordent la structure du code, les frontières métier et les conséquences d'une communication entre composants autonomes.

1. [[Conception modulaire - SOLID, composition et injection]] — séparer les raisons de changer sans multiplier les abstractions.
2. [[Architecture hexagonale - Domaine, ports et limites]] — placer la politique métier au centre et choisir les frontières.
3. [[Événements - Outbox, inbox et reprises]] — relier transaction locale et livraison avec des doublons possibles.
4. [[Systèmes distribués - Fiabilité et reprise]] — limiter une panne, observer et restaurer.

Prérequis utiles : [[Index - Fondations]], notamment [[Concurrence - Consistance et isolation]] et [[Contrats API - HTTP, idempotence et pagination]].
