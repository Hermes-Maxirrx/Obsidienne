# Obsidienne — coffre de révision

Ouvrir [`Accueil.md`](Accueil.md) pour choisir un parcours, rechercher une notion ou retrouver l'un des huit projets. Les 50 fiches de départ sont des supports de révision : elles renvoient aux dépôts et aux vrais fichiers, sans recopier de secrets ni prétendre à des déploiements.

## Installer sur un autre ordinateur

Accès à l'organisation GitHub `Hermes-Maxirrx` nécessaire : ce dépôt est privé.

```bash
git clone git@github.com:Hermes-Maxirrx/Obsidienne.git
```

Dans Obsidian : **Ouvrir un dossier comme coffre** → sélectionner le dossier `Obsidienne` cloné → ouvrir `Accueil.md`. Obsidian créera ses réglages locaux dans `.obsidian/`, qui ne sont pas versionnés.

## Recevoir les nouveaux cours

Dans le dossier cloné, sur la branche `main` :

```bash
git pull --ff-only origin main
```

Le `pull` est **manuel** : il ne se déclenche ni à l'ouverture d'Obsidian ni automatiquement quand une note est ajoutée sur GitHub. Si tu modifies des notes localement, sauvegarde d'abord tes changements sur une branche et propose une PR ; un `pull --ff-only` peut refuser de continuer si les fichiers locaux sont en conflit.

Les chemins `/home/max/...` mentionnés dans certaines fiches correspondent **au serveur d'origine**. Sur une autre machine, ouvre les dépôts liés sur GitHub ou clone-les à ton tour ; les chemins locaux ne sont pas portables. Les liens directs vers les fichiers CloudShop ouvrent le dépôt GitHub privé et nécessitent un compte autorisé.

## Actualiser le coffre côté auteur

Créer une branche thématique depuis `main`, modifier les notes, vérifier les wikilinks et les références, puis ouvrir une PR vers `main`. Après fusion, les autres copies du coffre reçoivent les changements au prochain `git pull --ff-only origin main`.
