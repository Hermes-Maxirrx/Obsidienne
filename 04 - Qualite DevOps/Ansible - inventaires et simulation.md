# Ansible — inventaires et simulation

**Définition.** Un inventaire désigne les cibles/groupes ; `group_vars` fournit les paramètres du groupe ; un playbook ordonne les tâches. **Pourquoi :** séparer une procédure de ses paramètres et refuser une cible imprévue avant toute action. Ici, « déploiement » signifie uniquement **simulation locale**.

**Exemple réel.** `ansible/inventory/dev.ini`, `ansible/inventory/test.ini` et `ansible/inventory/prod.ini` déclarent chacun `localhost ansible_connection=local` ; `ansible/group_vars/dev.yml`, `test.yml`, `prod.yml` changent les valeurs théoriques. `prod.yml` garde `maintenance_mode: true` et `app_port: 80` **sans ouvrir ce port**. Dans `ansible/playbook.yml`, la première tâche `fail` refuse la maintenance ; viennent ensuite l'assertion de cible locale, l'assertion optionnelle de présence du token avec `no_log`, puis deux `debug` non sensibles. Aucun SSH, module de service/Docker ou conteneur lancé. `scripts/verify-ansible.py` vérifie les syntaxes et les acceptations/refus attendus ; un code de sortie global zéro signifie que **le refus prod attendu** a été constaté, non que prod a été livrée.

**Piège.** Mettre `maintenance_mode: false` pour obtenir du vert détruirait le garde-fou. La présence d'une variable `DEPLOY_TOKEN` ne prouve pas ses droits ; ne jamais la mettre dans un argument, un log ou la note. Ne pas transformer ces exemples en action sur une machine distante.

**Questions**
1. Pourquoi prod échoue-t-elle avant l'assertion du token ?
2. Que prouvent les messages `SIMULATION seulement` et que ne prouvent-ils pas ?

À relier : [[Conteneurs - image Docker et reseau]], [[Securite - secret scan SCA SAST DAST]], [[CI CD DevSecOps]]. Sources locales : `ansible/playbook.yml`, `ansible/inventory/`, `ansible/group_vars/`, `docs/04-docker-ansible.md`, `VERIFICATION.md`.
