# React — état, rendu, reducer et contexte

**Définition.** Le rendu calcule l'interface depuis les props et l'état ; une donnée calculable à partir d'un état existant est *dérivée*, pas un deuxième état à synchroniser. Un reducer décrit des transitions pures ; le provider conserve les effets et partage état/actions par contexte.

**Dans le dépôt** `/home/max/react-avance` : `src/main.tsx` monte `App` sous `StrictMode`. `src/model.ts` définit `Project` et `Task`. Dans `src/App.tsx`, `visible` est filtré à partir de `projects` et de `deferredQuery`, et `project` est retrouvé par `selected`. Dans `src/ProjectDetail.tsx`, le nombre terminé est `project.tasks.filter(t => t.done).length`. `src/store.tsx` sépare `reducer` (`loading`, `loaded`, `error`, `optimistic`, `saved`, `rollback`) de `ProjectsProvider`, qui lit/écrit via `src/api.ts` ; `useProjects()` échoue explicitement hors provider. Voir `docs/01-rendu-et-etat.md`.

**Identité et navigation.** `src/App.tsx` lit `?projet=…`, écrit l'URL avec `history.pushState` puis met à jour `selected` ; `popstate` relit l'URL pour précédent/suivant. `key={project.id}` sur le détail réinitialise son formulaire quand le projet change. `pushState` seul ne déclenche pas `popstate`.

**Piège.** Copier le total des tâches dans `useState` crée deux sources de vérité. Muter l'ancien tableau en place rend aussi le snapshot de rollback inutilisable. Une ref conserve une valeur sans déclencher de rendu ; elle ne remplace pas l'état affiché. Les types `Project` ne prouvent pas la forme du JSON stocké.

**Mini-exercice.** Ajouter sur papier un filtre « au moins une tâche ouverte » à `visible` avec `p.tasks.some(t => !t.done)`, sans effet ni nouvel état. Que donne un projet sans tâche ?

Suite : [[React - effets asynchrone annulation]] · [[React - formulaires accessibilite]] · [[React avance]].
