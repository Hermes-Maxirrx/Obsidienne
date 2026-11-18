# React — priorité, Suspense, erreurs et tests

**Définition.** Une transition indique à React qu'une mise à jour peut être moins urgente ; `useDeferredValue` peut différer une valeur utilisée par un calcul. `lazy` charge un module à la demande et `Suspense` fournit un repli pendant son attente. Une frontière d'erreur de rendu ne remplace pas la gestion des promesses rejetées.

**Dans le dépôt** `/home/max/react-avance` : `src/App.tsx` utilise `useDeferredValue(query)` pour filtrer et `startTransition` pour sélectionner. Le détail est `lazy(() => import('./ProjectDetail'))` sous `Suspense` et `ErrorBoundary` ; `src/ErrorBoundary.tsx` gère l'erreur de rendu du descendant. `src/store.tsx` traite séparément les échecs asynchrones de lecture et sauvegarde. Voir `docs/04-performance-tests.md`.

**Tests et limites.** `src/App.test.tsx` couvre chargement, filtre, validation, rollback, stockage corrompu, annulation et boundary ; `src/validation.test.ts` couvre le titre vide ; `src/test-setup.ts` vide le stockage entre tests. Les tests rendent `App` directement, sans l'enveloppe `StrictMode` de `src/main.tsx`. Lire également `src/selection.test.tsx` pour le paramètre URL vide et le changement de projet après erreur de rendu ; ce test ne couvre pas explicitement précédent/suivant.

**Piège.** Sur trois projets fictifs, aucune accélération sensible n'est promise par une transition. `Suspense` ici concerne le module différé, pas la promesse de sauvegarde ; `ErrorBoundary` ne capture pas les rejets d'un événement asynchrone. Un test DOM vert ne mesure ni performances navigateur ni accessibilité complète. Pas de SSR/hydratation dans cet atelier.

**Mini-exercice.** Localiser les trois messages distincts « recherche en cours », « Ouverture… » et « Chargement du détail… » dans `src/App.tsx`, puis expliquer pour chacun quelle attente il représente et pourquoi sa visibilité n'est pas garantie.

Voir [[React - etat rendu reducer contexte]] · [[React - effets asynchrone annulation]] · [[React avance]].
