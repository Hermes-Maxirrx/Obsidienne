# React avancé — atelier de projets locaux

**Sujet.** Application pédagogique React/TypeScript : trois projets fictifs, recherche, détail et tâches cochables/ajoutables. Les données résident dans `localStorage` du navigateur ; `src/api.ts` simule délai et pannes, mais **aucune API HTTP, compte, synchronisation ou serveur métier** n'existe. Source : `/home/max/react-avance/README.md` et `src/api.ts`.

## Ordre de lecture dans `/home/max/react-avance`

1. `README.md` → portée et parcours ; `src/model.ts` → types et données fictives.
2. `docs/01-rendu-et-etat.md` → `src/main.tsx` → `src/App.tsx` → `src/store.tsx` : état dérivé, reducer, contexte, URL `?projet=`. Concept : [[React - etat rendu reducer contexte]].
3. `docs/02-asynchrone-et-optimisme.md` → `src/api.ts` → `src/store.tsx` → `src/ProjectDetail.tsx` : annulation du chargement, snapshot, sauvegarde optimiste et rollback. Concept : [[React - effets asynchrone annulation]].
4. `docs/03-formulaires-accessibilite.md` → `src/ProjectDetail.tsx` → `src/validation.ts` → `src/App.test.tsx` : titre, focus et message d'erreur. Concept : [[React - formulaires accessibilite]].
5. `docs/04-performance-tests.md` → `src/App.tsx` → `src/ErrorBoundary.tsx` → `src/App.test.tsx` → `src/selection.test.tsx` → `src/validation.test.ts` : priorités, Suspense, boundary, URL invalide et limites des tests. Concept : [[React - performance Suspense erreurs]].

**Exemple à tracer.** Cocher une tâche : `ProjectDetail` construit une nouvelle liste, `store.update` affiche le changement, `api.saveProjects` écrit localement ou échoue, `rollback` rétablit le snapshot. Avec la simulation d'échec, l'état est restauré ; ce n'est pas un test de réseau.

**Piège.** Le nom `api.ts` ne signifie pas backend. Le stockage peut être modifié par les scripts de l'origine et les onglets peuvent s'écraser. L'existence de tests DOM simulés ne certifie pas l'accessibilité réelle ou la performance.

**Mini-exercice.** Suivre dans les fichiers ci-dessus un `localStorage` corrompu (`{`) : quel avertissement apparaît, est-il effacé dès la lecture, et qu'advient-il après une sauvegarde réussie ?
