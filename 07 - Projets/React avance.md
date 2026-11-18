# React avancé — état et asynchrone locaux

**Sujet.** `/home/max/react-avance` est une application de démonstration React et TypeScript : trois projets fictifs, une liste filtrable, des détails et des tâches à ajouter ou cocher. Les modifications restent dans le `localStorage` du navigateur. Malgré son nom, le module `api.ts` ne contacte aucun serveur : délais et pannes y sont simulés. Il n'y a ni compte, ni API HTTP, ni synchronisation entre appareils.

**Conception.** Un modèle typé décrit projets et tâches ; leur progression est calculée à partir des tâches au lieu d'être dupliquée dans un état potentiellement contradictoire. Un reducer rend les transitions de chargement, sauvegarde et erreur explicites. Le chargement est annulable pour éviter qu'une ancienne réponse remplace une sélection plus récente ; une modification optimiste affiche immédiatement l'effet puis restaure un instantané si la sauvegarde échoue. À l'entrée du stockage, une vérification de structure à l'exécution complète les types TypeScript, lesquels disparaissent après compilation. Le formulaire garde le focus et associe le message d'erreur au champ.

**Ordre de lecture** (depuis la racine du dépôt) :
1. `README.md` — démonstration et limites de validation.
2. `src/model.ts` — modèle et données de départ.
3. `src/api.ts` — stockage local et frontière non fiable.
4. `src/store.tsx` — effets, reducer et rollback.
5. `src/ProjectDetail.tsx` — interaction et formulaire.

**Transfert et limites.** Relier [[React - rendu état effets et asynchrone]], [[TypeScript - types et frontieres runtime]], [[UI - accessibilite et performance]] et [[Tests - pyramide et TDD]]. L'optimisme ne démontre pas une gestion des conflits réseau ; plusieurs onglets peuvent écraser leurs données. Des tests sur DOM simulé ne remplacent pas une vérification complète au clavier et avec lecteurs d'écran.

**Question de révision.** Pourquoi valider les données lues de `localStorage` si `Project` possède déjà un type TypeScript ?
