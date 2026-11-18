# React — formulaires et accessibilité

**Définition.** Une entrée contrôlée reflète l'état React par `value` et `onChange`. La validation décide si la mutation peut commencer ; l'accessibilité relie champ, libellé, erreur et focus, sans remplacer les essais avec technologies d'assistance.

**Dans le dépôt** `/home/max/react-avance` : `src/ProjectDetail.tsx` conserve `title`, `error`, `fail`. `onSubmit` appelle `preventDefault`, puis `validateTitle(title)` dans `src/validation.ts` ; si le titre est vide après `trim`, `input.current?.focus()` replace le focus. Sinon l'événement crée un identifiant avec `crypto.randomUUID()`, reconstruit immuablement les projets et appelle `update`. Le champ n'est vidé qu'après succès. Le formulaire porte `noValidate` : la validation montrée vient de l'application. Lire `docs/03-formulaires-accessibilite.md` et `src/App.test.tsx`.

**Associations.** `<label htmlFor="task-title">` nomme le champ ; `aria-invalid` et `aria-describedby="task-error"` relient son état au message `role="alert"`. Les messages d'attente ont `role="status"`. Le texte des tâches est rendu dans un `<span>` ; un test vérifie que la chaîne `<img src=x>` s'affiche comme texte et ne crée pas d'image dans ce chemin.

**Piège.** `maxLength={120}` limite la saisie dans ce champ, mais `validateTitle` ne valide actuellement que le vide : ce n'est pas une règle métier de longueur générale. `aria-invalid` signale l'erreur, il ne valide rien. Les tests DOM simulés ne garantissent pas le comportement d'un lecteur d'écran, du clavier et du contraste en conditions réelles. `localStorage` n'est pas un coffre à secrets.

**Mini-exercice.** Ajouter une validation des titres de plus de 80 caractères après `trim`, tester 80 et 81 caractères dans `src/validation.test.ts`, et vérifier que l'erreur reste liée au champ et que le focus y retourne.

Voir [[React - effets asynchrone annulation]] · [[React - performance Suspense erreurs]] · [[React avance]].
