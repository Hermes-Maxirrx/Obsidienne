# Obsidienne — cours de conception et fiches de projets

Commencer par [`Accueil.md`](Accueil.md). Le coffre organise des **notions informatiques concrètes**, un [cours UML détaillé](Cours/06-UML/UML%20-%20index.md), les **23 design patterns classiques** et un dossier conservé de **huit projets expliqués**. Les exemples théoriques ne sont pas des preuves d'exécution ; les fiches projets distinguent simulations, corrigés reconstruits et réalisations vérifiées.

## Installer

Le dépôt est privé : un accès à l'organisation `Hermes-Maxirrx` est requis.

```bash
git clone git@github.com:Hermes-Maxirrx/Obsidienne.git
```

Dans Obsidian : **Ouvrir un dossier comme coffre** → sélectionner `Obsidienne` → ouvrir `Accueil.md`. Le dossier `.obsidian/` est ignoré par Git pour laisser chaque ordinateur gérer ses préférences.

## Récupérer les mises à jour

Depuis le clone, sur `main` :

```bash
git pull --ff-only origin main
```

Le téléchargement est **manuel**, pas automatique à l'ouverture d'Obsidian. En cas de modifications locales, sauvegarde-les d'abord sur une branche ; `--ff-only` refuse un historique divergent plutôt que d'écraser ton travail.

Les chemins `/home/max/...` dans les fiches projets correspondent au serveur d'origine ; sur une autre machine, consulte ou clone les dépôts concernés. Les liens GitHub privés exigent une autorisation. Les diagrammes sont écrits en Mermaid dans les notes Markdown ; les cours restent lisibles en texte si l'affichage d'un schéma échoue.

## Contribuer

Créer une branche depuis `main`, modifier les leçons, vérifier les wikilinks et les exemples, ouvrir une PR. Après fusion, les autres copies reçoivent la mise à jour au prochain `git pull --ff-only origin main`.
