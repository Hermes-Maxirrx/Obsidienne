# React — effets asynchrones, annulation et optimisme

**Définition.** Un effet synchronise avec une ressource extérieure au rendu. Son nettoyage peut rendre obsolète un chargement ; une mutation optimiste affiche immédiatement le prochain état puis confirme ou restaure le snapshot en cas d'échec.

**Dans le dépôt** `/home/max/react-avance` : `src/api.ts` simule une frontière asynchrone locale avec un délai de 300 ms, `readProjects`/`saveProjects` et la clé `atelier-projets-v1` dans `localStorage` ; aucun HTTP. `loadProjects(signal, fail)` attend via un timer annulable, lit le stockage ou remplace les données invalides par le jeu fictif avec avertissement. `src/store.tsx` crée un `AbortController` dans l'effet dépendant de `request`, annule au nettoyage et vérifie `signal.aborted` avant `dispatch`. Voir `docs/02-asynchrone-et-optimisme.md`.

**Chronologie.** `update` refuse si `busy.current` ou statut différent de `ready`, prend `before = state.projects`, émet `optimistic`, attend `saveProjects`, puis `saved` ou `rollback(before)`. Le verrou ref est synchrone entre deux événements précédant un rendu ; `saving` informe l'UI et désactive les contrôles. Une requête refusée n'est pas mise en file. `src/ProjectDetail.tsx` garde le titre saisi après un échec de création.

**Piège.** `StrictMode` peut installer/nettoyer/réinstaller un effet en développement : ne pas en déduire deux appels garantis en production. L'annulation du chargement local ne signifie pas annulation d'une écriture distante ; la sauvegarde ici n'est pas annulable. Le verrou ne couvre ni plusieurs onglets ni des conflits serveur. Une lecture corrompue n'efface pas immédiatement le stockage, mais une écriture réussie ultérieure peut l'écraser.

**Mini-exercice.** Dans l'UI, cocher « Simuler un échec de sauvegarde », modifier une tâche et suivre état optimiste, alerte, rollback ; repérer `before` et `finally` dans `src/store.tsx`.

Voir aussi [[React - etat rendu reducer contexte]] · [[React - performance Suspense erreurs]] · [[React avance]].
